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

Generally, a repository only contains one 'stream' of image updates; the same image is rebuilt and distributed as bugs and flaws are fixed. There are some exceptions, wuch as the 'rhel' repository, which contains both RHEL 6 and RHEL 7 images, and a handful of OpenShift repositories have a similar model. This is documented where appropriate.

Note: Future versions of Docker will remove the limitation on only two levels.

## Problem Statements

* The flat redhat/* namespace is insufficient to cover all Red Hat products:
* We would have to map all product and version information into the REPO[:TAG] part of the URL.
* Which does not always map to existing best practices and
* We will not be able to avoid name conflicts between our products.
* registry.access.redhat.com is a bit long, a shorter model would be preferrable. .io appears to be the common TLD for these kind of services.
* The  naming and versioning for Platform images (images meant for users to build other images on top of) has to be extremely stable and predictable to allow reliable tracking without imlicit changes e.g. of :latest on people building images on top.
* In many cases the generation of the underlying platform and of the content has significant impact on the user (e.g. software will stop working when the underlying stack moves from RHEL 6 to RHEL 7 or from Python 2.7 to Python 3)
* For application images (leaf layers, not meant to be build on top) this is much less of a problem.

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
 PRODUCT[[-]$PRODUCTGEN][--$PLATFORMDIFFERENTIATOR][--$RELEASE]/
 REPO[--$CONTENTGENERATION][--$PLATFORMDIFFERENTIATOR]
 :$COMPVER-$IMGBUILD
```

The spaces (" "s) after the are just for formating and not part of the actual scheme. The brackets ("[","]" ) denote optional fields.

* PRODUCT
  * The product this is part of - e.g. RHEL
* $PRODUCTGEN
  * The major relevant generation of the product that is relevant for user experience in the context of the images if needed - for RHEL this is the major release (e.g. RHEL 7)
  *The minor release of a product will only be used if it is significant for the respective product from a user point of view. Example: RHEL minor releases are not significant at that level due to the life cycle guarantees given with RHEL, while with SCLs no versions are relevant at this level, so it's simply scl/*
  * Optionally this part may be separated from PRODUCT by hyphen ("-").
* $RELEASE
  * Indicate specific release milestone. Example is beta or tech preview releases which can be used to deliver pre-release content with different levels of support.
* REPO
  * Name of the image - based on the primary component - e.g. rhel for base, php for php images.
  * Can have a modifier if it's a variant or the combination is very important: e.g. 'rhel-systemd' for a rhel platform image that includes systemd.
  * Note: The URL up to IMAGE actually designates a Repository, the TAG part then addresses a specific IMAGE within the Repo. We will use Image and Repo exchangeably.
* $CONTENTGENERATION
  * Optional number differentiating major generational changes withing the primary content that are significant for the use (ie the developer of a layered image). Example: python vs python-33, ruby-193 vs ruby-200.
* $PLATFORMDIFFERENTIATOR
  * Optional field to differentiate builds of higher-level images based on the underlying platform generation. Example: SCL ruby-200 built on RHEL 6 base vs RHEL 7 - the issue here is, that behavior of the whole stack is sufficiently different, so that as a developer I want to explicitly track (code may break in the move). We can't just track this in the TAG because the FROM line sets the base implicitly in the static linking model.
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
registry.access.redhat.com/rhscl/ruby-193-rhel6:1.9.3-11
registry.access.redhat.com/rhscl/ruby-193-rhel7:1.9.3-3
```

```
registry.access.redhat.com/rhscl/ruby-200-rhel6:2.0-1
registry.access.redhat.com/rhscl/ruby-200-rhel7:2.0-15
```

```
registry.access.redhat.com/rhscl/postgresql-92-rhel7:9.2-4
```

```
registry.access.redhat.com/redhat-sso-7/sso71-openshift:1.3-3
```

## Koji Naming

The internal root of the naming is the naming of the dist-git repo that contains the Dockerfile. So that naming must follow the above schema (without the tagging as that will be handled automatically)

dist-git repos should be named `$REPO-docker`.

The product context is derived from koji.

Exceptions:

RHEL Server base images. Internal name is rhel-server-docker, external name is just rhel

Examples:

rhel-server-docker -> rhel$MAJOR/rhel:TAG  
rhel-tools-docker -> rhel$MAJOR/rhel-tools:TAG 

## Primary Product Images in library/

In *addition*, primary product images for our core products will be created in the `library/` namespace to allow direct pull from `redhat.io/REPO` similar to the existing redirects from Docker. This includes an aggregate rhel repository with multiple majors. Note, this is a simple tag model.

Primary product images are images that for convenience and positioning purposes we want to be able to pull directly from `redhat.io/REPO` and redirected from `docker.io/redhat/REPO` or even `docker.io/REPO`.

Examples:

```
registry.access.redhat.com/rhel:7.0-23
registry.access.redhat.com/rhel:6.5-12
registry.access.redhat.com/rhel7:7.0-23
registry.access.redhat.com/rhel6:6.5-12
registry.access.redhat.com/rhel-tools:7.0-5
```

Primary images are the name-giving core images for official Red Hat products as well as select images that are key for the operation of the  containerized platform. Example list:

```
rhel
rhel6
rhel7
rhel-tools
```

The `/redhat` namespace will be set up to redirect to `/library`

One thing to keep in mind is that the `:TAG` part is not regularly used by most users. Only users who very specifically care about using certain versions will use it, or users wanting to find out what exactly they are using in their builds. The vast majority of users will rely on the REPO part of the url before the ':'.

**Note:** The example used above of having a `/rhel` repo, that includes both, rhel7 and rhel6 images is probably not going to work in the current layout of Crane/CDN on the Red Hat registry. So for now `/library/rhel` is simply `rhel7`.

## Latest TAG

The `:latest` tag will always be set to the highest version image in the repository. In cases of multi-major-version repositories this is the newest GA image in the newest major. E.g. `/library/rhel:latest` would point to the newest RHEL 7 image. 

## Compatibility TAGs

Until an automatic redirect solution can be implemented, or the approach can be phased out Red Hat will carry a number of additional TAGs for compatibility: `/redhat/*` for the existing redirect from docker.io.

## Beta Releases

Beta releases will be treated as separate 'generations' at the first level. Example:

```
rhel8-beta/rhel:8beta-$IMGBUILD
rhel8-beta/rhel-tools:8beta-$IMGBUILD
rhel8-beta/rsyslog:$VERSION-$IMGBUILD
rhel8-beta/php...
...
```

## Tech Preview Releases

Tech Preview releases are treated similarly to GA. 
There need be no special signifier in the repository name that an
image is tech preview, as the images (like rpms) are treated as GA 
in terms of signing. Tech Preview status should be handled at the Documentation
Level.

## Bugzilla Mapping

```
REGISTRY/
 PRODUCT[-]$PRODUCTGEN[--$PLATFORMDIFFERENTIATOR]/
 REPO[--$CONTENTGENERATION][--$PLATFORMDIFFERENTIATOR]
 :$COMPVER-$IMGBUILD
```

The mapping to Bugzilla (or Jira) components will follow REPO-docker within the respective product. Details as follows:


* `REGISTRY` Ignored in Bugzilla
* `PRODUCT[[-]$PRODUCTGEN]` Maps to the product/version in Bugzilla, other metadata such as
* `REPO[--$CONTENTGENERATION][--$PLATFORMDIFFERENTIATOR]`
  * `REPO[--$CONTENTGENERATION]` maps to the component with -docker appended
  * `PLATFORMDIFFERNTIATOR` is not part of the formal mapping
* `:$COMPVER` Maps to the version of the component
* `-$IMGBUILD` is not part of the formal mapping

Examples:

```
registry.access.redhat.com/rhel7/php:5.4.16-3
```

maps to

* Product: Red Hat Enterprise Linux 7
* Version: 7.0
* Component: php-docker

## ISV Namespaces

### Registries and Repositories

Certified ISV content will live in per-isv registries, federated in the search. The default namespaces is:

```
registry-$ISV.rhcloud.com/library/REPO
```

In the future ISV images may be permitted to be hosted in 3rd party registries, pending agreement on rules for that.

The ISV can add other urls but only the above will be federated in docker pull w/o explicit repository label (see redirect below).

The naming scheme for ISV images should follow the practice established above.

A key point is, that ISV images provided for RHEL must carry a reference to the Red Hat platform in their tag either in the first or second level of the path.

**Option 1**: level1 of the path

```
docker.io/$PRODUCT[[-]$PRODUCTGEN]--$PLATFORMDIFFERENTIATOR/$REPO[[-]$PRODUCTGEN]:$VERSION-$BUILD
```

**Option 2**: level2 of the path

```
docker.io/$ISVUSER/$PRODUCT[[-]$PRODUCTGEN]--$PLATFORMDIFFERENTIATOR:$VERSION-$BUILD
docker.io/library/$PRODUCT[[-]$PRODUCTGEN]--$PLATFORMDIFFERENTIATOR:$VERSION-$BUILD
```

Examples:

**Option 1** is recommended for products with multiple container images for a specific platform. **Option 2** is recommended for ISVs supporting multiple platforms with a single product and few images.

```
docker.io/kollab14--rhel7/kollab:14.0-23
docker.io/10gen/mongo--rhel7:2.4.9-8
docekr.io/mongo--rhel7:2.4.9-8
```

The `PLATFORMDIFFERENTIATOR` may only be at the major release level. E.g. `mongo--rhel7.1:2.4.9-8` would *NOT* be permissable.

The exception to this would be an image built for an advanced life cycle stream such as EUS, that actually is specific to the individual minor release.

Please note, that at this point Red Hat has *no* plans to actually offer any images for advanced life cycle offerings. ISVs would require a special agreement allowing them to do that on their own.

### ISV Metadata

Red Hat uses the LABEL metadata field to provide additional information for images. All LABELs that are not actively used by the ISV must be overwritten with "". The following labels must be set appropriately to pass certification:

* `"name"`
  * The primary name of the image (relative path w/o TAG): `PRODUCT[[-]$PRODUCTGEN][--$PLATFORMDIFFERENTIATOR] /REPO[--$CONTENTGENERATION][--$PLATFORMDIFFERENTIATOR]`
* `"release"`
  * The build of the image, `$IMGBUILD`
* `"vendor"`
  * Name of the ISV
* `"version"`
  * Version part of the TAG in the primary name.
* `"authoritative-source-url"`
  * The official registry the ISV is publishing the images to.
* `"distribution-scope"`
  * Distribution scope for the image

Any vendor specific LABELS, `'com.redhat.*'`, must be blanked out.

## Global Namespaces

Global namespaces will be segmented by the registry name:

```
redhat.io/* (a.k.a. registry.access.redhat.com/*)
registry-$ISV.rhcloud.com/*
docker.io/*
quay.io/*
```

The Red Hat search should explicitly list the registries for images it lists (at this point redhat.io & registry-$ISV.rhcloud.com).

We are in discussions with Docker on how to federate our content into their search.

## V2 Protocol

The work on defining the V2 protocol and repository/image format is ongoing. The naming model is expected to change with the introduction of V2.

## Red Hat Software Collections Containers and Labels

Red Hat Software Collections containers use the following values:

* $PRODUCT: rhscl
* $PRODUCTGEN: not specified, since RHSCL 1.x and RHSCL 2.x content space is shared
* $REPO: name of the Software Collection without vendor prefix (`rh` is already used in the PRODUCT part, so no need to duplicate)
* $CONTENTGENERATION: part of the main component version, that is related to API stability
  * for example `rh-php56` collection will use 56 as $CONTENTGENERATION
* $PLATFORMDIFFERENTIATOR: RHEL version that the collection is based on; e.g. rhel6, rhel7, ...
* $COMPVER: part of the main component version, that is related to API stability
  * for example `rh-php56` collection will use 56 as $COMPVER
* $IMGBUILD: build of the image, usually increasing integer

### Beta Releases

Beta releases will be treated as separate 'generations' at the first level. Example:

```
rhscl-beta/php-56-rhel7:5.6-3
rhscl-beta/postgresql-94-rhel7:9.4-1
...
```


### Bugzilla Mapping

The following rules are used for Bugzilla mapping in Red Hat Software Collections:

* `REGISTRY` Ignored in Bugzilla
* `PRODUCT[[-]$PRODUCTGEN]` is not part of the formal mapping
* `REPO[--$CONTENTGENERATION][--$PLATFORMDIFFERENTIATOR]`
  * `REPO[--$CONTENTGENERATION]` maps to the Software Collection name with -docker appended
  * `PLATFORMDIFFERNTIATOR` is not part of the formal mapping
* `:$COMPVER` Maps to the Software Collection name and is used in version field in Bugzilla
* `-$IMGBUILD` is not part of the formal mapping

Examples:
```
registry.access.redhat.com/rhscl/php-56-rhel7:5.6-3
```

maps to

* Product: Red Hat Software Collections
* Component: rh-php56-docker
* Version: rh-php56


## Red Hat Middleware Image Naming

TBA 
