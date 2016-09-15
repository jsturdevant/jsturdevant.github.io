# Overview

This is my blog that is hosted at http://jsturdevant.github.io.

# Setup on a new environment

A couple notes for getting this setup on a new environment.

Once it has been cloned from github, you will need to install the dependencies. Note that this will likely require that you use a newer version of ruby that is available with the OS. Recommend using RVM to accomplish this.

Reference http://rvm.io/rvm/install for up to date installation instructions.

```bash
gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3
\curl -sSL https://get.rvm.io | bash -s stable --ruby
```

This will take care of adding RVM to your profile and installing the latest ruby. Pay attention to the messages that come out of the install - it may tell you that you need to source a file before you can use RVM.

Next, let's tell our session to use a specific version of ruby. First, we need to figure out which version was installed.

```bash
rvm ls
```

It should result in something like this

```bash
$ rvm ls

rvm rubies

=* ruby-2.3.0 [ x86_64 ]

# => - current
# =* - current && default
#  * - default
```

Tell rvm to use the installed version

```bash
rvm use 2.3.0
```

If you have the system ruby installed, you can switch back by running the command

```bash
rvm use system
```

Ok, so now ruby should be in a good spot - we can install our dependencies. We will need the gem bundler to be able to do this. So first, run the following command.

```bash
gem install bundler
```

Then, within the base directory of this project, run the following command to install the needed gems

```bash
bundle install
```

It is possible that you may not have the build tools needed to install some of the gems (don't worry, it will give you an error message). Simply installing the `build-essential` apt package should get you everything you need.

Once everything is installed, you can now execute the site for testing

```bash
bundle exec jekyll serve
```

Or if you want to see drafts

```bash
bundle exec jekyll serve --drafts
```

If you want to make the website externally available, you can add the switch `--host 0.0.0.0`