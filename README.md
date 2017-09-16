# grok_exporter

Export [Prometheus] metrics from arbitrary unstructured log data.

## About Grok

[Grok] is a tool to parse crappy unstructured log data into something structured and queryable. 

The [`grok_exporter`] aims at porting Grok from the ELK stack to [Prometheus] monitoring. The goal is to use Grok patterns for extracting Prometheus metrics from arbitrary log files. 

## How to use this image

You need to provide [`grok_exporter`] with a config file in order to function properly. For more information on proper configuration of `grok_exporter` please refer to its [documentation].

There are various ways in which you can utilize this image, each however assumes that the log file you're trying to parse is reacheable by the grok_exporter container.


### Running Solo 

This assumes Prometheus is installed locally (or on another server) and therefore, the metrics endpoint that `grok_exporter` exposes must be reachable:

```sh
docker container run --name grok -d \
                     -p PORT:PORT \
                     -v $(pwd)/config.yml:/grok/config.yml \
                     palobo/grok_exporter
```

- `PORT` is the port defined in config file (default is 9144


### Docker Compose Setup

Assuming you're using this image alonside other images (Prometheus and Grafana running in containers too), then the is slightly different since there is no need to expose the ports externally.

```sh
docker container run --name grok -d \
                     --net PROM-NETWORK \
                     -v $(pwd)/config.yml:/grok/config.yml \
                     palobo/grok_exporter
```

### Example Config

Refer to the `grok_exporter` [documentation] for all the details on how to properly configure it.

```yml
global:
    config_version: 2
input:
    type: file
    path: ./example/example.log
    readall: true
grok:
    patterns_dir: ./logstash-patterns-core/patterns
metrics:
    - type: counter
      name: grok_example_lines_total
      help: Counter metric example with labels.
      match: '%{DATE} %{TIME} %{USER:user} %{NUMBER}'
      labels:
          user: '{{.user}}'
server:
    port: 9144
```

## Donations
If you'd like to thank my work in dockerizing this, donations are accepted in a number of crypto currencies:

- BTC: `17D3VRudF4CPZTAfCqf3XoVMKHiqq36HQF`
- ETH: `0xA86Efc80Ae2C471Fa0bF3956E85d6748552D015E`
- LTC: `LbUmXfLGEZZVwS3ixLeo2EoyK26BrxT7Zs`



[Prometheus]: https://prometheus.io/
[Grok]: https://www.elastic.co/guide/en/logstash/current/plugins-filters-grok.html
[grok_exporter]: https://github.com/fstab/grok_exporter
[documentation]: https://github.com/fstab/grok_exporter/blob/master/CONFIG.md