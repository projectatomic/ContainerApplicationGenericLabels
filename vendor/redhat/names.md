# Red Hat Image Naming Policy

## Baseline

Red Hat needs a consistent naming policy for Docker images released to the public in order to provide predictability for users.

Docker URLs in the V1 version of the protocol/repository format work similar to github repository names. Their structure is:

```
REGISTRY[:PORT]/USER/REPO[:TAG]
```

The implicit default registry is docker.io, so relative URLs as redhat/rhel resolve to docker.io/redhat/rhel .

The special name library/* maps to direct, non-prefixed images: e.g. docker.io/rhel.

Right now Red Hat content is redirected from docker.io to registry.access.redhat.com, and we are limited by the docker redirect of as select docker.io/library/IMAGE links and the docker.io/redhat/* namespace:

```
docker.io/rhel -> registry.access.redhat.com/rhel (alias for rhel7)
docker.io/rhel7 -> registry.access.redhat.com/rhel7
docker.io/rhel6 -> registry.access.redhat.com/rhel6
docker.io/redhat/* -> registry.access.redhat.com/redhat/*
```

Red Hat's Community content is accessible on Docker Hub:

```
docker.io/fedora
docker.io/centos
docker.io/jboss/*
```

With the Atomic Host GA / RHEL 7.1, we are shipping a version of docker, that by default will contact the Red Hat. That allows us to segment the namespace by registry.

The registry content is basically simply a mapping of names tagged, this does not involve copying content. There is however administrative overhead from keeping multiple 'symbolic' links as they have to be kept up to date.

The REPO is indeed a repository containing a number of explicitly tagged and a number of hidden layers. An 'IMAGE' is a specific layer-complete branch within a repository. For simplification we use 'image' and 'repository' almost synonymously although they are not the same. Eg. different images can be tagged into a single repository.

Note: Future versions of Docker will remove the limitation on only two levels.

## Problem Statements

* The flat redhat/* namespace is insufficient to cover all Red Hat products:
* We would have to map all product and version information into the REPO[:TAG] part of the URL.
* Which does not always map to existing best practices and
* We will not be able to avoid name conflicts between our products.
* registry.access.redhat.com is a bit long, a shorter model would be preferrable. .io appears to be the common TLD for these kind of services.
* The  naming and versioning for Platform images (images meant for users to build other images on top of) has to be extremely stable and predicatble to allow reliable tracking without imlicit changes e.g. of :latest on people building images on top.
* In many cases the generation of the underlying platform and of the content has significant impact on the user (e.g. software will stop working when the underlying stack moves from RHEL 6 to RHEL 7 or from Python 2.7 to Python 3)
* For application images (leaf layers, not meant to be buid on top) this is much less of a problem.

## Notes

We are proposing to use the [registry].redhat.io domain for the registry, instead of registry.access.redhat.com. The existing registry.access.redhat.com address will continue to work and for the Atomic Host GA time it's still the default. That is not implemented yet and may be obsoleted by a move to service records in docker.

It is important to keep in mind, that we are versioning *aggregate* packages, not individual components. This is similar to the versioning of RHEL itself vs. the included version of the kernel. There are too many components in the stack so that we are projecting the versioning on the version of the aggregate vehicle we are building from. The versioning will strictly follow the primary, name-giving component.

Below we will start with a simplified model used for base images and application, and an extended naming scheme for everything else.

At this point this proposal is focused on RHEL and related images. It will be the baseline for discussions with other groups, specifically JBoss.

In order to accommodate more complex scenarios such as multiple parallel generations of images (e.g. python27 vs python30) or higher-level platform images built in parallel for multiple underlying platform generations (e.g. SCLs built on RHEL 6 vs RHEL 7), there are a number of optional fields in the full scheme.

## Docker v1 Namespacing and Versioning Policy

### Repository Naming and Tagging

Based on the v1 Docker naming scheme `REGISTRY[:PORT]/USER/REPO[:TAG]`, Red Hat defines the naming and versioning scheme for Red Hat-built or certified images as follows:

```
registry.access.redhat.com/
 PRODUCT[$PRODUCTGEN][--$PLATFORMDIFFERENTIATOR]/
 REPO[$CONTENTGENERATION][--$PLATFORMDIFFERENTIATOR]
 :$COMPVER-$IMGBUILD
```

The spaces (" "s) after the are just for formating and not part of the actual scheme. The brackets ("[","]" ) denote optional fields.

* PRODUCT
  * The product this is part of - e.g. RHEL
* $PRODUCTGEN
  * The major relevant generation of the product that is relevant for user experience in the context of the images if needed - for RHEL this is the major release (e.g. RHEL 7)
  *The minor release of a product will only be used if it is significant for the respective product from a user point of view. Example: RHEL minor releases are not significant at that level due to the life cycle guarantees given with RHEL, while with SCLs no versions are relevant at this level, so it's simply scl/*
* REPO
  * Name of the image - based on the primary component - e.g. rhel for base, php for php images.
  * Can have a modifier if it's a variant or the combination is very important: e.g. 'rhel-systemd' for a rhel platform image that includes systemd.
  * Note: The URL up to IMAGE actually designates a Repository, the TAG part then addresses a specific IMAGE within the Repo. We will use Image and Repo exchangeably.
* $CONTENTGENERATION
  * Optional number differentiating major generational changes withing the primary content that are significant for the use (ie the developer of a layered image). Example: Python vs python33, ruby193 vs ruby200.
* $PLATFORMDIFFERENTIATOR
  * Optional field to differentiate builds of higher-level images based on the underlying platform generation. Example: SCL ruby200 built on RHEL 6 base vs RHEL 7 - the issue here is, that behavior of the whole stack is sufficiently different, so that as a developer I want to explicitly track (code may break in the move). We can't just track this in the TAG because the FROM line sets the base implicitly in the static linking model.
* $COMPVER
  * Version string of the primary component without the build part of the rpm. For core RHEL platform images this is the release version string, for e.g. php it would be right now be '5.4.16' based on 'php-5.4.16-23.rhel7_0.3'.x86_64.rpm. For different frames of reference (aka non-RHEL), this would follow a different model depending what makes sense for the repsective product..
* $IMGBUILD
  * Build of the image


#### Examples:

```
registry.access.redhat.com/rhel6/rhel:6.5-12
registry.access.redhat.com/rhel7/rhel:7.0-23
registry.access.redhat.com/rhel7/rhel-tools:7.0-5
registry.access.redhat.com/rhel7/php:5.4.16-3
```

```
registry.access.redhat.com/rhscl/ruby193--rhe6:1.1-11
registry.access.redhat.com/rhscl/ruby193--rhel7:1.1-3
```

```
registry.access.redhat.com/rhscl/ruby200--rhel6:1.1-1
registry.access.redhat.com/rhscl/ruby200--rhel7:1.1-15
```

```
registry.access.redhat.com/rhscl/postgresql92:1.1-4
```

## BREW Naming

The internal root of the naming is the naming of the dist-git repo in BREW that contains the Dockerfile.So that naming must follow the above schema (without the tagging as that will be handled automatically)

The naming in Brew should be reponame-docker

The product context is derived from Brew.

Exceptions:

RHEL Server base images. Internal name is rhel-server-docker, external name is just rhel

Examples:

rhel-server-docker -> rhel$MAJOR/rhel:TAG
rhel-tools-docker -> rhel$MAJOR/rhel-tools:TAG
