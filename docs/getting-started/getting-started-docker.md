### GraalVM Docker Containers

The official Docker images for GraalVM CE are available from the Docker Hub:
[https://hub.docker.com/r/oracle/graalvm-ce/](https://hub.docker.com/r/oracle/graalvm-ce/).

If you want to use the Docker container with GraalVM CE use the `docker pull` command:
```
docker pull oracle/graalvm-ce:1.0.0-rc8
```

The image is based on Oracle Linux and has GraalVM CE downloaded, unzipped and made available.
It means that Java, JavaScript, Node and the LLVM interpreter are available out of the box.

You can start a container and enter the `bash` session with the following run command:
```
docker run -it oracle/graalvm-ce:1.0.0-rc8 bash
```

Check that `java`, `js` and other commands work as expected.
```
→ docker run -it oracle/graalvm-ce:1.0.0-rc8 bash
bash-4.2# java -version
openjdk version "1.8.0_172"
OpenJDK Runtime Environment (build 1.8.0_172-20180625212755.graaluser.jdk8u-src-tar-g-b11)
GraalVM 1.0.0-rc8 (build 25.71-b01-internal-jvmci-0.48, mixed mode)
bash-4.2# node
> 1 + 1
2
> quit()
bash-4.2# lli --version
Graal llvm 6.0.0 (GraalVM CE Native 1.0.0-rc8)
bash-4.2#
```

Please note that the image contains only the components immediately available in the GraalVM CE distribution.
However, the [Graal updater utility]({{ "/docs/reference-manual/graal-updater/" | relative_url}}) is on the path.
You can install support for additional languages like Ruby, R, or Python at will. For example, the following command
installs the Ruby support (the output below is truncated for brevity):

```
docker run -it oracle/graalvm-ce:1.0.0-rc8 bash
bash-4.2# gu install ruby
Downloading: Component catalog
Processing component archive: Component ruby
Downloading: Component ruby
[######              ]
...
```

If you want to mount a directory from the host system to have it locally available in the container,
use [Docker volumes](https://docs.docker.com/storage/volumes/#choose-the--v-or---mount-flag).

Here is a sample command that maps the `/absolute/path/to/dir/no/trailing/slash` directory from the host system to the `/path/inside/container` inside the container.

```
docker run -it -v /absolute/path/to/dir/no/trailing/slash:/path/inside/container oracle/graalvm-ce:1.0.0-rc8 bash
```

If you want to create docker images that contain GraalVM Ruby, R, or Python implementation, you can use dockerfiles like the example below, which uses `oracle/graalvm-ce:1.0.0-rc8` as the base image, installs Ruby support using the `gu` utility, then creates and runs a sample Ruby program.

```
FROM oracle/graalvm-ce:1.0.0-rc8
RUN gu install ruby
WORKDIR /workdir
RUN echo 'puts "Hello from Truffleruby!\nVersion: #{RUBY_DESCRIPTION}"' > app.rb
CMD ruby app.rb
```

If you put the above snippet in the `Dockerfile` in the current directory,
you can build and run it with the following commands.

```
docker build -t truffleruby-demo .
...
$ docker run -it --rm truffleruby-demo
Hello from Truffleruby!
Version: truffleruby 1.0.0-rc8, like ruby 2.4.4, GraalVM CE Native [x86_64-linux]
```