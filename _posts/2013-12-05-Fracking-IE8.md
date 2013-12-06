---
layout: post
title: Fracking IE8 not inheriting background correctly.
category: Coding
tags: IE CSS
published: true
summary: The CSS background element does not properly inherit if you use PIE.htc.
image: post_three.jpg
name: moo
---
<p>
Just solved a problem which causes me to hate IE8 even more.  If you set a gradient on a section via css and <a href="http://css3pie.com/" title="PIE.htc">PIE.htc</a>
 and later on override it to be a solid color it doesn't work.
</p>
<!-- more -->

{% gist 7823815 frackingIE8-1.css %}

<p>
You would expect that the background would end up looking like this
</p>
<img src="/images/blue-gradient.png">
<p>
but no, not with IE8. It looks like this. Which might be a little hard to see but is a gentle gradient from almost white to a light grey
</p>
<img src="/images/grey-gradient.png">

<p>
Turns out the solution is to overwrite the full gradient settings like so.
</p>

{% gist 7823815 frackingIE8-2.css %}

<p>
There is probably an simpler way that overwriting the whole section but that works.
</p>
