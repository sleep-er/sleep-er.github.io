---
layout: post
title: Migrating Munin RRD files to a different machine
category: Linux
tags: Munin RRD
published: true
---
Have you tried moving your Munin rrd files to a new machine and ended up with this error

{% highlight bash %}
This RRD was created on another architecture
{% endhighlight %}

I have a solution and like other I have seen works recursively.
<!-- more -->

There are 2 stages to this.  Exporting your old rrd files to xml and then converting the xml back into the new rrd
format.  

## Stage 1 - Export the old rrd files into xml

cd into `/var/lib/munin` and create a file called `rrdexport.sh` and copy this into it

{% highlight bash %}
#!/usr/bin/env bash

foo () {
	for i in $1/*.rrd;do 
		rrdtool dump $i /tmp/rrd_dump/$i.xml;
	done
}
# delegate to subprogram
if [ "$1" = "-foo" ]; then
	shift 1
	foo "$@"
	exit $?
fi
find * -type d | xargs mkdir -p /tmp/rrd_dump
find * -type d | xargs -P 2 -I % $SHELL "$0" -foo %
{% endhighlight %}

It's a little rough and ready but it does the job.  Lets explain what it does.  

{% highlight bash %}
find * -type d | xargs mkdir -p /tmp/rrd_dump
{% endhighlight %}
First it finds all the directories from where you run it, I am assuming it will be run from 'var/lib/munin', 
then it creates a matching layout in `/tmp/rrd_dump`

{% highlight bash %}
find * -type d | xargs -P 2 -I % $SHELL "$0" -foo %
{% endhighlight %}
Secondly it finds all the directories and passes them into the function `foo`.  It uses `xargs -P 2` to use to threads 
at once in an attempt to speed it up a little.

{% highlight bash %}
foo () {
	for i in $1/*.rrd;do 
		rrdtool dump $i /tmp/rrd_dump/$i.xml;
	done
}
{% endhighlight %}
Function `foo` accepts a directory name as input.  We then find all `.rrd` files in the directory and 
using `rrdtool` export it as xml into `/tmp/rrd_dump/directoryname/filename.rrd`

## Stage 2 - Convert the exported xml into the new rrd format

I have skipped a stage here, the one where you copy the `/tmp/rrd_dump` directory onto your new machine.  I am sure you can do this without my help.  

Now we will do the same as before but in reverse. In your `rrd_dump` directory create a files named `rrdrestore.sh`
{% highlight bash %}
#!/usr/bin/env bash

restore () {
    for i in $1/*.xml;do 
	mkdir -p "/var/lib/munin/${i%.rrd.xml}";
        rrdtool restore "$i" "/var/lib/munin/${i%.xml}";
    done
}
# delegate to subprogram
if [ "$1" = "-restore" ]; then
    shift 1
    restore "$@"
    exit $?
fi
#find * -type d | xargs mkdir -p /var/lib/munin/
find * -type d | xargs -P 2 -I % $SHELL "$0" -restore %
{% endhighlight %}

You might have to tweak the file permissions once you have finished but all you historical data should
now have been transferred onto your new system.
