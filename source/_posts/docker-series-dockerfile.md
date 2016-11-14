---
title: Docker系列之Dockerfile使用方法
date: 2016/11/14
---


2.3.5 Dockerfile commands summary

Here's a quick summary of the few basic commands we used in our Dockerfile.

#### FROM
`FROM` starts the Dockerfile. It is a requirement that the Dockerfile must start with the FROM command. Images are created in layers, which means you can use another image as the base image for your own. The FROM command defines your base layer. As arguments, it takes the name of the image. Optionally, you can add the Docker Hub username of the maintainer and image version, in the format username/imagename:version.

#### RUN
`RUN` is used to build up the Image you're creating. For each RUN command, Docker will run the command then create a new layer of the image. This way you can roll back your image to previous states easily. The syntax for a RUN instruction is to place the full text of the shell command after the RUN (e.g., RUN mkdir /user/local/foo). This will automatically run in a /bin/sh shell. You can define a different shell like this: RUN /bin/bash -c 'mkdir /user/local/foo'

#### COPY
`COPY` copies local files into the container.

#### CMD

`CMD` defines the commands that will run on the Image at start-up. Unlike a RUN, this does not create a new layer for the Image, but simply runs the command. There can only be one CMD per a Dockerfile/Image. If you need to run multiple commands, the best way to do that is to have the CMD run a script. CMD requires that you tell it where to run the command, unlike RUN. So example CMD commands would be:

#### EXPOSE

`EXPOSE` opens ports in your image to allow communication to the outside world when it runs in a container.
