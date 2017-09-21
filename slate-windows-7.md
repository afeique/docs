**Created**: 19 September 2017

# Intro to Slate

Slate is a tool written in Ruby for auto-generating HTML documentation from Markdown files. It is officially compatible with Linux and OS X. Compatibility with Windows is not officially supported.

See [Slate on Github](https://github.com/lord/slate).

The official documentation suggests using Slate with Git and Github, as Slate contains functionality to automatically push documentation to a personal Github page based on Git commit history. Using these features is **optional**: Slate works perfectly fine without Git and Github integration.

## Get Slate

Clone [the Github repository](https://github.com/lord/slate) to a location of your choosing. You can either use `git clone` or download the `.zip` and extract.

# Slate on Windows 7

Though Slate isn't officially supported on Windows 7, it is still possible to run it. Note that there may be unexpected behaviour or bugs.

## Node.js Setup

Node.js is a necessary dependency for JavaScript execution. Install the appropriate version using one of the [Node.js downloads](https://nodejs.org/en/download/). Make sure to select to add `node` and `npm` to the PATH.

## Ruby Setup

Install Ruby >= 2.4.1 using [RubyInstaller](https://rubyinstaller.org/downloads/). Make sure to select "Add Ruby executables to your PATH" during installation. 

## MSYS2 Toolkit Setup

The [MSYS2 Toolkit](http://www.msys2.org/) is required for compilation of C/C++ native extensions for Ruby. To get started, download the installer from the webpage and install to a location of your choosing.

Once the installation is finished, run the MSYS2 shell, `msys2.exe`.

In the shell, update the package database and core system packages using:

    $ pacman -Syu

When asked, close the shell or force-quit. Reopen the shell and finish updating:

    $ pacman -Su

Next, open up a command prompt. Install the RubyInstaller Development Kit (ridk) using `ridk install` and option `3`:

    >ridk install
     _____       _           _____           _        _ _         ___
    |  __ \     | |         |_   _|         | |      | | |       |__ \
    | |__) |   _| |__  _   _  | |  _ __  ___| |_ __ _| | | ___ _ __ ) |
    |  _  / | | | '_ \| | | | | | | '_ \/ __| __/ _` | | |/ _ \ '__/ /
    | | \ \ |_| | |_) | |_| |_| |_| | | \__ \ || (_| | | |  __/ | / /_
    |_|  \_\__,_|_.__/ \__, |_____|_| |_|___/\__\__,_|_|_|\___|_||____|
                        __/ |           _
                       |___/          _|_ _  __   | | o __  _| _     _
                                       | (_) |    |^| | | |(_|(_)\^/_>
       1 - MSYS2 base installation
       2 - MSYS2 repository update
       3 - MSYS2 and MINGW development toolchain
    Which components shall be installed? [1,2,3]

To test that the MSYS2 Toolkit and RubyInstaller Development Kit are working okay, install the `fast_blank` and `nokogiri` Ruby gems, which are dependencies for Slate. Installing these gems will exercise native compilation:

    >gem install fast_blank nokogiri

## Install `bundler` gem

The `bundler` gem is required to resolve and install Slate's other gem dependencies. Open up a command prompt and run:

    >gem install bundler

## Install Remaining Dependencies

Navigate to the directory you cloned Slate into.

### Change Gemfile

#### Set `nokogiri` to `~> 1.8.1`

As of this writing, the `nokogiri` version specified in Slate's `Gemfile` resolves to a version incompatible with Ruby 2.4.x _on Windows_ (this problem does not seem to exist in Linux). To fix this problem, open Slate's `Gemfile` and change the following line:

    gem 'nokogiri', '~> 1.6.8'

to:

    gem 'nokogiri', '~> 1.8.1'

#### (optional) Add `wdm >= 0.1.0`

When running the `middleman` server using `bundle exec` on Windows, the following message appears:

    == The Middleman is loading
      Please add the following to your Gemfile to avoid polling for changes:
        gem 'wdm', '>= 0.1.0' if Gem.win_platform?

To avoid this message, do as suggested and add the following line to the Slate `Gemfile`:

    gem 'wdm', '>= 0.1.0'

### `bundle` Install

Run:

    >bundle install

to finish installing dependencies.

## Use Slate

Simply edit `source/index.html.md` in the Slate directory. While in the top-level of the Slate directory, publish your changes using:

    $ bundle exec middleman build --clean

## Rendered Output

View `build/index.html`.

## (Optional) Run `middleman` Server

Slate uses the `middleman` server to offer a more convenient way to render and preview your changes without having to manually re-build. Instead, it is possible to make your changes to `index.md.html` and simply refresh the browser to view an updated render of your documentation. 

To start the `middleman` server, navigate to the Slate directory and run:

    $ bundle exec middleman server

This will start the server instance in the console window on port `4567`. Rendered Slate output can then be viewed at: `localhost:4567`.

Now, any time you make changes to `index.md.html`, simply refresh `localhost:4567` to view an updated render.
