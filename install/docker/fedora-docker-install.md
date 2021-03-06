# Install Docker for Kata Containers on Fedora

> **Note:**
>
> - This guide assumes you have
>   [already installed the Kata Containers packages](../fedora-installation-guide.md).

1. Install the latest version of Docker with the following commands:

   > **Notes:**
   >
   > - This step is only required if Docker is not installed on the system.
   > - Docker version 18.09 [removed devicemapper support](https://github.com/kata-containers/documentation/issues/373).
   >   If you wish to use a block based backend, see the options listed on https://github.com/kata-containers/documentation/issues/407.

   ```bash
   $ source /etc/os-release
   $ sudo dnf config-manager --add-repo https://download.docker.com/linux/fedora/docker-ce.repo
   $ sudo dnf makecache
   $ sudo dnf -y install docker-ce
   ```

   For more information on installing Docker please refer to the
   [Docker Guide](https://docs.docker.com/engine/installation/linux/fedora).

2. Configure Docker to use Kata Containers by default with one of the following methods:

   1. systemd

       ```bash
       $ sudo mkdir -p /etc/systemd/system/docker.service.d/
       $ cat <<EOF | sudo tee /etc/systemd/system/docker.service.d/kata-containers.conf
       [Service]
       ExecStart=
       ExecStart=/usr/bin/dockerd -D --add-runtime kata-runtime=/usr/bin/kata-runtime --default-runtime=kata-runtime
       EOF
       ```

   2. Docker `daemon.json`

       Add the following definitions to `/etc/docker/daemon.json`:

       ```json
       {
         "default-runtime": "kata-runtime",
         "runtimes": {
           "kata-runtime": {
             "path": "/usr/bin/kata-runtime"
           }
         }
       }
       ```

3. Restart the Docker systemd service with the following commands:

   ```bash
   $ sudo systemctl daemon-reload
   $ sudo systemctl restart docker
   ```

4. Run Kata Containers

   You are now ready to run Kata Containers:

   ```bash
   $ sudo docker run busybox uname -a
   ```

   The previous command shows details of the kernel version running inside the
   container, which is different to the host kernel version.
