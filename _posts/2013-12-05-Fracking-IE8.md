---
layout: post
title: Fracking IE8 not inheriting background correctly.
category: Coding
tags: IE PIE
published: true
summary: The CSS background element does not properly inherit if you use PIE.htc.
image: post_three.jpg
---
<p>
Just solved a problem which causes me to hate IE8 even more.  If you set a gradient on a div in css and then later on override it to just be a plain color
it doesn't work.
</p>
<!-- more -->

{% highlight html %}
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

.header-gradient {
    background: rgb(254,254,254);
}
{% endhighlight %}

<p>
You would expect that the background would end up looking like this
</p>

<p>
but no it looks like
</p>

<p>
Turns out the solution is to overwrite the full gradient settings like so.
</p>
{% highlight html %}
.header-gradient {
    background: rgb(254,254,254);
    background: -moz-linear-gradient(top,rgba(254,254,254,1) 0%,rgba(235,237,236,1) 100%);
    background: -webkit-gradient(linear,left top,left bottom,color-stop(0%,rgba(254,254,254,1)),color-stop(100%,rgba(235,237,236,1)));
    background: -webkit-linear-gradient(top,rgba(254,254,254,1) 0%,rgba(235,237,236,1) 100%);
    background: -o-linear-gradient(top,rgba(254,254,254,1) 0%,rgba(235,237,236,1) 100%);
    background: -ms-linear-gradient(top,rgba(254,254,254,1) 0%,rgba(235,237,236,1) 100%);
    background: linear-gradient(top,rgba(254,254,254,1) 0%,rgba(235,237,236,1) 100%);
    -pie-background: linear-gradient(#fefefe,#ebedec);
    behavior: url(/assets/css/PIE.htc);
}

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
</p>
