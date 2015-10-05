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


