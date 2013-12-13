---
layout: post
title: Hosting your site on GitHub using Jekyll.
category: Coding
tags: GitHub jekyll
---

I kept seeing posts about Jekyll and how it would create static websites from plain text and you can even add in a little code to help out.
Even better some of them mentioned you can get GitHub to host the code, generate the html and display the site for you.  Free hosting and a chance
 to play around with an exciting piece of tech, count me in!
<!-- more -->

## So how does it work?

Quite simple really.  GitHub, in their kindness, allow you to serve pages from files in your repositories. Have a look [over here](http://pages.github.com/ "GitHub Pages")
to read more about this.  The only drawback is that the pages have to be static, I.E. you cannot generate them using PHP :(

## Is that it?

Not in my case.  I was not satisfied in a simple html site, I wanted more functionality, I wanted to be able to share snippets of html between pages.
How was I to do this?

One solution to this is [Jekyll](http://jekyllrb.com/ "Jekyll").  It takes a pile of html and markdown files and converts them into a fully functioning website.
No database backend needed either.  Excellent I'll use that to generate my html and submit that into GitHub.

Well you could do that or you could use another feature provided by GitHub and allow them to generate your site.  Even better, you just check in your Jekyll
files and GitHub will go off and generate the html for you.  This way you don't have to worry about old versions of renamed files hanging around in Git.

## How do I get started?

I will assume that you have Git install, have a GitHub account and have some form of text editor.  optionally you can also install Jekyll.  Install
the latest via ruby gems and not apt-get.

First off you will need to create a few files and directories

{% highlight bash %}
_config.yml
index.html
css/
_layouts/
_posts/
{% endhighlight %}

If you have installed Jekyll you can just run

{% highlight bash %}
jekyll new mynewsite
{% endhighlight %}

and they will be created for you.

Have a look at these files, tweak a few values and once you are happy push them back to GitHub.  Wait a minute or so and then view them.

## But I don't want to wait to view my changes

In that case, as long as you have install Jekyll, you don't have to.  Just run this command

{% highlight bash %}
jekyll serve --watch  --host 127.0.0.1 --port 8080 --drafts
{% endhighlight %}

With this command you have told it to:

`--watch` watch the code and generate new pages when it alters.

`--host 127.0.0.1` run a web server on this host.

`--post 8080` run a web server on this port number.

`--drafts` display any blog posts that might be in the _drafts folder. (slightly advanced feature)

You can now open your browser and go to [http://127.0.0.1:8080](http://127.0.0.1:8080). Any changes you make to your site will appear straight away.

### Do I need to install Jekyll?

The short answer to this is no.  The long answer is yes as it is very helpful.

If you don't have it installed you have to commit back to GitHub and wait for it to compile your pages.  If you have made a mistake you may not get a
site and even worse you will have no way of viewing the error.

## Wrapup

Using Jekyll and GitHub is really that simple.  Of course you could use something else to generate your code and then commit the static pages to GitHub
and display those but I like it this way.  I did run into a few problem along the way but I might save those for a further post.
