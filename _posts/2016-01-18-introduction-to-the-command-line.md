---
layout: post
title: Introduction to the Command Line
categories:
- blog
---

To the uninitiated, the command line seems like an arcane relic from before we invented mice and graphical user interfaces (GUIs). But the reality the command line is a useful, efficient, and modern way to interact with the computer. There is plenty of software that is only or primarily available through the command line, such as Vim, Git, and Emacs.

Learning to use the command line unlocks this power. And this might surprise you at first, but I guarantee you will be able to do things much faster if you liberate yourself from the drudgery of pushing around the mouse.

I hope this introduction gets you using the command line. After going through this, you should:

- Understand how shell commands are structured
- Know how to look up command docs with `man`
- Know how to create and save aliases
- Know how to move around the file system

At the end, I have a list of further resources for you to check out that will expand what you can do with the command line.

Now, let's begin.

## Prerequisites

I am going to assume you are using some sort of Unix-based operating system (like Mac OS or Ubuntu). This will have some sort of terminal application, usually called 'Terminal.'

---

## The Shell

Go ahead and open up your terminal application. (On Mac OS, this is Terminal.) You will see the command prompt Looking something this:

{% highlight bash %}
$
{% endhighlight %}
You can type commands here, and press enter or return to run them. Let's try out a few basic ones. (I will use the following notation: lines starting with `$` are commands to enter, while lines without `$` are the responses from the computer.

{% highlight bash %}
$ whoami
willjones
$ pwd
/Users/willjones/
{% endhighlight %}

You've entered your first two shell commands! Congrats. Now what did those do? `whoami`, as you might guess, gives you your username on your computer. `pwd` gives you the current directory path you are on. By default, you are usually at your home folder.

## Learning More About a Command

Let's focus on just one command for now: `ls`. Go ahead and type `ls` into the prompt.

{% highlight bash %}
$ ls
 Applications
  Desktop
   Documents
    ...
    {% endhighlight %}
    This command lists the files and folders in the current directory you are in. Folders should be bolded.

### Flags

Commands often take what are called flags, which are ways of enabling options. For example, the `-l` option gives a long form listing of the directory contents, with additional information, including permissions on the file or folder. (Permissions is something we will cover later, but they are really important.)

{% highlight bash %}
$ ls -l
drwx------+  74 willjones  staff        2516 Jan  3 20:56 Desktop
drwx------+  56 willjones  staff        1904 Nov 19 21:24 Documents
...
{% endhighlight %}

You can use multiple options by using multiple letters. They can be combined or separate and order generally doesn't matter. For example, these are all equivalent:
{% highlight bash %}
$ ls -l -a
$ ls -a -l
$ ls -al
$ ls -la
{% endhighlight %}
(Note: Order does not matter, but capitalization does.) The `-a` flag shows hidden files, which are files with names that start with a `.`.


### Arguments

In addition to flags, many commands take or require arguments. For example, if you write a file path after the `ls` command, it will give you the contents of the directory specified by the path you gave, rather than the directory you are currently in. For example, I know there is a directory called "Desktop" in my current directory so I can type `ls Desktop` and get the contents of that folder.

There are other types of paths that can be taken by this command. We will come back to this soon.

### Man Pages
How do you learn about what flags and arguments a command takes? Man pages!

Type:
{% highlight bash %}
$ man ls
{% endhighlight %}

This will open up the manual for the `ls` command in the program `less`. You can use the arrow keys to move around and `q` to exit. Here are a few other useful commands:

<table>
<thead>
<tr><th>Key</th><th>Function</th></tr>
</thead>
<tbody>
<tr><td>q</td><td>Quit</td></tr>
<tr><td>CTRL + V</td><td>Go down one page</td></tr>
<tr><td>OPTION + V</td><td>Go up one page</td></tr>
<tr><td>Up arrow</td><td>Go up</td></tr>
<tr><td>Up arrow</td><td>Go down</td></tr>
</tbody>
</table>

## Paths and Moving Around

So by now you should know the basics of how commands are structured, with flags and arguments, as well as how to look up documentation of commands with `man`. Of course, it's hard to do much without knowing how to move around the file system, so let's go over that.

You ready know one important command: `ls`, which lists the contents of the current or given directory.

Another important command is `cd`, or change directory. To understand how to use `cd`, you need to know how to write file paths. There are three basic types of paths you can write:

- **Absolute paths**, which are relative to the root directory. They start with `/`. For example, my Desktop folder is at `/Users/willjones/Desktop`.
- **Path relative to home folder**, which start with `~/`. So for me, the `~` is essentially a stand in for `/Users/willjones`. So for my desktop folder, the path could be written as `~/Desktop`.
- **Path relative to current directory**, which don't start with a `/` or `~`. For example, if you are in a folder that contains a folder called `Documents`, you could move to that folder by entering `cd Documents`. You can also use the shortcut `..` to go to the parent directory.

<table>
<thead><tr><th>code</th><th>what it stands for</th></tr></thead>
<tbody>
<tr><td><code>~</code></td><td>Home directory</td></tr>
<tr><td><code>..</code></td><td>parent directory</td></tr>
<tr><td><code>~</code></td><td>Home directory</td></tr>
</tbody>
</table>

## A List of Commands

There are a lot of different shell commands. Here are some of the basic ones you shoud know to move around and manipulate the filesystem.

<table>
<thead><tr><th>code</th><th>what it stands for</th></tr></thead>
<tbody>
<tr><td><code>cd</code></td><td>change directory</td></tr>
<tr><td><code>ls</code></td><td>list directory contents</td></tr>
<tr><td><code>mkdir</code></td><td>creates a new directory</td></tr>
<tr><td><code>touch</code></td><td>creates a new empty text file</td></tr>
<tr><td><code>pwd</code></td><td>gives the current file path</td></tr>
<tr><td><code>rm</code></td><td>removes a file</td></tr>
<tr><td><code>rmdir</code></td><td>removes a directory</td></tr>
<tr><td><code>mv</code></td><td>moves a file, but can also be used to rename</td></tr>
<tr><td><code>cp</code></td><td>copies a file</td></tr>
<tr><td><code>less</code></td><td>opens up a text file viewer</td></tr>
<tr><td><code>more</code></td><td>prints the entire contents of a file</td></tr>
<tr><td><code>head</code></td><td>prints the first few lines of a file</td></tr>
<tr><td><code>tail</code></td><td>prints the last few lines of a file</td></tr>
</tbody>
</table>

Let's practice with these. Start by moving to a folder where we can do some work manipulating files, such as your documents folder:

{% highlight bash %}
$ cd ~/Documents
{% endhighlight %}
Now create a folder for us to work in.
{% highlight bash %}
$ mkdir intro_shell
$ ls
...
intro_shell
...
{% endhighlight %}
Go ahead and open up that new folder.
{% highlight bash %}
$ cd intro_shell
{% endhighlight %}
You can go up on directory using `..`. Try that now.
{% highlight bash %}
$ cd ..
$ pwd
~/Documents/
$ cd intro_shell/
{% endhighlight %}
Now create a text  file called `readme.md` using `touch`:
{% highlight bash %}
$ touch readme.md
{% endhighlight %}
Go ahead and open that file using a text editor. (On Macs you can use TextEdit if nothing else.) Add the text,
{% highlight bash %}
Hello world!
{% endhighlight %}
Save the file and return to your terminal emulator. You can look at this file in two ways: `less` and `more`. The former will open the file in the same program used for `man`. The latter will just print the contents on the file, which can be dangerous if the file is very long!

{% highlight bash %}
$ more readme.md
Hello world!
$ less readme.md
{% endhighlight %}
As mentioned before, you can use `q` to quit less.

Now let's make a new folder and put a copy of `readme.md` into it.

{% highlight bash %}
$ mkdir stuff
$ cp readme.md stuff/readme_copy.md
$ ls stuff/
readme_copy.md
$ more stuff/readme_copy.md
Hello world!
{% endhighlight %}
Let's rename that file:
{% highlight bash %}
$ mv stuff/readme_copy stuff/docs.md
{% endhighlight %}
On second though, maybe we don't want the `stuff` folder. We can delete it with:
{% highlight bash %}
$ mv stuff/docs.md ./
{% endhighlight %}
What did I do there? When moving a file, you can just end the new path with `/` and it will keep the old name. The `./` indicates the current directory. We could have done the same thing by typing `mv stuff/docs.md ./docs.md`.

Finally, let's get rid of that folder `stuff`:
{% highlight bash %}
$ rmdir stuff
{% endhighlight %}
*Note: `rm` and `rmdir` are permantent; unlike moving stuff to the trash or recycle bin, it's pretty difficult to get files back after removing them. So take care using these commands!*

---

## Aliases

On of the reasons we us the shell is that it has powerful tools that can save us time. On things that helps make using the shell are aliases. Go ahead and type `alias` into the shell.

Aliases are little shortcuts for commands. Programmer commonly will create aliases for longer commands they commonly do. On Macs, the alias
{% highlight bash %}
l='ls -lah'
{% endhighlight %}
is very common. I usually have aliases for things like restarting the web server Apache or starting a database server. For example, I have a command for starting PostGRES:
{% highlight bash %}
startpg='pg_ctl -D /usr/local/var/postgres -l /usr/local/var/postgres/server.log start'
{% endhighlight %}

So how do we make aliases? Well to start, you can quickly create an alias by typing:

{% highlight bash %}
$ alias command_name="commands_to_run"
{% endhighlight %}
Here's the problem though: this alias will only work for the current shell session. When you open a new shell window, it will not work in the new shell session.

To permanently add an alias, you need to add it to your shell's configuration file.

## Piping and Writing to Files

One last thing I will leave you with is how you can pipe the output of commands into other commands and write output to files. A lot of commands will just print their output, but you can actually redirect this output in interesting way.

For example, we can write the output to a file with `>`. Try this:
{% highlight bash %}
$ ls -al > file_list.txt
$ less file_list.txt
{% endhighlight %}
You should see the same output in the file as you would if you had typed `ls -al`. We can also just "pipe" the output to another command, like `less`, using `|`. For example,
{% highlight bash %}
$ ls -a | less
{% endhighlight %}

There are a lot of more interesting commands to use with this, such as the `sort` command, but it's worth knowing it's there when starting out.

## Further Resources

There is a lot more to learn from here, but what I've covered here should get you started. Here are some ideas for how to take these skills further:

- Learn to use a command line program [like Git](http://willjones127.com/blog/introduction-to-git-and-github/)
- Learn more about writing shell scripts
- Trick out your shell with a better terminal emulator and shell program
- Learn about more advanced techniques in shell scripts, like piping and grep
- Learn about owners, groups and permissions.
- Learn about controlling processes
