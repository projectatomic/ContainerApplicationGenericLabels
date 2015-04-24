# Standard Container/Application Labels
Default Containerized Application Labels used to document the application and/or image

With the introduction of the [Atomic command](http://developerblog.redhat.com/2015/04/21/introducing-the-atomic-command/), we are accessing container image meta data to describe the purpose of the image or the application defined by the image.

We would like to get concensus on default names.  Proposals have been made to namespace the label names defined
in Docker.  If you would like to see additional names and desciptions added please open issues and send pull requests to update this readme.

But we believe their should be some generic toplevel names defined that are not vendor specific, or
distribution specific.  

We are looking at two types of data here. 

1. Labels used to describe how to use the application/image

 | Name        | Description                            |
 |-------------|----------------------------------------|
 | RUN         | Command required to run the image|
 | UNINSTALL   | Command Required to uninstall the image|
 | INSTALL     | Command Required to install the image|

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
 | Description | Detailed discription of the image|

