# Lesson 23/30 — Containerization

## Concepts
Containerization packages applications and dependencies into reproducible isolated environments. Linux containers share a host kernel rather than running independent guest kernels like traditional VMs. Containers tend to be lightweight, but isolation depends on configuration and the host kernel.

Docker Engine manages images and containers. A Dockerfile defines image construction; an image is a reusable layered template; a container is an instance with its own writable layer. Docker Hub is an image registry. Docker Compose defines multi-container applications; Kubernetes manages containerized workloads at scale. Modern Docker uses containerd/runc rather than requiring LXC.

## Dockerfile and Apache example
The course illustrates an Ubuntu-based Apache/SSH image. Its hardcoded password and unrestricted passwordless sudo are unsuitable for real deployments. This supplemental local-only Apache example avoids those settings:

~~~dockerfile
FROM ubuntu:22.04
RUN apt-get update && apt-get install -y apache2 && rm -rf /var/lib/apt/lists/*
EXPOSE 80
CMD ["apache2ctl", "-D", "FOREGROUND"]
~~~
FROM chooses a base image; RUN executes build-time instructions; EXPOSE documents the service port but does not publish it; CMD sets the default runtime command.

~~~bash
docker build -t fs-docker .
docker run -d -p 127.0.0.1:8080:80 fs-docker
~~~
build creates an image; -t tags it; dot selects the current build context. Use lowercase repository names rather than the lesson's uppercase example. run creates a container; -d detaches; -p publishes the container's port 80 to host loopback port 8080. This is a supplemental example, not an executed deployment. Alternative: use a maintained official web-server image.

## Management
| Command | Purpose |
| --- | --- |
| docker ps | Running containers |
| docker ps -a | All containers |
| docker images | Local images |
| docker stop NAME | Stop a container |
| docker start NAME | Start an existing container |
| docker restart NAME | Restart a container |
| docker rm NAME | Remove a container |
| docker rmi IMAGE | Remove an image |
| docker logs NAME | Inspect logs |
| docker inspect NAME | Inspect configuration |
| docker stats | Monitor resources |

Removal operations can destroy data. Docker group access on Linux is highly privileged. Do not blindly run the course installation script on an existing Docker Desktop/WSL setup.

## Persistence and Compose
A container's writable layer normally survives stopping and restarting the same container, but is removed when the container is removed. Image contents are not automatically changed by container writes. Volumes retain data independently when not explicitly deleted. Bind mounts expose selected host paths and require careful access control.
~~~bash
docker volume create my-data
docker volume ls
~~~
Compose manages related services using a YAML file. A hypothetical UniTrack deployment could use separate web-server, Flask application and database services; this is not a claim about its existing deployment. Database volumes still require consistent backups.

~~~bash
docker compose up -d
docker compose ps
docker compose logs
docker compose stop
docker compose down
~~~
up creates/starts configured services; -d detaches; ps and logs inspect; stop stops services; down removes service containers and networks. Avoid deleting volumes unintentionally.

## LXC
LXC focuses on isolated Linux system environments. Docker focuses primarily on application packaging. Both share the host kernel.
~~~bash
sudo apt install lxc
sudo lxc-create -n linuxcontainer -t ubuntu
lxc-ls
sudo lxc-info -n linuxcontainer
sudo lxc-start -n linuxcontainer
sudo lxc-stop -n linuxcontainer
sudo lxc-attach -n linuxcontainer
sudo lxc-console -n linuxcontainer
~~~
-n selects a container name; -t selects a template; attach opens a shell or executes a command; console connects to its console. These are lesson examples, not executed actions. Classic privileged LXC container settings typically live in /var/lib/lxc/NAME/config. lxc-config queries global settings; per-container network and bind mount settings belong in the container configuration. LXC is distinct from LXD.

## Namespaces and cgroups
Namespaces isolate views of system resources: PID (process IDs), NET (network), MNT (mounts), UTS (hostname), IPC (interprocess communication), USER (user/group IDs). Cgroups account for and restrict resource usage. The lesson uses cgroup v1 cpu.shares and memory.limit_in_bytes. CPU shares are relative scheduling weights, not an absolute CPU cap. cgroup v2 uses cpu.weight, cpu.max, and memory.max.

Supplemental Docker example:
~~~bash
docker run --memory=512m --cpus=1 nginx
~~~
--memory limits memory; --cpus sets a CPU quota equivalent to one CPU. Not executed.

## Security
Use maintained trusted images, least privilege, restricted ports, strong authentication, minimal host mounts, protected secrets and resource limits. Keep the kernel and runtime updated. Container isolation alone is insufficient for real malware analysis. Never commit private keys, passwords, or sensitive host configurations to a public repository.

## Optional practice and verification
The learner already uses Windows 11, Docker Desktop and WSL Ubuntu. Check existing installation before attempting any new installation:
~~~powershell
docker version
docker run hello-world
~~~
version reports client/server information; run downloads the test image if needed, creates a container and runs its demo. Expected message: Hello from Docker! No command was executed by the learner in this lesson yet.

The lesson lists nine optional LXC exercises: creation, networking, images, resource limits, management commands, web services, SSH, persistence and controlled software testing. None was newly performed. Section 23 has no mandatory questions. Section 22 was followed by Section 23; individual platform acceptance was not explicitly shown.

## Further reading
- https://docs.docker.com/engine/storage/drivers/
- https://docs.docker.com/engine/storage/volumes/
- https://docs.docker.com/engine/daemon/alternative-runtimes/
- https://www.kernel.org/doc/html/v5.0/admin-guide/cgroup-v2.html

Independent English study notes based on HTB Academy, with supplemental technical clarifications.
