# civibuild

Creating a full development environment for CiviCRM requires a lot of work, e.g.

 * Downloading / installing / configuring a CMS (Drupal, Joomla, WordPress)
 * Downloading / installing / configuring CiviCRM
 * Configuring Apache and MySQL
 * Configuring file permissions on data folders
 * Configuring a headless test database for phpunit
 * Configuring Selenium to connect to Civi

The *civibuild* command automates this process. It includes different
build-types that are useful for core development, such as *drupal-clean* (a
barebones Drupal+Civi site) and *wp-demo* (a WordPress+Civi site with some
example content).

Note: There are a number of build tools on the market which can, e.g.,
create a Drupal web site (like [drush](http://drush.ws/)) or WordPress web
site (like [wp-cli](http://wp-cli.org/)).  Civibuild does not aim to replace
these.  Unfortunately, such tools generally require extra work for a Civi
developer environment.  Civibuild works with these tools and and fills
in missing parts.

## Your First Build {:#start}

!!! tip
    Login as a non-`root` user who has `sudo` permission. This will ensure that new files are owned by a regular user, and (if necessary) it enables `civibuild` to restart Apache or edit `/etc/hosts`.

The first build requires only a few commands.  However, these are also the
hardest commands -- you need to provide detailed information about the
Apache/MySQL/PHP systems, and you may need to try them a few times.

Configure `amp` with details of your Apache/MySQL environment.  Pay close
attention to the instructions.  They may involve adding a line to your
Apache configuration file.

```
$ amp config
```

Test that `amp` has full and correct information about Apache/MySQL.

```
$ amp test
```

!!! note
    You may need to alternately restart httpd, re-run `amp config`, and/or re-run `amp test` a few times.

Create a new build using Drupal and the CiviCRM `master` branch.
The command will print out URLs and credentials for accessing the website.

```
$ civibuild create dmaster --url http://dmaster.localhost --admin-pass s3cr3t
```

Once you have a working build of `dmaster`, you can continue working with `civibuild` to create different builds as described below.

## Build Types

`civibuild` includes a small library of build scripts for different
configurations.

For a list of available build-types as well as documentation on writing build scripts,
see `app/config` within your buildkit installation.

For example, at time of writing, it includes:

* `backdrop-clean`: A bare, "out-of-the-box" installation of Backdrop+CiviCRM
* `backdrop-demo`: A demo site running Backdrop and CiviCRM
* `backdrop-empty`: An empty Backdrop site (without CiviCRM). Useful for testing tarball installation.
* `drupal8-clean`: A bare, "Out of the box" Installation of Druapl8+CiviCRM.
* `druapl8-demo` : A demo site running Drupal8 and CiviCRM.
* `drupal-clean`: A bare, "out-of-the-box" installation of Drupal+CiviCRM.
* `drupal-demo`: A demo site running Drupal and CiviCRM.
* `drupal-empty`: An empty Drupal site (without CiviCRM). Useful for testing tarball installation.
* `joomla-empty`: An empty Joomla site (without CiviCRM). Useful for testing tarball installation.
* `wp-demo`: A demo site running WordPress and CiviCRM
* `wp-empty`: An empty WordPress site (without CiviCRM). Useful for testing tarball installation.
* `hrdemo` A demo site running Drupal, CiviCRM, and CiviHR
* `symfony`: An experimental hybrid site running Drupal 7, Symfony 2, and CiviCRM
* `cxnapp`: A self-signed CiviConnect app based on the reference implementation.
* `messages`: A backend service for delivering in-app messages (eg "Getting Started").
* `extdir`: A mock website akin to civicrm.org/extdir/ . Useful for testing the extension download process.
* `dist`: A website containing nightly builds akin to dist.civicrm.org. Useful for preparing CiviCRM tarballs.
* `distmgr`: A service which manages redirects and report-backs for the download site.
* `l10n`: WIP - A build environment for creating translation files.
* `joomla-demo`: WIP/incomplete/broken

Build types can be mixed/matched with different versions of Civi, e.g.

```bash
$ civibuild create my-drupal-civi47 \
  --type drupal-demo \
  --civi-ver master \
  --url http://my-drupal-civi47.localhost
$ civibuild create my-drupal-civi46 \
  --type drupal-demo \
  --civi-ver 4.6 \
  --url http://my-drupal-civi46.localhost
$ civibuild create my-wordpress-civi4719 \
  --type wp-demo \
  --civi-ver 4.7.19 \
  --cms-ver 4.0 \
  --url http://my-wp-civi4719.localhost
```

The `--civi-ver` argument will accept any branch or version tag.  *Note: the 4.7 version is in the `master` branch.*.

You can also specify `--patch` with a pull request URL to apply those changes on top of your CiviCRM version.

## Build Aliases

For developers who work with several CMSs and several versions of Civi, it's
useful to have a naming convention and shorthand for the most common
configurations.  Civibuild includes aliases (in `src/civibuild.aliases.sh`)
like "d44" and "wpmaster":

Create a build "d44" using build-type "drupal-demo" with Civi "4.4"

```
$ civibuild create d44 --url http://d44.localhost
```

Create a build "d45" using build-type "drupal-demo" with Civi "4.5"

```
$ civibuild create d45 --url http://d45.localhost
```

Create a build "wp45" using build-type "wp-demo" with Civi "4.5"

```
$ civibuild create wp45 --url http://wp45.localhost
```

Create a build "wpmaster" using build-type "wp-demo" with Civi's "master" branch

```
$ civibuild create wpmaster --url http://wpmaster.localhost
```

These aliases exactly match the demo sites deployed under civicrm.org (e.g.
"wp45" produces the demo site "wp45.demo.civicrm.org").


## Upgrading a site you installed with civibuild {:#upgrade-site}

If you have a working civibuild site and you'd like to upgrade CiviCRM to the latest version, follow these steps:

### Begin in the civicrm directory within your site {:#upgrade-site-begin}

```
cd ~/buildkit/build/dmaster/sites/all/modules/civicrm/
```

!!! note
    The path to this directory will vary depending on where you installed buildkit and what CMS you site uses.

### Check the status of all git repos {:#upgrade-site-git-scan}

There are multiple git repos in your build (`civicrm-core.git`, `civicrm-packages.git`, etal). Before making a major switch, first double-check that all of these repos are in sane condition &mdash; i.e. there shouldn't be any uncommitted changes, and the repos should be on normal branches. For this purpose, use [git-scan](https://github.com/totten/git-scan), (installed with [buildkit](/tools/buildkit.md)).

```
git scan status
```

!!! fail "Check for errors"
    If you see a message like *"Fast-forwards are not possible"* or *"Modifications have not been committed"*, then you'll need to clean up these git repositories before proceeding.


### Update the git repos {:#upgrade-site-git-scan-up}

To update to the latest version of a particular branch, use `git scan up` which will perform a standard "fast-forward merge" (`git pull --ff-only`) across all the repos:

```
git scan up
```

!!! tip
    If you didn't cleanup earlier, then "fast-forward" may not be possible. It takes some judgment to decide what to do &mdash; e.g. a "merge" versus "rebase". Rather than risk a wrong decision, `git scan` will skip these repos and display warnings instead.)

Alternatively, if you'd like to hop to a specific tag, you can use `givi` (a tool included with [buildkit](/tools/buildkit.md)), but keep in mind that if you hop to a *previous* tag with code that expects a different database scheme, there will be no way to run database downgrades.

```
givi checkout 4.7.17
```

### Update the generated code, config files, databases {:#upgrade-site-update}

Reinstalling will recreate/overwrite all generated-code, config-files, and database content. Any data you put into your site (e.g. test contacts, etc) will be lost.

```
civibuild reinstall dmaster
```

Alternatively, if you care about the content in the database, then don't do a reinstall. Instead, update the generated-code and perform a DB upgrade:

```
./bin/setup.sh -Dg
drush civicrm-upgrade-db
```


## Downgrading a site you installed with civibuild {:#downgrade-site}

If you are [reviewing a pull request](/core/pr-review.md) you may wish to *downgrade* a civibuild site in order to begin replicating the issue and testing the fix. Currently this is **not possible** with civibuild, so instead you will need to do a [rebuild](#rebuild) with the the `--civi-ver` argument to specify your target version of CiviCRM.


## Rebuilds {:#rebuild}

If you're interested in working on the build types or build process, then the workflow will consist of alternating two basic steps: (1) editing build scripts and (2) rebuilding. Rebuilds may take a few minutes, so it's helpful to choose the fastest type of rebuild that will meet your needs.

There are four variations on rebuilding. In order of fastest (least thorough) to slowest (most thorough):

<table>
  <thead>
  <tr>
    <th>Command</th>
    <th>Description</th>
    <th>Civibuild Metadata</th>
    <th>Civi+CMS Code</th>
    <th>Civi+CMS Config</th>
    <th>Civi+CMS DB</th>
  </tr>
  </thead>
  <tbody>
  <tr>
    <td><b>civibuild restore &lt;name&gt;</b></td>
    <td>Restore DB from pristine SQL snapshot</td>
    <td>Preserve</td>
    <td>Preserve</td>
    <td>Preserve</td>
    <td>Destroy / Recreate</td>
  </tr>
  <tr>
    <td><b>civibuild reinstall &lt;name&gt;</b></td>
    <td>Rerun CMS+Civi "install" process</td>
    <td>Preserve</td>
    <td>Preserve</td>
    <td>Destroy / Recreate</td>
    <td>Destroy / Recreate</td>
  </tr>
  <tr>
    <td><b>civibuild create &lt;name&gt; --force</b></td>
    <td>Create site, overwriting any files or DBs</td>
    <td>Preserve</td>
    <td>Destroy / Recreate</td>
    <td>Destroy / Recreate</td>
    <td>Destroy / Recreate</td>
  </tr>
  <tr>
    <td><b>civibuild destroy &lt;name&gt; ; civibuild create &lt;name&gt;</b></td>
    <td>Thoroughly destroy and recreate everything</td>
    <td>Destroy / Recreate</td>
    <td>Destroy / Recreate</td>
    <td>Destroy / Recreate</td>
    <td>Destroy / Recreate</td>
  </tr>
  </tbody>
</table>

## civicrm.settings.php; settings.php; wp-config.php {:#settings}

There are a few CiviCRM settings which are commonly configured on a per-server
or per-workstation basis. For example, civicrm.org's demo server has ~10
sites running different builds (Drupal/WordPress/CiviHR),
and visitors should not be allowed to download new extensions on any of those
sites. However, on the training server, trainees should be allowed to download
extensions. As discussed in
[Override CiviCRM Settings](https://wiki.civicrm.org/confluence/display/CRMDOC/Override+CiviCRM+Settings),
this setting (and many others) can be configured in civicrm.settings.php.

The `civicrm.settings.php` is created automatically as part of the build. One
could edit the file directly, but that means editing civicrm.settings.php
after every (re)build. The easiest way to customize the settings is to put
extra `.php` files in `/etc/civicrm.settings.d` &mdash; these files will be loaded
on every site that runs on this server (regardless of how many sites you
create or how many times you rebuild them).

For more details on how `civicrm.settings.d` works, see `/app/civicrm.settings.d/README.txt` within your buildkit installation.

A parallel structure exists for the CMS settings files. See also:

* `/app/drupal.settings.d/README.txt`
* `/app/wp-config.d/README.txt`

## Development/Testing of `civibuild` {:#development}

The tests for `civibuild` are stored in `tests/phpunit`.  These are
integration tests which create and destroy real builds on the local system.
To run them:

* Configure `amp` (as above)
* Ensure that a test site is configured (`civibuild create civibild-test --type empty`)
* Run `phpunit4` or `env DEBUG=1 OFFLINE=1 phpunit4`
    * Note that the tests accept some optional environment variables:
        * `DEBUG=1` - Display command output as it runs
        * `OFFLINE=1` - Try to avoid unnecessary network traffic


## Experimental: Multiple demo/training sites {:#demo-training}

When creating a batch of identical sites for training or demonstrations,
one may want to create a single source-code-build with several
databases/websites running on top (using "Drupal multi-site"). To install
extra sites,  use the notation "civibuild create buildname/site-id" as in:

Create the original build

```
$ civibuild create training --type drupal-demo --civi-ver 4.5 --url http://demo00.example.org --admin-pass s3cr3t
```

Create additional sites (01 - 03)

```
$ civibuild create training/01 --url http://demo01.example.org --admin-pass s3cr3t
$ civibuild create training/02 --url http://demo02.example.org --admin-pass s3cr3t
$ civibuild create training/03 --url http://demo03.example.org --admin-pass s3cr3t
```

Alternatively, create additional sites (01 - 20)

```bash
$ for num in $(seq -w 1 20) ; do
  civibuild create training/${num} --url http://demo${num}.example.org --admin-pass s3cr3t
done
```



## Credits

Some content on this page was migrated from other sources, including:


* "Upgrading a site" from [Tim Otten's StackExchange answer](https://civicrm.stackexchange.com/questions/17717/how-do-i-upgrade-civicrm-on-a-local-site-that-i-installed-with-buildkit-civibuil/17721#17721)

