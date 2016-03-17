# Nulecule Specification Metadata

The Nulecule Specification has been a place to experiment with packaging
multi container applications. To open the opportunity to generate more of the
meta information used within a Nulecule file, a set of LABLEs to be used on
container images is proposed.

## Application runtime configuration

The following table contains a list of LABELs that could be used to make
statements about the runtime information needed by a container. These LABELs
can be reused by some developer tooling to generate Nulecule file snippets.

| Name           | Description                        |
|----------------|------------------------------------|
| io.projectatomic.nulecule.environment.required | Environment variables that are required by the container (eg its entrypoint) to work correctly |
| io.projectatomic.nulecule.environment.optional | Environment variables that are optional and could influence the container behavior |
| io.projectatomic.nulecule.volume.* | a LABEL giving additional information about a VOLUME, this could be used to generate Kubernetes PersistentVolumeClaims |

Example usage could be seen at https://github.com/goern/postgresql/blob/feature/enhanced-labels/9.4/Dockerfile.rhel7
