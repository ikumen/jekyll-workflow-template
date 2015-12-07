## How it works

Locally I have an install of Jekyll and [this template](https://github.com/ikumen/jekyll-workflow-template). The local Jekyll installation allows me to preview my site. When I'm happy with the generated content, I check-in my raw Markdown files to Git, then push to a remote Git repository to permanently save. If I wish I can also push to a server, where a Git `post-receive` hook will use Jekyll (installed on the server) to build my site from the checked-in content, and publish to an `Nginx` server for the world to see.

If you're interested in giving the workflow a try, read on! 

## Server configuration

My installation is specific to Ubuntu, but the instructions should be very similar for other environments after the required packages are installed.

#### Requirements

You'll need to have [Ruby](https://www.ruby-lang.org/en/), Git, and Jekyll installed. For some reason I could not get Jekyll to work with the default Ruby installation on my [Ubuntu 14.04 server](http://releases.ubuntu.com/14.04/)&mdash;I had to use [Ruby 2.2 specifically](https://www.brightbox.com/blog/2015/01/05/ruby-2-2-0-packages-for-ubuntu/).

``` bash
# get Ruby 2.2 package and install 
$ sudo apt-add-repository ppa:brightbox/ruby-ng
$ sudo apt-get update
$ sudo apt-get install ruby2.2 ruby2.2-dev

# install Jekyll
$ gem install jekyll

# depending on you needs, you may need additional Jekyll plugins, 
# I needed the following three
$ gem install jekyll-sitemap
$ gem install pygments.rb   # for highlighting
$ gem install redcarpet     # markdown language
```

#### Git Hook Configuration

Next we'll need to set up a bare Git repository on the server to handle pushes from our local repository. In the bare repository, we add a `post-receive` hook that will take our pushed content, run it through Jekyll, generates the site and finally pushes the generate site to Nginx.

``` bash
$ mkdir /home/user/yourname/myblog.git
$ cd /home/user/yourname/myblog.git
$ git --bare init
```

Add the following to `post-receive` hook script to `/home/user/yourname/myblog.git/hooks`.

``` bash
# post-receive
# where the local git repo will be pushed
GIT_REPO=/home/user/yourname/myblog.git
# where your generated site will be served from
PUBLIC_WWW=/var/www/myblog 
TMP_GIT_CLONE=$GIT_REPO/tmp

# move the pushed content to tmp
git clone $GIT_REPO $TMP_GIT_CLONE
# generate the site off the content in tmp
jekyll build -s $TMP_GIT_CLONE -d $PUBLIC_WWW
# clean up our work
rm -Rf $TMP_GIT_CLONE
exit
```

That's it for the server configuration, it's ready now to receive pushes from our local Git repositories containing our Jekyll managed site.

## Local Configuration 

I won't go into the details of how to use Jekyll, but on a local workstation or laptop, you'll need to clone [this template](https://github.com/ikumen/jekyll-workflow-template) or set up a new site and initialize it as a Git managed repository.

``` bash
# clone this template
$ git clone git@github.com:ikumen/jekyll-workflow-template.git myblog

# -- OR -- 
# generate a new site under ./myblog
$ jekyll new myblog
# initialize myblog as a git repo
$ cd myblog
$ git init


# where I keep original copy of my blog
$ git remote add origin git@bitbucket.org:ikumen/myblog.git
# the server we configured above
$ git remote add deploy server:/home/user/yourname/myblog.git

# add ignores for the `_site` directory that Jekyll generates
$ echo _site >> .gitignore
```

That's basically it&mdash;if you're using Jekyll and Git, you probably have something very similar set up and would just need to add the `deploy` remote.

## Deployment

After I'm happy with a local post, I check it into Git and push to my server like so.

``` bash
# on my local computer
$ git add .... # posts that I just edited/added
$ git commit -am "new posts about Jekyll workflow"
$ git push origin master  # pushing to original copy of my blog
$ git push deploy master  # pushing to server configure above
```


