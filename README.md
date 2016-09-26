# tmate-slave-build
This repository contains a Dockerfile capable of generating a build environment
for the `tmate-slave` project. `tmate-slave` is the server component for `tmate`.
It goes without saying, trusting the public `tmate` servers could be seen as a
security risk.

The docker image generated by the Dockerfile is meant only for building the
`tmate-slave` binary. You should then copy this binary in to another container
meant to just run the service.

Why have two separate steps/containers? I'm not a huge of fat containers, and
the `tmate-slave` project has a dependency on `glibc` meaning using Alpine is
not an option. By trying to use a two-container approach, I'm hopeful to build
a smaller container than if all of the build dependencies were included.

## Building
Building the docker image for your build environment is easy. Check out this
repository somewhere and then run the following:

```BASH
docker build -t theckman/tmate-slave-build .
```

At the end you should have a docker container capable of building `tmate-slave`.

## Using the build env
Once you have a container to use for building `tmate-slave`, you can tell it to
do so:

```BASH
docker run -v $(pwd)/nexus:/nexus heckman/tmate-slave-build
```

Because we're building a binary that needs to be shipped somewhere, we're using
the `nexus` directory to transfer data in and out of the container. This repo
includes a `nexus` directory to make it easy to use the resulting container.

## Considerations
The binary being built is not statically linked, meaning you'll need to use it
within a container that has compatible dynamic libraries installed. To make it
easy, you should just use a `debian:sid` base image. That said, `debian:sid` is
unstable so that may not always be true.

We are building against `debian:sid` to make sure we get the latest versions of
the build dependencies we need. It's possible that `debian:stretch` has the
proper versions, but it wasn't tested.

## License
This Dockerfile and the associated scripts are released under the MIT License.
Please see the [LICENSE](https://github.com/theckman/tmate-slave-build/blob/master/LICENSE)
file for the full content of the license.
