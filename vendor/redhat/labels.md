# Red Hat LABEL Metadata

Red Hat built images will include a fields in the metadata to identify the official primary name, version, etc in the Labels section. This will allow differentiating from alternative tags described below, and reconstruct the authoritative fully qualified URL for a given image.

## Naming and versioning related labels

The Fields, all required, are:

| Name           | Description                        |
|----------------|------------------------------------|
| architecture | Target hardware architecture the image is built for in the Red Hat terminology inherited from RPM. **Note**: there is an upstream Architecture field, but it uses a different terminology. We should work upstream to sync. For now we have to keep the two fields in sync. Current valid value is `"x86_64"` |
| name         | The primary name of the image (relative path without TAG): `PRODUCT[[-]$PRODUCTGEN][--$PLATFORMDIFFERENTIATOR]/ REPO[--$CONTENTGENERATION][--$PLATFORMDIFFERENTIATOR]` |
| authoritative-source-url | The authoritative registry in which the image is published. For Red Hat this is `"registry.access.redhat.com"`. This allows e.g. to verify if a newer version is available independent of local tagging. For ISVs this is optional. |
| release        | The build of the image, `$IMGBUILD` |
| vendor         | "Red Hat, Inc." |
| version        | Version part of the TAG in the primary name, `$COMPVER` |
| com.redhat.component | Respective component name in [Red Hat Bugzilla](https://bugzilla.redhat.com/) and the package name field inside the internal build system. |

To reconstruct the authoritative source for an image, docker pull...

```
$authoritative-source-url/$name:$version-$release
```

In other words:

```
$authoritative-source-url[:PORT]/PRODUCT[[-]$PRODUCTGEN][--$PLATFORMDIFFERENTIATOR]/REPO[--$CONTENTGENERATION][--$PLATFORMDIFFERENTIATOR]:$COMPVER-$IMGBUILD
```

## Other labels

| Name           | Required | Description                        |
|----------------|----------|------------------------------------|
| com.redhat.build-host | yes | The build host used to create an image for internal use and auditability, similar to the use in RPM. |
| distribution-scope | yes | Scope of intended distribution of the image. For Red Hat product images this will be set to `"authoritative-source-only"`. Possible values <ul><li>`private`: No public redistribution intended</li><li>`authoritative-source-only`: Redistribution only from the source listed in the `"authoritative-source-url"` label</li><li>`restricted`: Redistribution only with permission</li><li>`public`: No redistribution limits beyond licenses</li></ul> |
| build-date     | no | Date/Time image was built as [RFC 3339](https://tools.ietf.org/html/rfc3339) date-time |
| url            | no | Url with more information on the image |
| summary        | yes | Short Description of the image |
| description    | yes | Detailed description of the image |
| maintainer     | no | Name and e-mail for the person who is primary contact for the container image. Similar to `MAINTAINER` instruction [which is obsolete](https://github.com/docker/docker/pull/25466) since docker 1.13 |
| vcs-type       | no | The type of version control used by the container source. Generally one of git, hg, svn, bzr, cvs |
| vcs-url        | no | URL of the version control repository |
| vcs-ref        | no | A 'reference' within the version control repository; e.g. a git commit, or a subversion branch |
| io.k8s.description | no |  Description of the container displayed in Kubernetes |
| io.k8s.display-name | yes | Name of the container displayed in Kubernetes |

### Examples

1. `registry.access.redhat.com/rhel7/rhel:7.1-4` has the following metadata:

        "Labels": {
                    "architecture": "x86_64",
                    "authoritative-source-url:" "registry.access.redhat.com",
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
                    "authoritative-source-url": "registry.access.redhat.com"
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
                    "authoritative-source-url": "registry.access.redhat.com"
                    "com.redhat.build-host": "rcm-img04.build.eng.bos.redhat.com",
                    "com.redhat.component": "php-docker",
                    "name": "rhel7/php",
                    "release": "3",
                    "vendor": "Red Hat, Inc.",
                    "version": "5.4.16",
                    "summary":"RHEL 7 based PHP platform image",
                    "distribution-scope":"authoritative-source-only"
                },

1. `registry.access.redhat.com/rhscl/php-56-rhel7:5.6-3` has the following metadata:

        "Labels": {
                    "architecture": "x86_64",
                    "authoritative-source-url": "registry.access.redhat.com"
                    "com.redhat.build-host": "rcm-img04.build.eng.bos.redhat.com",
                    "com.redhat.component": "rh-php56-docker",
                    "name": "rhscl/php-56-rhel7",
                    "release": "3",
                    "vendor": "Red Hat, Inc.",
                    "version": "5.6",
                    "summary": "PHP 5.6 platform for building and running applications",
                    "distribution-scope": "authoritative-source-only",
                    "url": "http://github.com/sclorg/rhscl-dockerfiles",
                    "io.k8s.description="Platform for building and running PHP 5.6 applications",
                    "io.k8s.display-name="Apache 2.4 with PHP 5.6",
                },


