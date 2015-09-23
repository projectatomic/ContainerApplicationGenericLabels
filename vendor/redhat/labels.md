# Red Hat LABEL Metadata

Red Hat built images will include a fields in the metadata to identify the official primary name, version, etc in the Labels section. This will allow differentiating from alternative tags described below, and reconstruct the authoritative fully qualified URL for a given image.

## Naming and versioning related labels

The Fields are:

* `"architecture"`
  * Target hardware architecture the image is built for in the Red Hat terminology inherited from RPM.
  * Note: there is an upstream Architecture field, but it uses a different terminology. We should work upstream to sync. For now we have to keep the two fields in sync.
  * Right now the value is `"x86_64"`
* `"name"`
  * The primary name of the image (relative path without TAG): `PRODUCT[$PRODUCTGEN][--$PLATFORMDIFFERENTIATOR]/ REPO[$CONTENTGENERATION][--$PLATFORMDIFFERENTIATOR]`
* `"authoritative-source"`
  * The authoritative registry in which the image is published. For Red Hat this is `"registry.access.redhat.com"`. This allows e.g. to verify if a newer version is available independent of local tagging.
   * For ISVs this is optional.
* `"release"`
  * The build of the image, `$IMGBUILD`
* `"vendor"`
  * `"Red Hat, Inc."`
* `"version"`
  * Version part of the TAG in the primary name, `$COMPVER`
* `"com.redhat.component"`
  * The component in Bugzilla or other tracker representing the image. Can be used to file bugs.

To reconstruct the authoritative source for an image, docker pull...

```
$authoritative-source/$name:$version-$release
```

Aka:

```
$authoritative-source[:PORT]/PRODUCT[$PRODUCTGEN][--$PLATFORMDIFFERENTIATOR]/REPO[$CONTENTGENERATION][--$PLATFORMDIFFERENTIATOR]:$COMPVER-$IMGBUILD
```

## Other labels

* `"com.redhat.build-host"`
  * The build host used to create an image for internal use and auditability, similar to the use in RPM.
* `"distribution-scope"`
  * Scope of intended distribution of the image.
  * Possible values
    * `private`: No public redistribution intended
    * `authoritative-source-only` Redistribution only from the source listed in the `"authoritative-source"` label
    * `restricted` Redistribution only with permission
    * `public` No redistribution limits beyond licenses
  * For Red Hat product images this will be set to `"authoritative-source-only"`
* `"build-date"`
  * Date/Time image was built as [RFC 3339](https://tools.ietf.org/html/rfc3339) date-time (Optional)
* `"url"` (Optional)
  * Url with more information on the image
* `"summary"` (Required)
  * Short Description of the image
* `"description"` (Optional)
  * Detailed description of the image
* `"vcs-type"` (Optional)
  * The type of version control used by the container source. Generally one of git, hg, svn, bzr, cvs
* `"vcs-url"` (Optional)
  * URL of the version control repository
* `"vcs-ref"` (Optional)
  * A 'reference' within the version control repository; e.g. a git commit, or a subversion branch

### Examples

1. `registry.access.redhat.com/rhel7/rhel:7.1-4` has the following metadata:

        "Labels": {
                    "architecture": "x86_64",
                    "authoritative-source:" "registry.access.redhat.com",
                    "com.redhat.build-host": "rcm-img04.build.eng.bos.redhat.com",
                    "com.redhat.component": "rhel-server-docker",
                    "name": "rhel7/rhel",
                    "release": "4",
                    "vendor": "Red Hat, Inc.",
                    "version": "7.1",
                    "summary":"RHEL 7 base platform image",
                    "distribution-scope":"authoritative-source-only"
                },

1. `registry.access.redhat.com/rhel7/rsyslog:7.1-4` has the following metadata:

        "Labels": {
                    "architecture": "x86_64",
                    "authoritative-source": "registry.access.redhat.com"
                    "com.redhat.build-host": "rcm-img04.build.eng.bos.redhat.com",
                    "com.redhat.component": "rsyslog-docker",
                    "name": "rhel7/rsyslog",
                    "release": "4",
                    "vendor": "Red Hat, Inc.",
                    "version": "7.1",
                    "summary":"RHEL 7 rsyslog application image",
                    "distribution-scope":"authoritative-source-only"
                },

1. `registry.access.redhat.com/rhel7/php-5.4.16-3` has the following metadata:

        "Labels": {
                    "architecture": "x86_64",
                    "authoritative-source": "registry.access.redhat.com"
                    "com.redhat.build-host": "rcm-img04.build.eng.bos.redhat.com",
                    "com.redhat.component": "php-docker",
                    "name": "rhel7/php",
                    "release": "3",
                    "vendor": "Red Hat, Inc.",
                    "version": "5.4.16",
                    "summary": "Multiple",
                    "summary":"RHEL 7 based PHP platform image",
                    "distribution-scope":"authoritative-source-only"
                },

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
rhel8-beta/rsyslog:VERSION-$IMGBUILD
rhel8-beta/php...
...
```

## Bugzilla Mapping

```
REGISTRY/
 PRODUCT$PRODUCTGEN[--$PLATFORMDIFFERENTIATOR]/
 REPO[$CONTENTGENERATION][--$PLATFORMDIFFERENTIATOR]
 :$COMPVER-$IMGBUILD
```

The mapping to Bugzilla (or Jira) components will follow REPO-docker within the respective product. Details as follows:


* `REGISTRY` Ignored in Bugzilla
* `PRODUCT[$PRODUCTGEN]` Maps to the product/version in Bugzilla, other metadata such as
* `REPO[$CONTENTGENERATION][--$PLATFORMDIFFERENTIATOR]`
  * `REPO[$CONTENTGENERATION]` maps to the component with -docker appended
  * `PLATFORMDIFFERNTIATOR` is not part of the formal mapping
* `:$COMPVER` Maps to the version of the component
* `-$IMGBUILD` is not part of the formal mapping

Examples:

```
registry.access.redhat.com/rhel7/php:5.4.16-3
```

maps to

* Product: RHEL
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
docker.io/$PRODUCT[$PRODUCTGEN]--$PLATFORMDIFFERENTIATOR/$REPO[$PRODUCTGEN]:$VERSION-$BUILD
```

**Option 2**: level2 of the path

```
docker.io/$ISVUSER/$PRODUCT[$PRODUCTGEN]--$PLATFORMDIFFERENTIATOR:$VERSION-$BUILD
docker.io/library/$PRODUCT[$PRODUCTGEN]--$PLATFORMDIFFERENTIATOR:$VERSION-$BUILD
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
  * The primary name of the image (relative path w/o TAG): `PRODUCT[$PRODUCTGEN][--$PLATFORMDIFFERENTIATOR] /REPO[$CONTENTGENERATION][--$PLATFORMDIFFERENTIATOR]`
* `"release"`
  * The build of the image, `$IMGBUILD`
* `"vendor"`
  * Name of the ISV
* `"version"`
  * Version part of the TAG in the primary name.
* `"authoritative-source"`
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

TBA

## Red Hat Middleware Image Naming

TBA

