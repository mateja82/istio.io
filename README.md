| Site | Status
|------|-------
| istio.io | [![Netlify Status](https://api.netlify.com/api/v1/badges/c98435af-5464-4ac3-93c2-9c98faeec9b6/deploy-status)](https://app.netlify.com/sites/istio/deploys) [![CircleCI](https://circleci.com/gh/istio/istio.svg?style=shield)](https://circleci.com/gh/istio/istio.io)
| preliminary.istio.io | [![Netlify Status](https://api.netlify.com/api/v1/badges/a1cfd435-23d5-4a43-ac6d-8ec9230d9eb3/deploy-status)](https://app.netlify.com/sites/preliminary-istio/deploys)
| archive.istio.io | [![Netlify Status](https://api.netlify.com/api/v1/badges/f8c3eecb-3c5c-48d9-b952-54c7ed0ece8f/deploy-status)](https://app.netlify.com/sites/archive-istio/deploys)

## istio.io

This repository contains the source code for the [istio.io](https://istio.io),
[preliminary.istio.io](https://preliminary.istio.io) and [archive.istio.io](https://archive.istio.io) sites.

Please see the main Istio [README](https://github.com/istio/istio/blob/master/README.md)
file to learn about the overall Istio project and how to get in touch with us. To learn how you can
contribute to any of the Istio components, please
see the Istio [contribution guidelines](https://github.com/istio/community/blob/master/CONTRIBUTING.md).

* [Editing and testing content](#editing-and-testing-content)
* [Linting](#linting)
* [Site infrastructure](#site-infrastructure)
* [Versions and releases](#versions-and-releases)
  * [How versioning works](#how-versioning-works)
  * [Publishing content immediately](#publishing-content-immediately)
  * [Creating a version](#creating-a-version)

## Editing and testing content

We use [Hugo](https://gohugo.io/) to generate our sites. To build and test the site locally, we use a docker
image that contains Hugo. To build and serve the site, simply go to the root of the tree and do:

```bash
$ make serve
```

This will build the site and start a web server hosting the site. You can then connect to the web server
at `http://localhost:1313`.

To make and serve the site from a remote server, override `ISTIO_SERVE_DOMAIN` as follows with the IP address
or DNS Domain of the server as follows:

```bash
$ export ISTIO_SERVE_DOMAIN=192.168.7.105
$ make serve
```
This will build the site and start a web server hosting the site. You can then connect to the web server
at `http://192.168.7.105:1313`.

All normal content for the site is located in the `content` directory, as well as in sibling translated
directories such as content_zh.

## Linting

We use linters to ensure some base quality to the site's content. We currently
run 3 linters as a precommit requirement:

* HTML proofing, which ensures all your links are valid along with other checks.

* Spell checking.

* Style checking, which makes sure your markdown files comply with our common style rules.

You can run these linters locally using:

```bash
$ make build
$ make gen
$ make lint
```

If you get spelling errors, you have three choices to address each:

* It's a real typo, so fix your markdown.

* It's a command/field/symbol name, so stick some `backticks` around it.

* It's really valid, so go add the word to the `.spelling` file at the root of the repo.

And you can set any value to an environment variable named "INTERNAL_ONLY", then the linter will not check external links. It looks like that:

```bash
$ make INTERNAL_ONLY=True lint
```

## Site infrastructure

Here's how things work:

* Primary site content is in the `content` directory. This is mostly
markdown files which Hugo processes into HTML.

* Additional site content is in the `static` directory. These are files that
Hugo directly copies to the site without any processing.

* The `src` directory contains the source material for certain files from the
`static` directory. You use

    ```bash
    $ make build
    ```

    to build the material from the `src` directory and refresh what's in the `generated`
    directory.

## Versions and releases

Istio maintains three variations of its public site.

* [istio.io](https://istio.io) is the main site, showing documentation for the current release of the product.

* [archive.istio.io](https://archive.istio.io) contains snapshots of the documentation for previous releases of the product.
This is useful for customers still using these older releases.

* [preliminary.istio.io](https://preliminary.istio.io) contains the actively updated documentation for the next release of the product.

The user can trivially navigate between the different variations of the site using the gear menu in the top right
of each page. All three sites are hosted on [Netlify](https://netlify.com).

### How versioning works

* Documentation changes are primarily committed to the master branch of istio.io. Changes committed to this branch
are automatically reflected on preliminary.istio.io.

* The content of istio.io is taken from the latest release-XXX branch. The specific branch that
is used is determined by the istio.io [Netlify](https://netlify.com) project's configuration.

* The content of archive.istio.io is taken from the older release-XXX branches. The set of branches that
are included on archive.istio.io is determined by the `TOBUILD` variable in this
[script](https://github.com/istio/admin-sites/blob/master/archive.istio.io/build.sh)

> The above means that if you want to do a change to the main istio.io site, you need
to make the change in the master branch of https://github.com/istio/istio.io and then merge that change into the
current release branch.

### Publishing content immediately

Checking in updates to the master branch will automatically update preliminary.istio.io, and will only be reflected on
istio.io the next time a release is created, which can be several weeks in the future. If you'd like some changes to be
immediately reflected on istio.io, you need to check your changes both to the master branch and to the
current release branch (named release-XXX such as release-0.7).

### Creating a version

Here are the steps necessary to create a new documentation version. Let's assume the current
version of Istio is 0.6 and you wish to introduce 0.7 which has been under development.

#### Creating the release branch

1. Switch to the istio/istio.io repo and make sure everything is up to date.

1. Create a new release branch off of master, named as release-*major*.*minor*, which in this case would be
release-0.7. There is one such branch for every release.

1. In the **release** branch you created, edit the file `data/args.yml`. Set the `preliminary` field to `false`
and the `source_branch_name` and `doc_branch_name` fields to the name of the branch, in this case release-0.7.

1. Commit the previous edit to your local git repo and push your **release** branch to GitHub.

#### Updating preliminary.istio.io

1. Switch to the istio/istio.io repo and make sure everything is up to date.

1. In the **master** branch, edit the file `data/releases.yml` and add a new entry at the top of the file
for version 0.8. You'll need to make sure the URLs are updated for the first few entries. The top
entry (0.8) should point to preliminary.istio.io. The second entry (0.7) should point to istio.io. The third
and subsequent entries should point to archive.istio.io.

1. In the **master** branch, edit the file `data/args.yml` and update the `version` and `full_version` fields to have the version
of the next Istio release. In this case, you would set the fields to 0.8 and 0.8.0 respectively.

1. Commit the previous edits to your local git repo and push the **master** branch to GitHub.

1. Wait a while (~2 minutes) and browse preliminary.istio.io to make sure everything looks good.

#### Updating istio.io

1. Go to the istio.io project on [Netlify](https://netlify.com)

1. Change the branch that is built from the previous release's branch to the new release branch, in this case release-0.7

1. Select the option to trigger an immediate rebuild and redeployment.

1. Once deployment is done, browse istio.io and make sure everything looks good.

#### Updating archive.istio.io

1. Switch to the istio/istio.io repo and make sure everything is up to date.

1. Go to the [Google Custom Search Engine](https://cse.google.com) and do the following:

    - Download the archive.istio.io CSE context file from the Advanced tab.

    - Add a new FacetItem at the top of the file containing the previous release's version number. In
    this case, this would be "V0.6".

    - Upload the updated CSE context file to the site.

    - In the Setup section, add a new site that covers the previous release's archive directory. In this
    case, the site URL would be archive.istio.io/v0.6/*. Set the label of this site to the name of the
    facet item created above (V0.6 in this case).

1. In the **previous release's** branch (in this case release-0.6), edit the file `data/args.yml`. Set the
`archive` field to true and the `archive_date` field to the current date.

1. Commit the previous edit to your local git repo and push the **previous release's* branch to GitHub.

1. Switch to the istio/admin-sites repo.

1. Edit the `archive.istio.io/build.sh` script to add the newest archive version (in this case
release-0.6) to the `TOBUILD` variable.

1. Commit the previous edit to your local git repo and push the change to GitHub.

1. Wait a while (~10 minutes) and browse archive.istio.io and make sure everything looks good.
