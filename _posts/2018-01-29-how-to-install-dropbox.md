---
layout: post
title: How to change the Dropbox default directory location
---

As stated in the [Dropbox documentation](https://www.dropbox.com/help/syncing-uploads/stuck-syncing), is not a good idea to use it over a mounted partition. This is a problem in case your `/home` doesn't reside physically on your host.
In this post I will show how to change the Dropbox default directory location. I did it on CentOS7 and I guess that can work on Fedora and also on Debian based OS (I'm assuming that you are using a 64bit device).  
## Choose the new destination
I choose to store my Dropbox files in the `/media` folder.
Prepare your new home executing those commands:

{% highlight bash %}
sudo mkdir -p /media/dropbox-youruser/
sudo chown youruser /media/dropbox-youruser/
sudo chmod 700 /media/dropbox-youruser/
{% endhighlight %}

## Install Dropbox 
Dropbox will be installed in the `/opt` directory 

{% highlight bash %}
cd /tmp/
curl -Lo dropbox-linux-x86_64.tar.gz https://www.dropbox.com/download?plat=lnx.x86_64
sudo mkdir -p /opt/dropbox
sudo tar xzfv dropbox-linux-x86_64.tar.gz --strip 1 -C /opt/dropbox
{% endhighlight %}
Now we need to run manually the dropbox deamon for the first time in order to authenticate it.

{% highlight bash %}
export HOME=/media/dropbox-youruser/; /opt/dropbox/dropboxd
{% endhighlight %}

Insert you Dropbox user and password in the browser, done!  
Now you know the trick, changing the env var `HOME` to our new location allow us to play Dropbox.

## Keep it syncing at each startup
That's not the end! Unless if you want to run manually the daemon at each startup!
Dropbox comes with the option:
  > ☐ Start Dropbox on System startup

But this will not fetch the proper env home
We will create instead a systemd deamon to keep it alive


{% highlight bash %}
sudo vim /etc/systemd/system/dropbox.service
{% endhighlight %}

{% highlight ini %}
[Unit]
Description=Dropbox daemon
After=syslog.target network.target sockets.target

[Service]
Restart=always
Environment=HOME=/media/dropbox-youruser/

ExecStart=/opt/dropbox/dropboxd

{% endhighlight %}

Save and quit then:

{% highlight bash %}
sudo systemctl daemon-reload
sudo systemctl start dropbox
{% endhighlight %}

Then run this command to configure the service to start when your device boots:

{% highlight bash %}
sudo systemctl enable dropbox
{% endhighlight %}



