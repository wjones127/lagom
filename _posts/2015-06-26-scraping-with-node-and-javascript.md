---
layout: post
title: 'Lessons Learned: Scraping with Node and Javascript'
categories:
- blog
---

I just finished my first web scraping job, which was not a particularly easy one. The pages I need to scrape were not only password protected, but they also had the worst quality HTML markup I have ever seen. The typical HTML I needed to scrape on these pages looked something like this:

{% highlight html %}
<B>E-Mail: </B> <a href="mailto:watson@gmail.com">watson@gmail.com</a>
<B>Phone Number: </B>(111)123-1231 <B>Primary Contact</B> John Watson
<B>Description: </B> ...
{% endhighlight %}

Yuck! Little hierarchical structure, no ids or classes on these HTML elements. No, this would not be an simple job.

But after about a week of trial and error, I finally had a clean dataset to show for my efforts. I hope to share what I learned.

There are several components to the process I used, most of which was done in Javascript. (A little bit of data cleaning was done in R.) In the end there were four steps that went into this project, and I had written a separate Javascript module for each.

1. Scraping the pages of interest.
2. Handling HTTP Requests and _Authentication_.
3. Pulling 1 and 2 together to scrape and save the data into files.
4. Combining and cleaning the data files.

### Some general tips
Before I address the specific technical problems, there are a couple general lessons I learned about web scraping.

* First, __scrape only a part of the data at a time__. Unless you are really lucky, you are going to need to clean up your data after you scrape it. Instead of scraping everything and _then_ cleaning, scrape a portion of the data and then test and clean that data while the rest is coming in. This way you can write the cleaning script with some actual data in hand but before you finish all the scraping. That will save you a lot of time. 
* __Automate as much as you can__. Try to create a pipeline of scripts to extract and clean the data. This helped me out a lot. In the beginning I lost a lot of time because I was handling authentication by hand, requiring me to log in on the scrapers behalf at regular intervals. This also helps if you end up needing to redo part of the process. Toward the end of my project, I had all the scraped data ready and in files, but then my boss realized there were a few fields missing that she really wanted. Because everything was scripted, I just had to change a few things in my extraction script, and run the scraper scripts again overnight.

### Scraping the pages
If the HTML you are scraping is nice, you can take advantage of the hierarchical structure and extract things pretty easily. There are plenty of libraries designed for scraping nice HTML, but because I knew from the beginning they wouldn't be useful for my project, I haven't used any yet.

Instead, I resorted to using regular expressions. As someone who is not a regex master, this was definitely a learning experience. [regexr.com](www.regexr.com) helped me out immensely with this project. I recommend you check it out if you find yourself needing to use regular expressions.

It was pretty straightforward to get specific fields. For example, here is how I extracted the email field from the pages:

{% highlight javascript %}
exports.extract = function(html) {
  var reEmail = /<B>E-Mail: <\/B>[^<]*<[^>]+>([^<]+)/g;
  var result = {};
  var email = reEmail.exec(html);
  result['email'] = (email === null ? 'na' : email[1]);
  // Add other fields to result...
  return result;
}
{% endhighlight %}

However, the fields I was scraping were numerous but not consistently on the page. Some pages would have a telephone number, and others would only have email. Instead of creating a regular expression for each field, I created one that would match most of the fields. Conveniently, the fields had a consistent format: the field key was between bold tags and the value was after the bold tags but before the next bold tags started. Doing this made it really easy to get as many fields as possible without having to do a lot of research no the pages I was scraping to determine what fields existed.

### HTTP requests & authentication
The database I was looking at required me to login. (I should note I did have a valid log in; I wasn't stealing data from some place I wasn't supposed to be.)

There were two parts necessary to make the server accept my requests:

1. The `User-Agent` header had to be present and look like a browser (though I admit I didn't test the latter).
2. The session cookies from logging in needed to be present.

To make requests, I used the [request](https://www.npmjs.com/package/request) node library. You can set this up to have a cookie jar by using the following:

{% highlight javascript %}
var request = require('request').defaults({jar: true});
var j = request.jar();
{% endhighlight %}

Then any request made using the jar `j` will send and get cookies and save them in that jar.

To send a request with the jar and the `User-Agent` header:

{% highlight javascript %}
var headers = {'User-Agent': 'insert user agent here'}
var request_options = {url: url,
                       headers: headers,
                       jar: j};
request(request_options, callback);
{% endhighlight %}

A request will be sent to the `url`, and when the response comes back it will be handled by the function `callback`. That's just a placeholder name for this code, but in the actual code it is a function the scrapes the data from the response HTTP and saves it in an object.

The header was easy, but the login was a more difficult problem. My first approach to authentication was to login in to the website in the browser, copy my cookies into a JSON file using [EditThisCookie](https://chrome.google.com/webstore/detail/editthiscookie/fngmhnnpilhplaeedifhccceomclgfbg?hl=en), and then load those cookies into my scraper's jar.

{% highlight javascript %}
var fs = require('fs');
var cookies = fs.readFileSync('./cookies.json', 'utf-8');
cookies = JSON.parse(cookies);
for (var i = 0; i < cookies.length; i++) {
  var cookieString = cookies[i].name + '=' + cookies[i].value;
  var cookie = request.cookie(cookies[i].name + '=' + cookies[i].value);
  j.setCookie(cookie, cookie_url);
}
{% endhighlight %}

This worked, but required me to update the cookies file regularly. Even worse, if I had the scraper running for too long, the tail end of the data would all end up blank, because the scraper would just get the login page at that point. At this point I realized what I really needed to do was teach the scraper how to login.

Logging in isn't too hard. You just need to send a POST request with the login information to the right URL---usually the same url that gives you the login screen. Just make sure to check out the HTML of the login form so you know what the name of the POST parameters are. (They should be the `name` attributes on the fields.)

{% highlight javascript %}
function login(callback) {
  var auth = {
    username: 'your_username',
    password: 'your_password'
  }
  var login_url = 'www.website.com/login/page/';
  var options = {
    url: login_url,
    method: 'POST',
    jar: j,
    form: auth
  }
  request.post(options, callback);
}
{% endhighlight %}

After doing this, the scraper should have the proper cookies in the jar. To completely automate the login process though, I also needed to make sure the scraper would automatically login when it detected the session expired.

This required a more complex callback function to be passed to request. So I rewrote the request function:

{% highlight javascript %}
exports.request = function(url, callback) {
  var request_options = {url: url,
                         headers: headers,
                         jar: j};
  request(request_options,
          function(error, response, body) {
            return checkLogin(error, response, body, url, callback);
          });
};

function checkLogin(error, response, body, url, callback) {
  var reLoggedOut = /<h2>Log in:/g; // Distinguishing feature of login page
  if (reLoggedOut.exec(body) !== null)
    login(function() {exports.request(url, callback);});
  else
    callback(error, response, body);
}
{% endhighlight %}

Instead of the usual callback function, the request uses the `checkLogin` function as the callback function. The `checkLogin` function either calls the usual callback function, or, if it is not logged in, will use the `login` function before then making the request over again.

### Pulling it together

With the previous two modules in place, I could create a script to scrape the data and output some JSON file.

One thing I lucked out on was that URL scheme was straight forward. There was a page parameter `p` that I could just iterate through to get all the relevant pages.

This is not always possible. In fact, if I had decided I only wanted a subset of those pages, I would have had to come up with a different solution. One idea I started out with (before I knew that I could just manipulate the URLs) was crawling search results pages from a search of the site and scraping the URLs of the results.

Scraping the pages ended up being basically a for loop over the URLS while using my extract data function as the callback for the HTTP requests. After a few tries I learned it was better to do the scraping in chunks, so I made the script take parameters of the start and end indexes for the pages to scrape. When it saved a JSON file of the result, it included those indexes in the filename.

I also used a progress bar in my script, which was really helpful for figuring out if the scraper was working and how long it would be taking. For this I used the npm packages [progress](https://www.npmjs.com/package/progress), which I found easy to use. I would highly recommend it.

### Cleaning the data
After the data is scraped there probably is some cleaning that needs to happen. You could try to do this in the extract function, but if your in a situation like I was, you might not know ahead of time what kind of data you might get in each of the fields.

For example, in the `budget` field I also got some extra text included. It was much easier to clean this up afterward than try to anticipate this ahead of time.

I used R for this, as I am pretty familiar with data manipulation in that language. 

### Conclusions
This project really was a good exercise. Fundamentally I think I've learned that a good data scraper creates a pipeline fast and makes as much of it as possible automated. Actually running these scripts is so time intensive that it pays well to make them highly reusable. 
