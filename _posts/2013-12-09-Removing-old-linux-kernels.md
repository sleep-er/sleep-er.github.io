---
layout: post
title: Removing old linux kernels in Ubuntu.
category: Linux
tags: Ubuntu
published: true
---
<p>
As I always forget how to remove the older kernel headers I am posting this here

{% highlight bash %}
sudo apt-get remove --purge $(dpkg -l 'linux-*' | sed '/^ii/!d;/'"$(uname -r | sed "s/\(.*\)-\([^0-9]\+\)/\1/")"'/d;s/^[^ ]* [^ ]* \([^ ]*\).*/\1/;/[0-9]/!d')
{% endhighlight %}

and a link back to the <a href="http://askubuntu.com/questions/2793/how-do-i-remove-or-hide-old-kernel-versions-to-clean-up-the-boot-menu">originating page</a>
</p>


