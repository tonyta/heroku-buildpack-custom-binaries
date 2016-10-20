# Heroku Buildpack for Custom Binaries

This is a [Heroku buildpack](http://devcenter.heroku.com/articles/buildpacks)
for adding pre-compiled custom binaries into your project.

## Usage

Add this buildpack to your Heroku project:

``` bash
heroku buildpacks:add https://github.com/tonyta/heroku-buildpack-custom-binaries#v1.0.0
```

Add a `.custom_binaries` file to the root of your project with the names and
urls of your gzipped tarballed binary (YAML format):

``` yaml
# app_root/.custom_binaries
executable-name: https://s3-us-wet-1.amazonaws.com/url-to-your-binary/your-archive.tar.gz
another-binary: https://s3-us-wet-1.amazonaws.com/url-to-other-binary/archive.tar.gz
```

That's it!

## How it works

This buildpack will unarchive your binary and add it to `/app/custom_bin` when
Heroku compiles your project's slug. It will also create a
[`profile.d` script](https://devcenter.heroku.com/articles/buildpack-api#profile-d-scripts)
that will append this directory to `$PATH` to your binary whenever your app is
deployed.

## Sounds great! But how do I build a binary that works with Heroku?

Great news! All Heroku projects use Heroku's
[Cedar-14 stack on Ubuntu 14.04 LTS](https://devcenter.heroku.com/articles/cedar-ubuntu-packages)
so you have to do is compile your binary on a similar environment, compress it
into a gzipped tarball, then host it somewhere it's available via url.

### Compile your binary for Heroku

The simplest way to do this is to compile from source on either a fresh Heroku
project or using the Heroku's Cedar-14 docker image.

#### Using Heroku

Create a blank Heroku project:

``` bash
heroku create
heroku run bash --app that-projects-name
```

Then you can build from source and transfer it locally using `git` or `scp`.

#### Docker Cedar-14 Image

You can also use Heroku's official Cedar-14 image:

``` bash
docker run -it heroku/cedar:14 bash
```

This has the additional convenience of being able transfer your binary to a
bind-mounted volume when you use the `-v` flag.

### Host a gzipped tarball

Build a gzipped tarball bundle of the resulting binary:

``` bash
tar -cvzf archive-name.tar.gz binary-to-compress
```

Then make it available to via URL through Amazon S3 or your favorite online
file storage.
