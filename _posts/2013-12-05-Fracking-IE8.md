---
layout: post
title: Fracking IE8 not inheriting background correctly.
category: Coding
tags: IE CSS
published: true
summary: The CSS background element does not properly inherit if you use PIE.htc.
---
<p>
Just solved a problem which causes me to hate IE8 even more.  If you set a gradient on a section via css and <a href="http://css3pie.com/" title="PIE.htc">PIE.htc</a>
 and later on override it to be a solid color it doesn't work.
</p>
<!-- more -->

{% highlight css %}
.header-gradient {
    background: rgb(39,50,132);
    background: -moz-linear-gradient(top,rgba(0,0,0) 0%,rgba(0,0,0) 100%);
    background: -webkit-gradient(linear,left top,left bottom,color-stop(0%,rgba(0,0,0)),color-stop(100%,rgba(0,0,0)));
    background: -webkit-linear-gradient(top,rgba(0,0,0) 0%,rgba(0,0,0) 100%);
    background: -o-linear-gradient(top,rgba(0,0,0) 0%,rgba(0,0,0) 100%);
    background: -ms-linear-gradient(top,rgba(0,0,0) 0%,rgba(0,0,0) 100%);
    background: linear-gradient(top,rgba(0,0,0) 0%,rgba(0,0,0) 100%);
    -pie-background: linear-gradient(rgba(0,0,0), rgba(0,0,0));
    behavior: url(/assets/css/PIE.htc);
}

.header-gradient {
    background: rgb(39,50,132);
}
{% endhighlight %}

{% gist 7823815 frackingIE8-1.css %}

<p>
You would expect that the background would end up looking like this
</p>
<img src="/images/blue-gradient.png">
<p>
but no, not with IE8. It looks like this.
</p>
<img src="/images/grey-gradient.png">

<p>
Turns out the solution is to overwrite the full gradient settings like so.
</p>

{% highlight css %}
.header-gradient {
    background: rgb(39,50,132);
    background: -moz-linear-gradient(top,rgba(39,50,132) 0%,rgba(39,50,132) 100%);
    background: -webkit-gradient(linear,left top,left bottom,color-stop(0%,rgba(39,50,132)),color-stop(100%,rgba(39,50,132)));
    background: -webkit-linear-gradient(top,rgba(39,50,132) 0%,rgba(39,50,132) 100%);
    background: -o-linear-gradient(top,rgba(39,50,132) 0%,rgba(39,50,132) 100%);
    background: -ms-linear-gradient(top,rgba(39,50,132) 0%,rgba(39,50,132) 100%);
    background: linear-gradient(top,rgba(39,50,132) 0%,rgba(39,50,132) 100%);
    -pie-background: linear-gradient(rgba(39,50,132), rgba(39,50,132));
    behavior: url(/assets/css/PIE.htc);
}
{% endhighlight %}

<p>
There is probably an simpler way that overwriting the whole section but that works.
</p>
