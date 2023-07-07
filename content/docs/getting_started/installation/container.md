---
weight: 20
---

# Container

This guide walks you through getting GoToSocial up and running using the official container images we publish. In this case we'll be using the Docker runtime directly through [Docker Compose](https://docs.docker.com/compose) together with SQLite as the database.

You can also run GoToSocial using a container orchestration system such as [Kubernetes](https://kubernetes.io/) or [Nomad](https://www.nomadproject.io/), but that is beyond the scope of this guide.

## Create a Working Directory

You need a working directory in which your docker-compose file will be located, and a directory for GoToSocial to store data in, so create these directories with the following command:

```bash
mkdir -p ~/gotosocial/data
```

Now change to the working directory you created:

```bash
cd ~/gotosocial
```

## Get the latest docker-compose.yaml

Use `wget` to download the latest [docker-compose.yaml](https://raw.githubusercontent.com/superseriousbusiness/gotosocial/main/example/docker-compose/docker-compose.yaml) example, which we'll customize for our needs:

```bash
wget https://raw.githubusercontent.com/superseriousbusiness/gotosocial/main/example/docker-compose/docker-compose.yaml
```

## Edit the docker-compose.yaml

Because GoToSocial can be configured using [Environment Variables](../../../configuration#environment-variables), we can skip mounting a config.yaml file into the container, to make our configuration simpler. We just need to edit the docker-compose.yaml file to change a few things.

First open the docker-compose.yaml file in your editor of choice. For example:

```bash
nano docker-compose.yaml
```

### Version

If desired, update the GoToSocial Docker image tag to the version of GtS you want to use.

`latest`   - the default. This points to the latest stable release of GoToSocial.

`snapshot` - points to whatever code is currently on the main branch. Not guaranteed to be stable, and may often be broken. Use with caution.

{{< hint info >}}
**Tip**

You can also replace `latest` with a specific GoToSocial version number. This is recommended when you want to make sure that you don't update your GoToSocial version by accident, which can cause problems. The list of releases can be found [right here](https://github.com/superseriousbusiness/gotosocial/releases), with the newest release at the top. Replace `latest` in the docker-compose.yaml with the number of the desired release (without the leading `v` or trailing version name).
{{</hint>}}

### Host

Change the `GTS_HOST` environment variable to the domain you are running GoToSocial on.

### User (optional / probably not necessary)

By default, Dockerized GoToSocial runs with Linux user/group `1000:1000`, which is fine in most cases. If you want to run as a different user/group, you should change the `user` field in the docker-compose.yaml accordingly.

For example, let's say you created the `~/gotosocial/data` directory for a user with id `1001`, and group id `1001`. If you now try to run GoToSocial without changing the `user` field, it will get a permissions error trying to open its database file in the directory. In this case, you would have to change the `user` field of the docker compose file to `1001:1001`.

### LetsEncrypt (optional)

If you want to use [LetsEncrypt](../../../configuration/tls) for TLS certificates (https), you should also:

1. Change the value of `GTS_LETSENCRYPT_ENABLED` to `"true"`.
2. Remove the `#` before `- "80:80"` in the `ports` section.
3. (Optional) Set `GTS_LETSENCRYPT_EMAIL_ADDRESS` to a valid email address to receive certificate expiry warnings etc.

## Start GoToSocial

With those small changes out of the way, you can now start GoToSocial with the following command:

```shell
docker-compose up -d
```

After running this command, you should get an output like:

```text
Creating network "gotosocial_gotosocial" with the default driver
Creating gotosocial ... done
```

If you want to follow the logs of GoToSocial, you can use:

```bash
docker logs -f gotosocial
```

If everything is OK, you should see something similar to the following:

```text
time=2022-04-19T09:48:35Z level=info msg=connected to SQLITE database
time=2022-04-19T09:48:35Z level=info msg=MIGRATED DATABASE TO group #1 (20211113114307, 20220214175650, 20220305130328, 20220315160814) func=doMigration
time=2022-04-19T09:48:36Z level=info msg=instance account example.org CREATED with id 01EXX0TJ9PPPXF2C4N2MMMVK50
time=2022-04-19T09:48:36Z level=info msg=created instance instance example.org with id 01PQT31C7BZJ1Q2Z4BMEV90ZCV
time=2022-04-19T09:48:36Z level=info msg=media manager cron logger: start[]
time=2022-04-19T09:48:36Z level=info msg=media manager cron logger: schedule[now 2022-04-19 09:48:36.096127852 +0000 UTC entry 1 next 2022-04-20 00:00:00 +0000 UTC]
time=2022-04-19T09:48:36Z level=info msg=started media manager remote cache cleanup job: will run next at 2022-04-20 00:00:00 +0000 UTC
time=2022-04-19T09:48:36Z level=info msg=listening on 0.0.0.0:8080
```

## Create your first User

Now that GoToSocial is running, you should create at least a user for yourself. How to do so is documented in our [Creating users](../../user_creation) guide.

### Done

GoToSocial should now be running on your machine! To verify this, open your browser navigate to whatever you set as your `GTS_HOST` value. You should see the GoToSocial landing page. Well done!

## (Optional) Reverse Proxy

If you want to run other webservers on port 443 or want to add an additional layer of security you might want to use a [reverse proxy](../../reverse_proxy). We have guides available for a couple of popular open source options and will gladly take pull requests to add more.
