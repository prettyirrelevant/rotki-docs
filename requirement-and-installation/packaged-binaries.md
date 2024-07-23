# Packaged Binaries

## Verifying Integrity

Starting with the release of v1.6.2, rotki releases now include the SHA512 sum of the packaged binaries. You can download the file containing the hash along with the binary from the release page.

The files containing the hash have the same name as their respective binaries followed by a `.sha512` extension.

To verify a binary, download the `.sha512` file for it. Then, proceed with the verification:

### On Linux:

```sh
cd ~/Downloads
sha512sum -c rotki-linux_x86_64-vx.x.x.AppImage.sha512
# rotki-linux_x86_64-vx.x.x.AppImage: OK
```

### On macOS:

```sh
cd ~/Downloads
shasum -a 512 -c rotki-darwin-vx.x.x.dmg.sha512
# rotki-darwin-vx.x.x.dmg: OK
```

If there is any problem with the checksum verification, you should see an error similar to the following:

```sh
rotki-linux_x86_64-vx.x.x.AppImage: FAILED
sha512sum: WARNING: 1 computed checksum did NOT match
```

### On Windows:

First, open the Command Prompt and navigate to the `Downloads` directory where the rotki executable and SHA512 files were downloaded:

```sh
cd Downloads
certutil -hashfile rotki-win32-vx.x.x.exe SHA512
```

After running the command, you should see the command's output:

```sh
SHA512 hash of rotki-win32-v1.6.2.exe:
a3e0d79724460f642245774ba1af4c7116dfde56503d134c688f406afff5339f70a84a0bdb2556bc0785931b11e2447e3ffcd116cdec9e8a50382ec0165788b4
CertUtil: -hashfile command completed successfully.
```

To verify that the generated hash matches, open the `rotki-win32-vx.x.x.exe.sha512` file with Notepad or any other text editor and ensure that the hash there matches the one generated by the certutil command.

> **Note:** Depending on your OS, you can check the details by right-clicking on the app:
>
> - **Windows:** The signature will display "Rotki Solutions GmbH".
> - **macOS:** In the "Get Info" window, the copyright should read "Rotki Solutions GmbH".

It is also possible to verify the integrity of a binary and obtain the workflow that created it using GitHub attestations. You can find more information in the [GitHub docs](https://cli.github.com/manual/gh_attestation_verify).

Example:

```sh
$ gh attestation verify ~/Downloads/rotki134.dmg --repo rotki/rotki
Loaded digest sha256:12bb7aa1cf8d5b568f925e7c772b946a29efaf66ae030026a1f113da528c8e39 for file:///Users/yabirgb/Downloads/rotki134.dmg
Loaded 1 attestation from GitHub API
✓ Verification succeeded!

sha256:12bb7aa1cf8d5b568f925e7c772b946a29efaf66ae030026a1f113da528c8e39 was attested by:
REPO         PREDICATE_TYPE                  WORKFLOW
rotki/rotki  https://slsa.dev/provenance/v1  .github/workflows/rotki_release.yaml@refs/tags/v1.34.0
```

## Linux

Go to the [releases](https://github.com/rotki/rotki/releases) page and download the `linux-x64` AppImage from the [latest release](https://github.com/rotki/rotki/releases/latest), or the `.tar.xz` file, whichever you prefer.

If you downloaded the AppImage, give it executable permission:

```sh
chmod +x rotki-linux_x86_64-vx.x.x.AppImage
```

Replace `vx.x.x` with the proper version, then run it.

If you downloaded the tar file, unzip it in a directory of your choice. In the root directory of the unzipped archive, there is a `rotki` executable. Run it via the terminal to start rotki.

### Troubleshooting

If you encounter any issues when starting the application or during its usage, please open an issue on GitHub and include all logs found in `~/.config/rotki/logs`.

## macOS

### Homebrew

```sh
brew install --cask rotki
```

### Manual Installation

Go to the [releases](https://github.com/rotki/rotki/releases) page and download the `darwin-x64` dmg package from the [latest release](https://github.com/rotki/rotki/releases/latest).

> **Note:** rotki's minimum supported macOS version is Mojave (10.14). If you can't upgrade to Mojave (hardware older than 2012), either buy new Mac hardware or switch to Linux or Windows. This limitation is due to Apple's policy.

Click on the dmg installer and when prompted, drag the rotki logo to your Applications folder. This will install the application. Once finished, you can select rotki from your Applications folder and launch it.

If your macOS version does not allow you to open the application, you will need to go to your OS settings and insert an exception for rotki to allow it to run. [Here](https://ccm.net/faq/29619-mac-os-x-run-apps-downloaded-from-unknown-sources) is a guide.

### Troubleshooting

If you encounter any issues when starting the application or during its usage, please open an issue on GitHub and include all logs found in `~/Library/Application Support/rotki/logs`.

## Windows

Go to the [releases](https://github.com/rotki/rotki/releases) page and download the `win32-x64` package from the [latest release](https://github.com/rotki/rotki/releases/latest).

Unzip it in a folder of your choice. In the root directory of the unzipped archive, there is a `rotki` executable. Double-click it to start rotki.

### Troubleshooting

If you encounter "The python backend crashed" or any other error, please run the executable via the Command Prompt and provide us with the output visible in the prompt to help us debug your issue.

You should also include all logs found in `<WindowsDrive>:\Users\<User>\AppData\Roaming\rotki\logs\` (`%APPDATA%\rotki\logs`).

## Docker

Since v1.11.0, rotki provides official Docker images of nightly versions and releases.

> **Note:** Versions up to v1.13.2 report a dev version instead of the actual release due to an issue during the build process.

You can find all the available Docker images on [DockerHub](https://hub.docker.com/r/rotki/rotki).

:::warning
It is advisable to run the Docker image in a secure environment. For example, running the Docker image on the host machine and accessing it only via NAT. Do not expose the Docker image directly to public or local networks to avoid security issues and unauthorized access to your data.
:::

To get the latest version of rotki, pull the `latest` tag:

```sh
docker pull rotki/rotki:latest
```

To start a container based on the latest rotki image, run the following:

```sh
docker run -d --name rotki \
    -p 8084:80 \
    -v $HOME/.rotki/data:/data \
    -v $HOME/.rotki/logs:/logs \
    rotki/rotki:latest
```

> **Warning:** On Linux, the mounted volume folders for data and logs will be owned by the `root` user. If the owner of these folders changes to another user, they will become inaccessible by the container, resulting in 500 errors when accessing rotki in the container. To fix this issue, change the directory owner back to `root`.

This will start a new container that stores the data and logs in a `.rotki` directory under the user’s home directory. You will be able to find your account data (databases, etc.) under the `.rotki/data` directory. If port `8084` is busy on your machine, choose any other available port.

At this point, the rotki Docker container should be running, and you should be able to access the rotki frontend by opening your browser and navigating to `http://localhost:8084`. You should see the rotki login screen.

### Configuring the Backend in Docker

It is possible to change the configuration of the backend in the Docker container using two different approaches.

The first approach includes mounting a config volume:

```sh
docker run -d --name rotki \
    -p 8084:80 \
    -v $HOME/.rotki/data:/data \
    -v $HOME/.rotki/logs:/logs \
    -v $HOME/.rotki/config:/config \
    rotki/rotki:latest
```

Create a `rotki_config.json` file and place it inside the `$HOME/.rotki/config` directory.

Example:

```json
{
  "loglevel": "info",
  "logfromothermodules": true,
  "sleep-secs": 22,
  "max_size_in_mb_all_logs": 550,
  "max_logfiles_num":

3,
  "sqlite_instructions": 0
}
```

The above list contains all the supported configuration options, but you can also specify only the ones you want to change. This approach allows you to modify the config and restart the container to apply them.

The second approach uses environment variables during container creation:

```sh
docker run -d --name rotki \
    -p 8084:80 \
    -v $HOME/.rotki/data:/data \
    -v $HOME/.rotki/logs:/logs \
    -e LOGLEVEL=debug \
    rotki/rotki:latest
```

The supported environment variables are `LOGLEVEL`, `LOGFROMOTHERMODULES`, `MAX_SIZE_IN_MB_ALL_LOGS`, `MAX_LOGFILES_NUM`, and `SQLITE_INSTRUCTIONS`. Changing these variables requires recreating the container with the new parameters.

> **Warning:** When using both a config file and environment variables, the config file will take precedence.

### Setting Timezone in Docker

To set the timezone in the Docker environment, use the `-e TZ=Timezone` option when starting the container:

```sh
docker run -d --name rotki \
    -p 8084:80 \
    -v $HOME/.rotki/data:/data \
    -v $HOME/.rotki/logs:/logs \
    -e TZ=America/New_York \
    rotki/rotki:latest
```

You can find all TimeZone Databases on [Wikipedia](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones).

### Updating to a Newer Version

When a new rotki version is released, follow these steps to upgrade to the newest version:

1. Stop the rotki container from running:

   ```sh
   docker stop rotki
   ```

2. Remove the old container:
   ```sh
   docker rm rotki
   ```

Since your data is stored in volumes on the host machine, deleting the container will not affect your data.

3. Pull the latest tag from Docker Hub:

   ```sh
   docker pull rotki/rotki:latest
   ```

4. Create a new container:
   ```sh
   docker run -d --name rotki \
       -p 8084:80 \
       -v $HOME/.rotki/data:/data \
       -v $HOME/.rotki/logs:/logs \
       rotki/rotki:latest
   ```

Ensure that the volumes from the previous container are used again. If you don’t use the same volumes, your old accounts and data will be unavailable.

## Docker Compose

If you prefer to use Docker Compose, a `docker-compose.yml` template is provided below for convenience.

### Using Docker Over a Public Network

Since rotki was not designed to run over a public network, it is [generally advised](#docker) to avoid directly exposing your rotki instance over a public network.

If you still want to access rotki over a public network, it is suggested to use at least a proxy with basic authentication as an intermediate layer of security. With this setup, anyone who visits your public domain will need to authenticate with the basic auth user and password before accessing rotki's user interface.

Additionally, Traefik will be set up to automatically request and set up an SSL certificate to ensure that your connection to your rotki instance is properly encrypted using TLS.

Assuming you have a server provisioned and a domain `rotki.example.com` already set to the server's IP address:

1. Create a basic auth user with a password. Use the Apache utilities to create a secure password using `bcrypt` for your user:

   ```sh
   htpasswd -cB ~/.rotki/.htpasswd user
   ```

2. Create an `.env` file in the same directory where your `docker-compose.yml` will be. In the env file, set your authentication user, the domain, and the email where you will receive notifications about the status of your domain:

   ```sh
   AUTH_USER=username
   FQDN=rotki.example.com
   LETSENCRYPT_EMAIL=user@example.com
   ```

3. Create the `docker-compose.yml`:

   ```yaml
   version: '3.11'

   services:
     proxy:
       image: traefik:2.9
       restart: always
       command:
         - --global.sendAnonymousUsage=false
         - --providers.docker
         - --providers.docker.exposedByDefault=false
         - '--entrypoints.web.address=:80'
         - '--entrypoints.websecure.address=:443'
         - --certificatesresolvers.le.acme.httpchallenge=true
         - --certificatesresolvers.le.acme.httpchallenge.entrypoint=web
         - '--certificatesresolvers.le.acme.email=${LETSENCRYPT_EMAIL}'
         - --certificatesresolvers.le.acme.storage=/etc/acme/acme.json
       ports:
         - '80:80'
         - '443:443'
       networks:
         - rotki-net
       volumes:
         - $HOME/.rotki/.htpasswd:/auth/.htpasswd
         - $HOME/.rotki/acme/:/etc/acme/
         - /var/run/docker.sock:/var/run/docker.sock:ro

     rotki:
       environment:
         - TZ=Europe/Berlin # TimeZone Databases on Wikipedia: https://en.wikipedia.org/wiki/List_of_tz_database_time_zones
       image: rotki/rotki:latest
       networks:
         - rotki-net
       volumes:
         - $HOME/.rotki/data:/data
         - $HOME/.rotki/logs:/logs
       labels:
         - traefik.enable=true
         - traefik.http.services.rotki.loadbalancer.server.port=80
         - traefik.http.middlewares.redirect.redirectscheme.scheme=https
         - 'traefik.http.middlewares.rotki-auth.basicauth.realm=${AUTH_USER}'
         - traefik.http.middlewares.rotki-auth.basicauth.usersfile=/auth/.htpasswd
         - 'traefik.http.routers.rotki-insecure.rule=Host(`${FQDN}`)'
         - traefik.http.routers.rotki-insecure.middlewares=redirect
         - 'traefik.http.routers.rotki.rule=Host(`${FQDN}`)'
         - traefik.http.routers.rotki.middlewares=rotki-auth
         - traefik.http.routers.rotki.entrypoints=websecure
         - traefik.http.routers.rotki.tls.certresolver=le

   networks:
     rotki-net:
   ```

4. Run:
   ```sh
   docker-compose up -d
   ```

With this setup, Traefik will act as a proxy with basic authentication that will protect your rotki instance interface from unauthorized access. When navigating to `http://rotki.example.com`, you will get a browser popup where you have to enter the `AUTH_USER` and the password you created with `htpasswd`.

After passing the basic authentication step, you will be greeted with the familiar login page of rotki.

> **Note:** As a bonus, you should now be able to also access rotki from your mobile phone.

### Using Docker on a Private Network

**Using Docker Defined Volume:**

```yaml
version: '3.7'
services:
  rotki:
    environment:
      - TZ=America/Chicago # TimeZone Databases on Wikipedia: https://en.wikipedia.org/wiki/List_of_tz_database_time_zones
    image: rotki/rotki:latest
    ports:
      - '8084:80' # Container exposes port 80, 8084 can be any port of your choosing.
    networks:
      - rotki-net
    volumes:
      - rotki-data:/data
      - rotki-logs:/logs
volumes:
  rotki-data:
  rotki-logs:
networks:
  rotki-net:
```

**Using $HOME (or whichever path to your local data) Defined Directed Volume:**

```yaml
version: '3.7'
services:
  rotki:
    environment:
      - TZ=America/Chicago # TimeZone Databases on Wikipedia: https://en.wikipedia.org/wiki/List_of_tz_database_time_zones
    image: rotki/rotki:latest
    ports:
      - '8084:80' # Container exposes port 80, 8084 can be any port of your choosing.
    networks:
      - rotki-net
    volumes:
      - $HOME/.rotki/data:/data
      - $HOME/.rotki/logs:/logs
networks:
  rotki-net:
```

## Moving the Accounts from the Desktop Application

If you would like to move your accounts from the rotki application to Docker, you can do so by copying the data directory of the application to the Docker data volume.

You can find where the rotki application data is stored in the [rotki data directory](/usage-guides/data-directory.html#rotki-data-directory).

To move your existing accounts from the application, copy the contents of the application data directory to the data volume mount point.

Assuming you used the directory from the example, to move a specific account on Linux, run:

```sh
sudo cp -R ~/.local/share/rotki/data/username ~/.rotki/data/username
```

This will copy the `username` account to the data volume and make it accessible through the Docker container.

## Troubleshooting

If you encounter any issues when starting the application or during its usage, please open an issue on GitHub and include all logs found in `$HOME/.rotki/logs/rotki.log` or the custom volume you used during the container