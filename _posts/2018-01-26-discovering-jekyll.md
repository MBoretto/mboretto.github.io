---
layout: post
title: Discovering Jekyll
---

I decided to give a chance to [Jekyll](https://jekyllrb.com/) after I realized that you can deploy it for free on [Github](https://pages.github.com/)!
This is also my <strong>first</strong> post on this blog, I would report here the procedure I did to start with Jekyll.
This will be useful when I will write the <strong>second</strong> post!

## The Jekyll environment 
I created a [Docker](http://docker.com) image to recreate the develompent environment every time.  
I know, there is already an Offcial [Jekyll Docker image](https://hub.docker.com/r/jekyll/jekyll/) but it's so heavy!  
I created a `Dockerfile` starting from [Alpine](https://alpinelinux.org/):

{% highlight docker %}
FROM alpine:3.7
LABEL maintainer="marco.bore@gmail.com"

RUN apk add --update curl vim \
    build-base \
    python \
    libxslt-dev libffi-dev \
    ruby-dev ruby-io-console ruby-irb ruby-json ruby-rake \
    && gem install --no-document jekyll bundle

EXPOSE 4000
{% endhighlight %}

The last line is the most important:

* allows you to connect to the container throught port `4000`.

## Build and run the container
When I have more that 3 docker options I usually create a `run.sh` file.

{% highlight bash %}
#!/bin/bash
echo 'Building container...'
docker build -t cnt-jekyll .
docker run -it --rm \
   --name=jekyll \
   -v `pwd`/file:/file \
   -w "/file" \
   -h jekyll \
   cnt-jekyll \
   /bin/sh
{% endhighlight %}

The most important parameter is:

* `-v` that mount the local `./file/` directory inside the container
You can now develop your Jekyll website in this directory without loosing your work in case the container stops.

## Serve Jekyll
{% highlight bash %}
bundle install
bundle exec jekyll serve --host 0.0.0.0 
{% endhighlight %}

Pleaset notice that if you don't specify the `--host` option, you will reach Jekyll just from the container inside. 

You can now reach Jekyll typing in the browser [jekyll.ip.inthe.dockerbridge:4000](http://172.17.0.4:4000)

## My personal impression

Pro:

* At every change the static website is rebuild
* Is easy to understart the content structure

Contra:

* Why I need to install python to use `pygments`?
* I took some time to get Jekyll up and running




