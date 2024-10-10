# Docker Kafka Kraft

![Build and Push Status](https://github.com/moeenz/docker-kafka-kraft/actions/workflows/ci.yml/badge.svg?branch=master)

Apache Kafka Docker image using using Kafka Raft metadata mode (KRaft). In KRaft the Kafka metadata information will be stored as a partition within Kafka itself. There will be a KRaft Quorum of controller nodes which will be used to store the metadata. The metadata will be stored in an internal Kafka topic `@metadata`.

**IMPORTANT: THIS IMAGE IS ONLY VALID FOR TEST PURPOSES AND NOT PRODUCTION USE.**

## Getting Started

```bash
$ docker pull moeenz/docker-kafka-kraft
$ docker run -e KRAFT_CONTAINER_HOST_NAME=kafka -e KRAFT_CREATE_TOPICS=topic-a,topic-b,topic-c -e KRAFT_PARTITIONS_PER_TOPIC=3 -e KRAFT_AUTO_CREATE_TOPICS=true moeenz/docker-kafka-kraft
```

- Now you can reach the container at `localhost:9093` on your host machine or inside Docker network with hostname `kafka`.
- Comma seperated values received by `KRAFT_CREATE_TOPICS` env will be used to create topics at startup time.
- The `KRAFT_AUTO_CREATE_TOPICS` env variable lets you turn off auto topic creation (`auto.create.topics.enable=false`). Default Kafka behavior is `true`. 

### Access Across The Network

You can expose this Kafka broker to the network, in case any non-container applications need to access it or you want to access it from other machines.
This can also be useful if you run Linux in a VM (or via WSL) and want to allow your host machine to access Kafka.

Remember that this image **does not use Kafka authentication** so you should only do this on networks you trust.

```bash
$ hostname
my-linux-box
$ docker run -p 9093:9093 -e KRAFT_ADVERTISE_HOST_NAME=my-linux-box -e KRAFT_CONTAINER_HOST_NAME=kafka -e KRAFT_PARTITIONS_PER_TOPIC=3 -e KRAFT_AUTO_CREATE_TOPICS=true moeenz/docker-kafka-kraft
```

This allows other machines on the network to connect to Kafka at `my-linux-box:9093`.
`KRAFT_ADVERTISE_HOST_NAME` can also be an IP address if your network is not setup for hostnames:

```bash
$ ip addr show eth0
...
    inet 192.168.0.2
$ docker run -p 9093:9093 -e KRAFT_ADVERTISE_HOST_NAME=192.168.0.2 -e KRAFT_CONTAINER_HOST_NAME=kafka -e KRAFT_PARTITIONS_PER_TOPIC=3 -e KRAFT_AUTO_CREATE_TOPICS=true moeenz/docker-kafka-kraft
```

### Compose Example

```yaml
services:
  kafka:
    image: moeenz/docker-kafka-kraft:latest
    restart: always
    ports:
      - "9093:9093"
    environment:
      - KRAFT_CONTAINER_HOST_NAME=kafka
      - KRAFT_CREATE_TOPICS=topic-a,topic-b,topic-c
      - KRAFT_PARTITIONS_PER_TOPIC=3
      - KRAFT_AUTO_CREATE_TOPICS=true
```

## Environment Variables

| Name                       | Type     | Description                                                                       | Example                 |
| -------------------------- | -------- | --------------------------------------------------------------------------------- | ----------------------- |
| KRAFT_CONTAINER_HOST_NAME  | string   | Hostname for the running container as the Kafka listener                          | kafka                   |
| KRAFT_ADVERTISE_HOST_NAME  | string   | Hostname or IP address that Kafka advertises externally. Defaults to `localhost`. | my-hostname             |
| KRAFT_CREATE_TOPICS        | []string | Comma separated list of topics to be created post server setup                    | topic-a,topic-b,topic-c |
| KRAFT_PARTITIONS_PER_TOPIC | int      | Number of partitions per topic                                                    | 3                       |
| KRAFT_AUTO_CREATE_TOPICS   | string   | `true` or `false`, Kafka default behavior is `true`.                              | false                   |

## Resources

- [https://adityasridhar.com/posts/how-to-easily-install-kafka-without-zookeeper](https://adityasridhar.com/posts/how-to-easily-install-kafka-without-zookeeper)

## License

MIT License

Copyright (c) 2024 docker-kafka-kraft

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
