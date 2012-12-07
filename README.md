graphitefriction.github.com
===========================

My personal website and blog, baked with Awestruct and published at <http://graphitefriction.com>.

For instructions on how to install Awestruct and its dependencies, refer to the section *Install Awestruct* below.

## Preview the site locally

The following command will allow you to preview the site locally.

    awestruct -d

Visit: <http://localhost:4242>

If you are building the site on Linux and Awestruct fails to locate a JavaScript runtime, you can either:

1. install a node.js package or
2. set the following environment variable in your shell profile scripts (e.g., ~/.bash\_profile):

    export EXECJS_RUNTIME=SpiderMonkey

## Add and commit files

Add new files (such as a new blog entry or image):

    git add blog/YYYY-MM-DD-blog-title.textile

or all new files present in a directory (it only affects new files, skips files already committed)

    git add blog/

Commit all files marked to be committed, including new files that were just added:

    git commit -m 'your message here'

Commit all files that have changed, even if not marked to be committed:

    git commit -a -m 'your message here'

Make sure there are no files that are "untracked" or "modified":

    git status

You should see:

    nothing to commit (working directory clean)

Now you are ready to deploy.

## Deploy the site to github pages

The following commands will push changes (`git push`), clean build the site (`--force -g`) using the production profile (`-P production`), then deploy it to github pages (`--deploy`):

    git push
    awestruct -P production --force -g --deploy

Or simply run the prepared script:

    ./publish

## Planet Fedora feed

Here's the source feed containing all the entries:

<http://graphitefriction.com/feed.atom>

The feed for Planet Fedora is first filtered based on the tag `fedora` using feedrinse:

<http://www.feedrinse.com/services/rinse/?rinsedurl=7bf3eff3b2f7ca66cd59ed4887b2913d>

It's then plugged into feedburner to track visits and served up to Planet Fedora:

<http://feeds.feedburner.com/graphitefriction-fedora>

## Install Awestruct (on Fedora, user config)

Awestruct is a RubyGem, available in the main repository. I recommend installing it locally (rather than systemwide). Though I'm a huge fan of JRuby, the startup time impedes rapid (and comfortable) development, so I recommend using C Ruby for this purpose.

First, make sure that you have Ruby and RubyGems installed:

    sudo yum install ruby rubygems

You also need some development packages to install gems that use native extensions:

    sudo yum install ruby-devel libxml2 libxslt gcc-c++

Next, configure RubyGems to install gems in your home directory when using the command gem.

    cat > $HOME/.gemrc << CONTENT
    gemhome: $HOME/.gem/ruby/system
    gempath:
      - /usr/share/gems
    install: --no-rdoc --no-ri
    update: --no-rdoc --no-ri
    CONTENT

You'll need to run an additional set of commands to addresses an inconsistency in how Ruby sets the GEM PATH:

    mkdir -p ~/.gem/ruby/system
    cd ~/.gem/ruby
    ln -s system $(ruby -e "puts RbConfig::CONFIG['ruby_version']"
    cd .. 

Finally, make sure that the local gem bin directory is on your PATH:

    export PATH="$HOME/.gem/ruby/system/bin:$PATH"

At this point, you could install Awestruct directly, but since the site build has some additional dependencies, it's best to let bundler handle the installation. Bundler will also ensure that you are using the correct versions of each gem when you run Awestruct.

We'll need to get the bundler gem and the integration with RubyGems.

    gem install bundler rubygems-bundler

Now, inside the website project directory (i.e., this directory), use bundler to install the gems specified in Gemfile, including Awestruct.

    GEM_HOME=$HOME/.gem/ruby/system bundle install
    GEM_HOME=$HOME/.gem/ruby/system gem regenerate_binstubs

You're now Awestruct!
