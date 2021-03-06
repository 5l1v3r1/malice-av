malice-sophos
=============

[![License](http://img.shields.io/:license-mit-blue.svg)](http://doge.mit-license.org) [![Docker Stars](https://img.shields.io/docker/stars/malice/sophos.svg)](https://hub.docker.com/r/malice/sophos/) [![Docker Pulls](https://img.shields.io/docker/pulls/malice/sophos.svg)](https://hub.docker.com/r/malice/sophos/) [![Docker Image](https://img.shields.io/badge/docker image-1.902 GB-blue.svg)](https://hub.docker.com/r/malice/sophos/)

This repository contains a **Dockerfile** of [Sophos](https://www.sophos.com/en-us/products/free-tools/sophos-antivirus-for-linux.aspx) for [Docker](https://www.docker.io/)'s [trusted build](https://hub.docker.com/r/malice/sophos/) published to the public [DockerHub](https://index.docker.io/).

### Dependencies

-	[debian:jessie (*125 MB*\)](https://index.docker.io/_/debian/)

### Installation

1.	Install [Docker](https://www.docker.io/).
2.	Download [trusted build](https://hub.docker.com/r/malice/sophos/) from public [DockerHub](https://hub.docker.com): `docker pull malice/sophos`

### Usage

```
docker run --rm malice/sophos EICAR
```

#### Or link your own malware folder:

```bash
$ docker run --rm -v /path/to/malware:/malware:ro malice/sophos FILE

Usage: sophos [OPTIONS] COMMAND [arg...]

Malice Sophos AntiVirus Plugin

Version: v0.1.0, BuildTime: 20160920

Author:
  blacktop - <https://github.com/blacktop>

Options:
  --verbose, -V         verbose output
  --table, -t           output as Markdown table
  --post, -p            POST results to Malice webhook [$MALICE_ENDPOINT]
  --proxy, -x           proxy settings for Malice webhook endpoint [$MALICE_PROXY]
  --timeout value       malice plugin timeout (in seconds) (default: 10) [$MALICE_TIMEOUT]
  --elasitcsearch value elasitcsearch address for Malice to store results [$MALICE_ELASTICSEARCH]
  --help, -h            show help
  --version, -v         print the version

Commands:
  update        Update virus definitions
  help          Shows a list of commands or help for one command

Run 'sophos COMMAND --help' for more information on a command.
```

This will output to stdout and POST to malice results API webhook endpoint.

### Sample Output JSON:

```json
{
  "sophos": {
    "infected": true,
    "result": "EICAR-AV-Test",
    "engine": "5.27.0",
    "database": "5.31",
    "updated": "20160920"
  }
}
```

### Sample Output STDOUT (Markdown Table):

---

#### Sophos

| Infected | Result        | Engine | Updated  |
|----------|---------------|--------|----------|
| true     | EICAR-AV-Test | 5.27.0 | 20160920 |

---

Documentation
-------------

### To write results to [ElasticSearch](https://www.elastic.co/products/elasticsearch)

```bash
$ docker volume create --name malice
$ docker run -d --name elastic \
                -p 9200:9200 \
                -v malice:/usr/share/elasticsearch/data \
                 blacktop/elasticsearch
$ docker run --rm -v /path/to/malware:/malware:ro --link elastic malice/sophos -t FILE
```

### POST results to a webhook

```bash
$ docker run -v `pwd`:/malware:ro \
             -e MALICE_ENDPOINT="https://malice.io:31337/scan/file" \
             malice/sophos --post evil.malware
```

To update the AV run the following:

```bash
$ docker run --name=sophos malice/sophos update
```

Then to use the updated sophos container:

```bash
$ docker commit sophos malice/sophos:updated
$ docker rm sophos # clean up updated container
$ docker run --rm malice/sophos:updated EICAR
```

### Issues

Find a bug? Want more features? Find something missing in the documentation? Let me know! Please don't hesitate to [file an issue](https://github.com/maliceio/malice-av/issues/new).

### CHANGELOG

See [`CHANGELOG.md`](https://github.com/maliceio/malice-av/blob/master/sophos/CHANGELOG.md)

### Contributing

[See all contributors on GitHub](https://github.com/maliceio/malice-av/graphs/contributors).

Please update the [CHANGELOG.md](https://github.com/maliceio/malice-av/blob/master/sophos/CHANGELOG.md) and submit a [Pull Request on GitHub](https://help.github.com/articles/using-pull-requests/).

### License

MIT Copyright (c) 2016 **blacktop**
