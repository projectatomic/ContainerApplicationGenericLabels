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
 | uninstall   | Command to uninstall the image|
 | install     | Command to install the image|
 | stop        | Command to execute before stopping container|
 | debug       | Command to run the image with debugging turned on|

2. Labels Names used to describe the application/image

 | Name        | Description                            |
 |-------------|----------------------------------------|
 | name        | Name of the Image|
 | version     | Version of the image|
 | release     | Release Number for this version|
 | architecture| Architecture for the image|
 | build-date  | Date/Time image was built|
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

3. Possible values of distribution-scope field

 |Name         | Description |
 |-------------|-------------|
 | private     | No public redistribution intended|
 | authoritative-source-only  | Redistribution only from the source listed in the 'authoritative-source-url' label|
 | restricted  | Redistribution only with permission|
 | public      | No redistribution limits beyond licenses|

4. Custom labels may be defined by a namespace prefix using reverse DNS notation of a domain controlled by the author. For example, `com.redhat.access`.

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
