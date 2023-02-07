---
layout: page
title: Running a shell script as a Windows Service using cygwin
permalink: /cygwin-windows-service/
nav_order: 27
parent: Howtos
---

# [](#header-1)Running a shell script as a Windows Service using cygwin

Sometimes one wants to run a shell script or any other program
as a Windows Service. The Windows Service API is unfortunately
quite complex. Fortunately, with CygWin there exists a simple
solution with the ``cygrunsrv`` command: So assuming your script
is in the home directory of Administrator and called ``myservice.sh``
the command would be:

{% highlight bash %}
cygrunsrv -I demoservice -t manual -p /bin/bash -a '-c /home/Administrator/myservice.sh'
{% endhighlight %}

The ``-I`` parameter means install a service. The ``-t`` is the
start type, the ``-p`` is the path to the program (cygwin absolute
path) and the ``-a`` are the arguments to the program (in our
case instructing bash to run the ``myservice.sh`` script).

Your script may be something like:

{% highlight bash %}
LETTER=m
while true
do
    date >> /cygdrive/$LETTER/some-data
    sync
    sleep 1
done
{% endhighlight %}

I use it during development to test the DRBD reactor port I am
currently working on.

Note that now you can start and stop the service just like any
other service with the ``sc`` (or ``net``) ``start/stop``
commands:

{% highlight bash %}
sc start demoservice
sc query demoservice # (should display the service as running)
sc stop demoservice
{% endhighlight %}

To watch the file you can use ``tail -f`` as in:

{% highlight bash %}
tail -f /cygdrive/m/some-data
{% endhighlight %}

Note that you can specify any program for the ``-p`` parameter.
You can also redirect ``stdout`` and ``stderr`` of the program
to a file (with the ``-1`` and ``-2`` parameters). 

``cygrunsrv`` has many options, there is a comprehensive help
so I won't repeat it here: just do

{% highlight bash %}
cygrunsrv --help
{% endhighlight %}

to get started.

Best wishes, Johannes
