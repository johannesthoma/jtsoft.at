---
layout: page
title: ReactOS Console with virsh
permalink: /reactos-virsh-console/
nav_order: 21
parent: Howtos
---

# [](#header-1) Viewing the ReactOS console with virsh

ReactOS, by default logs to the serial port. If you have
installed ReactOS using virsh under Linux, here is how
to see the logs:

First find the domain name or the domain ID of your ReactOS
VM.

{% highlight bash %}
virsh list --all
{% endhighlight %}

It says something like

     Id    Name                           State
    ----------------------------------------------------
     -     lbtest-vm-120                  shut off
     -     ReactOS                        shut off

First you start ReactOS using

{% highlight bash %}
virsh start ReactOS
{% endhighlight %}

and select ReactOS(Debug) in the boot menu (this is the default).

Then you can see the console with the command:

{% highlight bash %}
virsh console ReactOS
{% endhighlight %}

After a few seconds, you should see ReactOS debug messages.

Note: This works with any OS that logs to a serial port. For
example, Linux can be configured to log to the serial port.
