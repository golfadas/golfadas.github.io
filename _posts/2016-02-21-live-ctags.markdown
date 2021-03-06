---
layout: post
title:  "Always up to date Rails and coffeescript ctags"
date:   2016-02-21
categories: ctags bash
---
Ctags are awesome, and if you are using vim you should definitely use them in order to have a better source code navigation. The problem with rails is that, we need to generate tags for our ruby, coffescript scripts and used gems.

Gems? Why would you need to navigate into gems source code? Well, because you can, is easy and lets you dive into third party code. Did I say that its easy? Here is an example of entering tag aka `ctrl + ]` in a `has_many` active record relation:

![Entering a tag](/images/has_many_entertag.gif){: .blog-image }

Generating ctags for ruby scripts and used gems on rails projects is easy with:

`ctags -R --languages=ruby --exclude=.git --exclude=log . $(bundle list --paths)`

But, this doesn't generate tags for your coffescript files. In order to have ctags for coffeescript we need to use [Coffeetags](coffe-tags). After installed, generating coffescript tags is also easy with:

{% highlight bash %}
coffeetags -R
{% endhighlight %}

Any time a new method is added, the ctags file must be generated again. This might take a some time on large code bases, so in order to always have ctags available, I use the following [script][getagssh] to generate ctags into a temp folder, and then move it back to the project, replacing the old ctags file:

{% highlight bash %}
TAGS_TMP_FOLDER="/tmp/tmp_tags"
coffeetags -R -f $TAGS_TMP_FOLDER 
ctags -R --languages=ruby --exclude=.git --exclude=log -a -f $TAGS_TMP_FOLDER . $(bundle list --paths)
mv $TAGS_TMP_FOLDER tags
{% endhighlight %}

-a flag tells catgs to append the generated tags into the specified file.

Finally, [fswatch][fs-watch] can be used to automatically run the previous script whenever a file is saved, keeping ctags always up to date.

{% highlight bash %}
fswatch -o -r app lib spec | xargs -n1 sh gentags.sh
{% endhighlight %}

[coffee-tags]:  https://github.com/lukaszkorecki/CoffeeTags
[getagssh]:    https://gist.github.com/golfadas/71d7b5bf503619f466ac
[fs-watch]:  https://github.com/emcrisostomo/fswatch
