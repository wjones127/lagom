---
layout: post
title: Introduction to Git and GitHub
categories:
- blog
---


Git and GitHub are two of the most important tools I use. This is probably true of most programmers. It's one of the more confusing and intimidating tools for beginner programmers, so I thought I might create a short guide for getting started.

I am, of course, greatly indebted to all the resources that I learn from them, and I have links to most of them, often as good sources for further reading.

There are some prereqs: I assume you know how to use a text editor, know the basics of using the command line, and have installed Git. You can find instructions on how to install git [here](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).

## What is Git?

Git is a version control software, that is primarily used for text files. Are you programming? Writing HTML? Git is there to help you manage the different versions.

Git goes beyond just simple backing up, and this is where it's true power comes in. A line of versions does not need to be linear, there can be branches splitting off and merging back.

Let's consider a simple example. You have a current version of your website, that works pretty well. But you are currently working on a great new feature: a giant cat will walk across the screen whenever a visitor clicks on your logo. Cool! Of course, making this will take a while, so you will have one version of the website on your server for the public to access. And you will have another version on your own computer you are working on, getting that feline working perfectly.

But, halfway through working on `awesome_cat.js`, your buddy emails to letting you know that you misspelled 'programmer' on your side. Whoops. You fix those, but now you have two different versions of your site, each with some new features.

This is where Git can be helpful. Those two different versions of your website (or any software project)—each with new updates of their own, called "branches" in Git—can be merged automagically!

## Try it yourself!

Let's walk through as simple example of how to use Git on your local machine.

### Configuring Git
Before we really get into how to use Git, there are a few things you might want to configure. First, it's a good idea to add a name and email address when you start Git. You can do this with the command `git config`:

{% highlight bash %}
git config --global user.name "John Doe"
git config --global user.email johndoe@example.com
{% endhighlight %}
Git also opens a text editor at times, so it will be useful to set up a text editor for Git. By default, Git uses the default text editor from your shell, which is usually vi. If you're not comfortable using vi, you can [change the default editor.](https://help.github.com/articles/associating-text-editors-with-git/)

For Emacs, use `git config --global core.editor emacs`.

For Sublime Text, type the following commands into the shell:
{% highlight bash %}
$ ln -s "/Applications/Sublime Text.app/Contents/SharedSupport/bin/subl" ~/bin/subl
$ git config --global core.editor "subl -n -w"
{% endhighlight %}

There are instructions for other editors in [the GitHub Help pages.](https://help.github.com/articles/associating-text-editors-with-git/)


### Initialize a Git Folder

First make a new folder where we will keep our new project.

{% highlight bash %}
cd ~/path/to/containing/folder/
mkdir intro-git
cd intro-git
{% endhighlight %}

Now we can initialize the directory as a git project by entering,

{% highlight bash %}
git init
{% endhighlight %}

### Our first commit

We create different versions of our project usually with commits, as in committing changes. For a commit, we need to create, delete, or change a file.

Let's start our project out by making a simple HTML file. Open your text editor of choice, and paste in the following:

{% highlight bash %}
<!DOCTYPE HTML>
<html>
  <head>
      <title>First Git Project</title>
        </head>
          <body>
              <h1>Hello world!</h1>
                </body>
                </html>
                {% endhighlight %}

Save in the folder `intro-git` as `index.html`. (You can open `index.html` now to find you have a webpage that says "Hello world!". Congrats!)

To make a commit using git you first need to make changes, and then stage those files you want to be in the commit. This using the `git add` command.

There are various ways to add files. One is to name them explicitly: if you have files `foo.html` and `bar.js`, you can add them by entering
{% highlight bash %}
git add foo.html bar.js
{% endhighlight %}
If you want to add all changed files in the directory, you can use
{% highlight bash %}
git add -A
{% endhighlight %}
(`git add .` is sometimes used. That adds all changes except files that are being deleted.)

One of my favorite commands to use is `git add -u`. This adds all files that have been previously staged, ignoring files that aren't already tracked. Useful if, like me, you sometimes keep files around your directories you don't actually want in your version control system.

For this situation where we have one file we want to add, we can use either `git add -A` or `git add index.html`. Do either.

You should have your new file staged. Not sure if it worked? Here's a useful command:

{% highlight bash %}
git status
{% endhighlight %}
This should give you a similar output to this:

{% highlight bash %}
On branch master

Initial commit

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)

new file:   index.html
{% endhighlight %}

Now to finish the commit type,

{% highlight bash %}
git commit -m "Created first version of website"
{% endhighlight %}

The `-m` options allows us to leave a message explaining what the commit was for. This is usually good for one line messages, but for complex commits it is polite to leave a longer message. If you just type `git commit`, it will open a text editor (by default Vim) for you to write the commit message. (You can change the default editor for your shell by changing the `EDITOR` setting in the shell config file or change the git default editor by typing `git config --global core.editor "editor"`, where `editor` is the command to open your editor of choice.)

Want some tips on writing good commit messages? [Chris Beams wrote a good piece on the subject.](http://chris.beams.io/posts/git-commit/)

### Creating and Managing Branches

For software projects we will often want multiple versions to be worked on at the same time. For a website, for example, you might need a stable version working on your web server, a version for the stuff you are working on, and another one for Mary who is refactoring that mess you call a stylesheet. In Git, we have different versions called branches.

We create new branches by starting off with a copy of another branch, and later merge the two using Git. Most often, Git can automagically merge two branches. (The main situation where Git cannot is where two different branch have changes on the same lines of a file. If people are dividing work up properly, this shouldn't happen.)

We can create a branch called `dev` and switch to it using the following command:
{% highlight bash %}
$ git checkout -b dev
{% endhighlight %}
You can always tell what branch you are on with `git status`.

Let's add a file really quickly and commit that.

{% highlight bash %}
$ touch fun.txt
$ git add fun.txt
$ git commit -m "Added a empty text file for fun."
{% endhighlight %}

Now we should have two different branches. If we switch to the other branch, there shouldn't be a `fun.txt`:
{% highlight bash %}
$ ls
index.html
fun.txt
$ git checkout master
$ ls
index.html
$ git checkout dev
{% endhighlight %}

We can merge these two branches simply. First, switch back to the branch you want to merge *into*, `master`:
{% highlight bash %}
$ git checkout master
{% endhighlight %}
Now we can merge by entering:
{% highlight bash %}
$ git merge dev
{% endhighlight %}

Now we should have `fun.txt` merged into master.
{% highlight bash %}
$ ls
hello_world.txt
fun.txt
{% endhighlight %}


### Ignoring Files

Sometimes you don't want to let Git track some files. Some of your data might be private, containing secret passcodes. You might also have a configuration file that only applied to your local setup. Remember that once you start working with GitHub soon, these files will often be public.

To have Git ignore files, we create a `.gitignore` file.

So let's create a file we want to ignore:
{% highlight bash %}
$ touch secret.txt
{% endhighlight %}
If you type `git status` it will show the file as untracked.

Now let's open a text editor. In `.gitignore` files we can ignore files, folders, and even types of files. Each line is something to be ignored. For example, to ignore `secret.txt`, a folder called `photos`, and all files ending with `.jpg`, our `.gitignore` file will look like this:
{% highlight bash %}
secret.txt
photos
*.jpg
{% endhighlight %}
The `*` acts as a wildcard, matching anything. You can read more about gitignore files here, but just know that they exist and are really important for keeping some things in your repo private or local.

The gitignore file, because its name starts with a period, is a hidden file, so you might have a little trouble opening it from finder.

The gitignore file is also tracked by Git, so be sure to commit your changes to it after saving it:
{% highlight bash %}
$ git add .gitignore
$ git commit -m "started gitignore"
{% endhighlight %}

### Summing Up Git

So these are the basics of how to use Git. We:

- Make changes to files and stage them with `git add <filename>`. (`git add -A` for all files and `git add -u` for already tracked files.)
- Commit our changes with `git commit -m "<commit message>"`
- Create new branches with `git checkout -b <branch name>`. Switch branches with `git checkout <branch name>`.

---

## GitHub

Okay, so we covered the basics of Git. Now let's look at GitHub.

GitHub is a website that integrates well with Git repositories and provides a platform for collaborating on software projects, complete with an issue tracker and a wiki. There are a lot of tiny cool features on GitHub, and I will point out a few as we go along.

We are  going to practice  a basic workflow using GitHub:

1. Writing an issue on GitHub describing a problem to fix or new feature to add.
2. Creating a branch to work on and making changes locally.
3. Pushing changes up to GitHub.
4. Making a pull request on GitHub.

This advantage of this workflow is it keeps the changes organized and well documented. The more people working on a software project, the more important this becomes.

### Make an Account

If you haven't already, I encourage you to make an account. Free accounts are available that have a few limitations, notably that all your repositories are public. You can get an account with 5 private repositories for $7.00 a month or free if you are a student and sign up for [the Student Developer Pack](https://education.github.com/pack). (You can sign up for that after you make your GitHub account.)

### Starting a Repository

There are a couple of ways of starting a repository. We already started one earlier, by using Git `init` and we can link that one to a GitHub repo if we want. We can also start a repo on GitHub and then clone a local copy.

Both start with creating a repository on GitHub.

On GitHub, click on the plus sign in the upper left hand corner and click on "New Repository". There are several options you can set:

- **Owner**: By default your account is the owner, but you can also set organizations to be the owner. Good if you want the repo to be part of a group project.
- **Name**: The name of the repository.
- **Description**: This is a short description that should help people identify what the repo is.
- **Public/Private**: Paid accounts have the option of have a private repo that only they can see.
- **Initialize with a README**: This starts the repo with a basic README file already there. Useful if we are starting our repo in GitHub, but don't do this if you've already started your repo with `git init`.
- **Add .gitignore**: There are a bunch of default gitignore files you can choose from, depending on what kind of project you are doing. (Fun fact: This list of gitignore files is itself [a GitHub repo you could contribute to!](https://github.com/github/gitignore))
- **Add license**: licenses help people know how they can use your work. [GitHub made a nice little site to help you choose which one](http://choosealicense.com/).

After choosing all those options you should have a new GitHub repo.

#### Method 1: Linking an Existing Git Repo to GitHub

So at this point, if you have a Git repo on your computer, we have a few steps more to link it to the GitHub repo.

1. *Set the remote origin*: We need to tell Git where your online repo is. First, copy the URL of your repo. (There is a button that copies it to your clipboard on your GitHub.) Then, in a shell with the working directory in your local git repo, enter `git remote add origin https://github.com/your-username/your-repo-name.git`.

2. *Pull down changes*: If you added a license, gitignore, or readme, you have changes in the online repo that aren't in your current repo. To merge these changes to your local copy, enter `git pull origin master`. In this command, `origin` specifies where you want to pull from and `master` specifies which branch.

3. *Push local changes*: Of course, you also have local changes that you need to push up. You can push those with `git push origin master`.

#### Method 2: Cloning a Repository

If you don't already have a local repository, you can create one really easily. Open a folder you want the repo to be in and type:
{% highlight bash %}
git clone https://github.com/username/repo-name.git
{% endhighlight %}

You can use this to clone any repo on GitHub.

### Creating a README

One of my favorite features of GitHub: It shows markdown files in HTML by default. What's markdown? It's a simply a language for writing simple text documents that is usually compiled into HTML. There are many flavors, and GitHub has it's own, which you can read about [here](https://help.github.com/articles/markdown-basics/) and [here](https://help.github.com/articles/github-flavored-markdown/). (The best features of GitHub markdown: emoji support!)

README files are the standard starting point for anyone looking at your repository; GitHub displays them when people go to your repositories home page. And they are almost always written in markdown.

Generally, READMEs give:

- a description of the project
- some simple getting started documentation
- instructions for how to start contributing to the project

### Writing an Issue

Issues are where changes are proposed. Often they will describe a problem and propose a potential solution. They can be discussed by users working on the project and even assigned to a particular user to address. When they are solved, issues can be closed.

Let's go ahead and make our first issue. At your GitHub repo, open the Issues tab and then click on the green "New Issue" button. You can just write a simple title to start, and then describe the issue further below (in markdown!). Let's make an issue complaining that our name isn't in the README.

![Screeenshot of GitHub issue: My name is not in the README.](/figs/github_issue_screenshort.png)

Once you create an issue, you and others can discuss it further here. The sidebar on the right gives more options for labeling the issue, milestones, notification and assigning the issue. We won't cover that right now, but you might consider checking out [GitHub's guide to issues](https://guides.github.com/features/issues/).

### Making and Pushing Up Local Changes

Alright, let's switch back to our local git repository. We want to solve this issue, but we might not want to edit the master branch directly. It's generally a bad idea to do that. Imagine if a really important bug comes up while you are working on the master branch. You need to solve it soon, but the current master branch is already stuck with a half written feature, which you don't want to delete, but you also can't push to production.

So let's save ourselves some headache and switch over to a new branch.

{% highlight bash %}
$ git checkout -b name
{% endhighlight %}
This creates a new branch called `name` and switches to it.

Now open README.md with a text editor and add your name to the file.

You can commit this change in just one command:
{% highlight bash %}
$ git commit -a -m "Added my name"
{% endhighlight %}
The `-a` flag automatically adds all modified and deleted files, but ignores new ones.

Now we can push this change up to GitHub.
{% highlight bash %}
$ git push origin name
{% endhighlight %}
(It might ask you for your username and password.)

### Making a Pull Request

Now that we have the changes on GitHub, we can make a pull request. On many repos that aren't yours, you won't be able to merge to master yourself (or it would at least be impolite.) Instead you can make a pull request, which is a proposal for some changes you made to be merged into a branch.

Go ahead and go to the Pull Requests tab in your GitHub repo and click on "New pull request."

You will have to choose the `base` and `compare` branches. The `base` branch is the one you want to merge into, and the `compare` branch is the one where changes are made. Here, your `base` should be the master branch and `compare` should be the name branch.

You might notice that it can automatically merge these two branches. While this is often the case, there are a few times where it is not.

Go ahead and click on "Create pull request."

This screen should look familiar! It's pretty similar to the issues page. But now we come with a solution not a problem. In larger software projects, you might use this space to explain your solution and have others examine your work.

At the bottom you should see all the changes made to the repository listed.

One of the most important things we can do is reference the issue we are solving. We can do that in the  title and body of the pull request description by typing `#` and the number of the issue we created. Our issue should be the first, so it should be `#1`. If you aren't sure, go ahead and open another tab and find the issue.

Even cooler, if we use a keyword like "fixes" or "resolve" before #1 in the description, it will automatically mark the issue as resolved! ([A full list of keywords is here.](https://help.github.com/articles/closing-issues-via-commit-messages/)) Go ahead and try that: write "Fixes #1" as you description.

Go ahead and click "Create Pull Request."

Normally we might have more discussion about changes, but because it's so small, we will go ahead and just merge the pull request. Click on "Merge pull request."

## Further Reading and Resources
This should be enough to get you started, but there's a lot more to still learn. Here are a few resources I recommend, some of which I've already mentioned above:

- Check out the [GitHub Cheat Sheet](https://github.com/tiimgreen/github-cheat-sheet)
- [How to Write a Git Commit Message](http://chris.beams.io/posts/git-commit/) by Chris Beams
- Learn how to harness the full power of Issues on GitHub from [GitHub's guide to Issues](https://guides.github.com/features/issues/).
- Learn how to merge branches that can't be automatically merged.
