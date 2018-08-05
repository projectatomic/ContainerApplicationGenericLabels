# Standard Container/Application Identifiers
Default Containerized Application Labels/Annotations/Ids used to document the application and/or image

With the introduction of the [Atomic command](http://developerblog.redhat.com/2015/04/21/introducing-the-atomic-command/), we are accessing container image meta data to describe the purpose of the image or the application defined by the image.

We would like to get consensus on default names.  Proposals have been made to namespace the label names defined
in Docker.  

But we believe there should be some generic toplevel names defined that are not vendor specific, or
distribution specific.  

If you would like to see additional names and descriptions added please open issues and send pull requests to update this readme.

## Overview

The following types of data are being considered:

1. Labels used to describe how to use the application/image

 | Name        | Description                            |
 |-------------|----------------------------------------|
 | help        | Command to run the help command of the image|
 | run         | Command to run the image|
 | run_opts_file | Path to a file containing options that will be used in 'run' command in place of ${RUN_OPTS}|
 | uninstall   | Command to uninstall the image|
 | install     | Command to install the image|
 | stop        | Command to execute before stopping container|
 | debug       | Command to run the image with debugging turned on|

1. Labels Names used to describe the application/image

 | Name        | Description                            |
 |-------------|----------------------------------------|
 | name        | Name of the Image|
 | version     | Version of the image|
 | release     | Release Number for this version|
 | architecture| Architecture for the image|
 | build-date  | Date/Time image was built as [RFC 3339](https://tools.ietf.org/html/rfc3339) date-time|
 | vendor      | Owner of the image|
 | url         | Url with more information on the image|
 | summary     | Short Description of the image|
 | description | Detailed description of the image|
 | vcs-type    | The type of version control used by the container source. Generally one of git, hg, svn, bzr, cvs|
 | vcs-url     | URL of the version control repository|
 | vcs-ref     | A 'reference' within the version control repository; e.g. a git commit, or a subversion branch|
 | authoritative-source-url | The authoritative location in which the image is published|
 | distribution-scope  | Intended scope of distribution for image (see below for possible values)|
 | changelog-url | URL of a page containing release notes for the image|
 | config       | A namespace for defining the application configuration (see below for possible values)|

1. Possible values of distribution-scope field

 |Name         | Description |
 |-------------|-------------|
 | private     | No public redistribution intended|
 | authoritative-source-only  | Redistribution only from the source listed in the 'authoritative-source-url' label|
 | restricted  | Redistribution only with permission|
 | public      | No redistribution limits beyond licenses|

1. Possible values of config field. Application configuration may be defined or hinted using the config label. This allows documentation and tooling to provide parameterization control. Currently, environment variables are the only parameter, with three attributes.

 |Name         | Description |
 |-------------|-------------|
 | config.env.KEY             | Default value. Use empty string "" if no default.|
 | config.env.KEY.description | Description|
 | config.env.KEY.required    | Required boolean, true or false.|
Example:

        LABEL config.env.foo=bar \
              config.env.foo.description="Lorem ipsum dolor sit amet" \
              config.env.foo.required=true

1. Custom labels may be defined by a namespace prefix using reverse DNS notation of a domain controlled by the author. For example, `com.redhat.access`.

        LABEL io.openshift.expose-services="8080:http" \
              io.openshift.tags="builder,nodejs,nodejs010"


## Details on Labels

## `authoritative-source-url`
* The authoritative location the image is published by the owner.
* In combination with the 'name' label, this tells a user where to go and look for official updates and current versions of the image, regardless of the local tags.

## `distribution-scope`
* The intended scope of distribution for the image.
* Allows a user to define the intended scope of distribution. This addresses the end-user case of internal builds vs. public content and the use case of a vendor like Red Hat that provides content streams under subscription agreements - which is different from the license(s) of the image content.
* In combination with the 'authoritative-source-url' and 'name' labels allows automatic redirect to the authoritative source.

## Signing Server Metadata "sigstore" Image

Signing server metadata may be served by a special image in a repository. The image shall be named "sigstore" and contain the following labels, all required:

| Name               | Description                            |
|--------------------|----------------------------------------|
| sigstore-url       | The signature server URL, including port |
| sigstore-type      | Signature server type, either "docker" (static web server) or "atomic" (Atomic Registry and OpenShift API) |
| pubkey-id          | The public key ID in the form of an email address |
| pubkey-fingerprint | The public key fingerprint, typically a long hexidecimal string |
| pubkey-url         | The URL to download the public key |

Example Dockerfile:

```
FROM scratch
LABEL sigstore-url="sigstore.example.com:8443" \
      sigstore-type="docker" \
      pubkey-id="security@example.com" \
      pubkey-fingerprint="B3B04F8CF186436EF8F1CDAD7C6ACC9EE3A31016" \
      pubkey-url="https://pgp.mit.edu/pks/lookup?op=get&search=0xFD5EB4DB480717ED"
```
