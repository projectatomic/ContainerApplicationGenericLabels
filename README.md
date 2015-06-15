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
 | license     | Image License|
 | build_date  | Date/Time image was built|
 | vendor      | Owner of the image| 
 | url         | Url with more information on the image|
 | summary     | Short Description of the image|
 | description | Detailed description of the image|
 | vcs_type    | The type of version control used by the container source. Generally one of git, hg, svn, bzr, cvs|
 | vcs_url     | URL of the version control repository|
 | vcs_ref     | A 'reference' within the version control repository; e.g. a git commit, or a subversion branch|
