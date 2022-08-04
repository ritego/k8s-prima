# K8s

## Prima

### Containers
1. Application programs are typically comprised of a language runtime, libraries, and your source code. Container images bundle a program and its dependencies into a single artifact under a root filesystem.

2. The most popular container image format is the Docker image format, which has been standardized by the OpenContainer Initiative to the OCI image format. Kubernetes supports both Docker- and OCI-compatible images via Docker and other runtimes. Docker images also include additional metadata used by a container runtime to start a running application instance based on the contents of the container image.

3. The image isn’t a single file but rather a specification for a manifest file that points to other files. It is made up of a series of filesystem layers. Each layer adds, removes, or modifies files from the preceding layer in the filesystem. This is an example of an overlay filesystem. During runtime,there are a variety of different concrete implementations of such filesystems, including aufs, overlay, and overlay2.

4. Container images are typically combined with a container configuration file.

5. Containers fall into two main categories:
- System containers - System containers seek to mimic virtual machines and often run a full boot process. They often include a set of system services typically found in a VM, such as ssh, cron, and syslog.
- Application containers - commonly run a single program.

6. docker, containerd, cri-o, gVisor, Kata