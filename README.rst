
conan is a suite of analysis utilities and command line tools for Docker images,
their layers and how these relate to each other as well as Dockerfiles.

Note: this is a work in progress
 
conan provides utilities to:
 - identify Docker images in a file system, its layers and the related metadata.
 - given a Docker image, collect and report its metadata.
 - given a Docker image, extract the layers using to rebuild what would a runtime
   rootfs would look like
 - find and parse Dockerfiles
 - find how Dockerfiles relate to actual images and their layers.
 
 
Quick start
-----------

- Get Python 2.7. or 3.6
- Check out a clone or download of conan, then run: `./configure`.
- Then run `bin/conan -h` for help.

 
Container image formats
-----------------------

conan handles the formats of Docker images as created by the `docker save` command.
There are three versions for this Docker image format. 
The latest v1.2 is a minor update to v1.1.

- v1.1 provides improved and richer metadata over v1.0 with a top level manifest.json
  file and a Config file for each image with full layer history and ordeing. It also
  use checksum for enhanced security and traceability of images and layers.

- v1.0 uses a simple `repositories` meta file and requires infering the ordering of
  the layers in an image based on each individual layer `json` meta file. This
  format is no longer support in the latest version of Conan.

- All V1.x formats use the same storage format for layers e.g the layer format V1.0
  where each layer is stored in a sub-directories named after the layer id. 
  Each of this directories contains a "layer.tar" tarball with the layer payload, 
  a "json" JSON metadata file describing the layer and a "VERSION" file describing
  the layer format version. Each tarball represents a slice or diff of the image
  root file system using the AUFS conventions.

At runtime, in a sequence of layers of an image, each root filesystem slice of a 
layer is "layered" on top of each other from the root bottom layer to the latest
layer (or selected tagged layer) using a union file system (e.g. AUFS).
In AUFS, any file or directory prefixed with .wh. are "white outs" files deleting
files in the underlying layers.

See the imaged specifications saved in docs/references/


Internal data model
-------------------
- Registry: this is a collection of Image Repositories (related or not).
- Repository: this is a collection of related Images, stored in the same base directory.
- Image: this is a runnable image composed of metadata and a sequence of layers.
- Layer: this is a slice of an image root filesystem with a payload and metadata

We skip entirely Registry and Repository and only expose Image and Layer


Plans
-----
 - in progress: support OCI image layout
 - in progress: fetch Image from remote registry (already available, but not integrated)
 - in progress: collect inventories of packages and files installed in an image or layer 
   (implemented using a provided callable)
 - future: given collected Dockerfiles and Images, build a graph
   of the relationships between all these elements.

And if you wonder about the name, conan stands for CONtainer ANalysis.