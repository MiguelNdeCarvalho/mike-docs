# Speedtest Exporter
---

[![GitHub release (latest by date)](https://img.shields.io/github/v/release/miguelndecarvalho/speedtest-exporter?color=%23009485&label=Latest%20Release&logo=github&style=for-the-badge)][7]
[![GitHub Repo stars](https://img.shields.io/github/stars/miguelndecarvalho/speedtest-exporter?color=%23009485&label=repo%20stars&logo=github&style=for-the-badge)][11]
[![Docker Pulls](https://img.shields.io/docker/pulls/miguelndecarvalho/speedtest-exporter?color=%23009485&logo=docker&logoColor=%23ffffffff&style=for-the-badge)][9]
[![GitHub Workflow Status](https://img.shields.io/github/workflow/status/miguelndecarvalho/speedtest-exporter/Release%20Docker%20Image%20with%20new%20Tag?color=%23009485&logo=github&style=for-the-badge)][1]


[**Speedtest exporter**][1] is a [**Prometheus**][2] exporter written in **Python** using the official [Speedtest CLI][3] made by **Ookla**.

It will measure your:

- Download Speed
- Upload Speed
- Ping
- Jitter

---

## Setting up the Exporter

### Docker

Image location:

| Container Registry    | Image                                          |
| --------------------- | ---------------------------------------------- |
| [Github Container][8] | `ghcr.io/miguelndecarvalho/speedtest-exporter` |
| [Dockr Hub][9]        | `miguelndecarvalho/speedtest-exporter`         |

???+ warning
    I recommend you to use [Github Container][8] as you won't face any [pull limit][10].

Deploying:

=== "Docker CLI"

    ``` bash
    docker run -d \
      --name=speedtest-exporter \
      -p 9798:9798 \
      -e SPEEDTEST_PORT=<speedtest-port> #optional \
      -e SPEEDTEST_SERVER=<speedtest-serverid> #optional \
      --restart unless-stopped \
      miguelndecarvalho/speedtest-exporter
    ```

=== "Docker-Compose"

    ``` yaml
    version: "3.0"
    services:
      speedtest-exporter:
        image: miguelndecarvalho/speedtest-exporter
        container_name: speedtest-exporter
        environment:
          - SPEEDTEST_PORT=<speedtest-port> #optional
          - SPEEDTEST_SERVER=<server-id> #optional
        ports:
          - 9798:9798
        restart: unless-stopped
    ```

???+ tip
    You can pull a specific version of the exporter. For example: `miguelndecarvalho/speedtest-exporter:v3.1`
    Get the [changelog here][7]

#### Environments

| Env                | Optional         | Description                                          | Default     | Example          |
| ------------------ | ---------------- | ---------------------------------------------------- | ----------- | ---------------- |
| `SPEEDTEST_PORT`   | :material-check: | Sets the **Port** where exporter listens             | `9798`      | `9800`           |
| `SPEEDTEST_SERVER` | :material-check: | Set the **Server** from where the tests will be made | Best Server | `31309` - MEO PT |

???+ tip
    To get the **ServerID** to use in the **Env** `SPEEDTEST_SERVER` you can use this [Server list][4] to get the **IDs**.

???+ warning
    When you set the **Env** `SPEEDTEST_PORT`, don't forget to publish the right **port**.

    === "Docker CLI"
    ``` bash
    -p <SPEEDTEST_PORT>:<SPEEDTEST_PORT>
    ```

    === "Docker-Compose"
    ``` yaml
    ports:
      - <SPEEDTEST_PORT>:<SPEEDTEST_PORT>
    ```
???+ danger
    **We strongly discourage you from setting a specific server.**
    If the **server** that you are using to make the **tests** stops working, your exporter won't be able to do the tests and
    you won't get the **metrics**.


### Manual

#### Requirements

You will need to have:

- `git`,
- `python3`,
- `pip3`,
- [`speedtest`][3]

#### Steps to install

1. **Clone repo** - `git clone https://github.com/MiguelNdeCarvalho/speedtest-exporter.git`
2. **Enter the repo folder** - `cd speedtest-exporter`
3. **Install python modules** - `pip install -r src/requirements.txt`
4. **Execute the exporter** - `python src/exporter.py`

Then just access the page `http://localhost:9800/` and you will have the **exporter** page.

???+ tip
    If you wanna set the **Port** and the default **Server** just use [these](#environments) **Envs** to set it
    
    Example:
    ```bash
    export SPEEDTEST_PORT=9800; export SPEEDTEST_SERVER=1758; python src/exporter.py
    ```

## Add exporter to Prometheus

To add the Speedtest Exporter to your Prometheus just add this to your `prometheus.yml`:

``` yaml
- job_name: 'speedtest-exporter'
  scrape_interval: <time-between-tests>
  scrape_timeout: 1m
  static_configs:
    - targets: ['<ip-of-exporter-machine>:<port-where-exporter-is-listenning>']
``` 

Real example where the **tests** will be done **every hour**:

``` yaml
- job_name: 'speedtest-exporter'
  scrape_interval: 1h
  scrape_timeout: 1m
  static_configs:
    - targets: ['speedtest-exporter:9798']
```

## Grafana Dashboard

The Grafana Dashboard can be found [here][5] or you can get the file [here][6]

<figure>
  <img src="/assets/images/projects/speedtest-exporter/grafana.png"/>
  <figcaption>Fig 1. Grafana Dashboard</figcaption>
</figure>

## FAQ

???+ question
    **How can I set the time between the Scrapes?**

    That is configured on your `prometheus.yml`. In this [example](#add-exporter-to-prometheus) the test will
    be made every hour `scrape_interval: 1h`.

???+ question
    **Why isn't it working?**

    If you set the tests to be made **every hour** (`scrape_interval: 1h`), **Prometheus** will only do the first
    scrape after it is up for an hour.

???+ question
    **How can I test the exporter without Prometheus?**

    You can just acess access it with your browser `http://ip:9798/` and you should be prompted with a welcome screen.
    If you access `http://ip:9798/metrics` it should take around 30 seconds and then you will be prompted with all the
    metrics.

## Changelog

You can check the changelog [here][7]

[1]: https://github.com/MiguelNdeCarvalho/speedtest-exporter
[2]: https://prometheus.io/
[3]: https://www.speedtest.net/apps/cli
[4]: https://c.speedtest.net/speedtest-servers-static.php
[5]: https://grafana.com/grafana/dashboards/13665
[6]: https://github.com/MiguelNdeCarvalho/speedtest-exporter/blob/main/Dashboard/Speedtest-Exporter.json
[7]: https://github.com/MiguelNdeCarvalho/speedtest-exporter/releases
[8]: https://github.com/users/miguelndecarvalho/packages/container/package/speedtest-exporter
[9]: https://hub.docker.com/repository/docker/miguelndecarvalho/speedtest-exporter
[10]: https://docs.docker.com/docker-hub/download-rate-limit/
[11]: https://github.com/MiguelNdeCarvalho/speedtest-exporter/stargazers
