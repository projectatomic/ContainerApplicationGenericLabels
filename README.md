# Standard Container/Application Identifiers
Default Containerized Application Labels/Annotations/Ids used to document the application and/or image

With the introduction of the [Atomic command](http://developerblog.redhat.com/2015/04/21/introducing-the-atomic-command/), we are accessing container image meta data to describe the purpose of the image or the application defined by the image.

We would like to get consensus on default names.  Proposals have been made to namespace the label names defined
in Docker.  

But we believe their should be some generic toplevel names defined that are not vendor specific, or
distribution specific.  

If you would like to see additional names and descriptions added please open issues and send pull requests to update this readme.

The following types of data are being considered:

1. Labels used to describe how to use the application/image

 | Name        | Description                            |
 |-------------|----------------------------------------|
 | RUN         | Command to run the image|
 | UNINSTALL   | Command to uninstall the image|
 | INSTALL     | Command to install the image|
 | STOP        | Command to execute before stopping container|

2. Labels Names used to describe the application/image

 | Name        | Description                            |
 |-------------|----------------------------------------|
 | Name        | Name of the Image|
 | Version     | Version of the image|
 | Release     | Release Number for this version|
 | Architecture| Architecture for the image|
 | License     | Image License|
 | BuildDate  | Date/Time image was built|
 | BuildHost  | Host Image was built on|
 | Vendor      | Owner of the image| 
 | URL         | Url with more information on the image|
 | Summary     | Short Description of the image|
 | Description | Detailed description of the image|
 | VcsType    | The type of version control used by the container source. Generally one of git, hg, svn, bzr, cvs|
 | VcsUrl     | URL of the version control repository|
 | VcsRef     | A 'reference' within the version control repository; e.g. a git commit, or a subversion branch|
