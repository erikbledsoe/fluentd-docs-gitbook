# Plugin Management

This article explains how to manage Fluentd plugins, including adding
3rd party plugins.


## fluent-gem

The `fluent-gem` command is used to install Fluentd plugins. This is a
wrapper around the `gem` command.

``` {.CodeRay}
fluent-gem install fluent-plugin-grep
```
Ruby doesn\'t guarantee C extension API compatibility between its major
versions. If you update Fluentd\'s Ruby version, you should re-install
the plugins that depend on C extension.

### If Using td-agent, Use /usr/sbin/td-agent-gem

If you are using td-agent, please make sure to use td-agent's
`td-agent-gem` command. Otherwise (e.g. you use the command belonging to
system, rvm, etc.), you won't be able to find your "installed" plugins.

Please see [this
FAQ](/overview/faq.md/#i-installed-td-agent-and-want-to-add-custom-plugins-how-do-i-do-it)
for more information.

### Gem and native extension

Some plugins depend on natvie extension library. It means you need to
install development packages to build it, e.g. gcc, make, autoconf and
etc. If you see logs like below, install development packages before
plugin installation.

``` {.CodeRay}
Building native extensions.  This could take a while...
ERROR:  Error installing fluent-plugin-twitter:
        ERROR: Failed to build gem native extension.

    /opt/td-agent/embedded/bin/ruby extconf.rb

checking for rb_str_scrub()... yes
creating Makefile

make "DESTDIR = " clean
sh: 1: make: not found

make "DESTDIR = "
sh: 1: make: not found

make failed, exit code 127

Gem files will remain installed in /opt/td-agent/embedded/lib/ruby/gems/2.1.0/gems/string-scrub-0.0.3 for inspection.
Results logged to /opt/td-agent/embedded/lib/ruby/gems/2.1.0/extensions/x86_64-linux/2.1.0/string-scrub-0.0.3/gem_make.out
```

## "-p" option

Fluentd's `-p` option is used to add an extra plugin directory to the
load path. For example, if you put the `out_foo.rb` plugin into
`/path/to/plugin`, you can load the `out_foo.rb` plugin by specifying
the `-p` option as shown below.

``` {.CodeRay}
fluentd -p /path/to/plugin
```

You can specify the `-p` option more than once.

## Add a Plugin Via /etc/fluent/plugin

Fluentd adds the `/etc/fluent/plugin` directory to its load path by
default. Thus, any additional plugins that are placed in
`/etc/fluent/plugin` will be loaded automatically.

For example, if `/etc/fluent/plugin/out_foo.rb` exists, you can use
`@type foo` in `<match>`.

### If Using td-agent, Use /etc/td-agent/plugin

If you are using td-agent, Fluentd uses the `/etc/td-agent/plugin`
directory instead of `/etc/fluent/plugin`. Please put your plugins here
instead.

## Plugin version management

Fluentd and plugins are evolving, so you may hit unexpected error with
latest version, e.g. regression by new feature, removed deprecated
parameter,, change library dependency, etc. Avoiding these problems, we
recommend to fix fluentd and plugin version on production. If you want
to update fluentd or plugins, check the behaviour first on your test
environment. For example, td-agent fixes fluentd and plugins version in
each release.

Fluentd plugins are rubygems and rubygems installs latest version by
default. So we don't recommend to execute following commands on
production:

-   `gem install fluentd`
-   `gem install fluent-plugin-elasticsearch`
-   `gem update # This is very dangerous. Update all existing gems`

Another problem: if you install the plugin which depends on fluentd
v0.14, `gem` installs fluentd v0.14 together even if you installed
fluentd v0.12. This is unexpected result for fluentd v0.12 users.

You should specify target version with `-v` option.

-   `gem install fluentd -v 0.12.43`
-   `gem install fluent-plugin-elasticsearch -v 1.9.3`

`/usr/sbin/td-agent-gem` is also same because `/usr/sbin/td-agent-gem`
uses `gem` command internally.

## "--gemfile" option

A Ruby application manages gem dependencies using Gemfile and
[Bundler](http://bundler.io/). Fluentd's `--gemfile` option takes the
same approach, and is useful for managing plugin versions separated from
shared gems.

For example, if you have following Gemfile at /etc/fluent/Gemfile:

``` {.CodeRay}
source 'https://rubygems.org'

gem 'fluentd', '0.12.43'
gem 'fluent-plugin-elasticsearch', '1.9.3'
```

You can pass this Gemfile to Fluentd via the `--gemfile` option.

``` {.CodeRay}
fluentd --gemfile /etc/fluent/Gemfile
```

When specifying the `--gemfile` option, Fluentd will try to install the
listed gems using Bundler. Fluentd will only load listed gems separated
from shared gems, and will also prevent unexpected plugin updates.

In addition, if you update Fluentd's Ruby version, Bundler will
re-install the listed gems for the new Ruby version. This allows you to
avoid the C extension API compatibility problem.


------------------------------------------------------------------------

If this article is incorrect or outdated, or omits critical information, please [let us know](https://github.com/fluent/fluentd-docs/issues?state=open).
[Fluentd](http://www.fluentd.org/) is a open source project under [Cloud Native Computing Foundation (CNCF)](https://cncf.io/). All components are available under the Apache 2 License.