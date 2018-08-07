# SDI Monitoring

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT) [![Build status](https://travis-ci.org/savvydatainsights/monitoring.svg?branch=master)](https://travis-ci.org/savvydatainsights/monitoring)

Project for developing the SDI monitoring solution, consisted basically in 3 components:

- [Prometheus](https://prometheus.io) - Monitoring system and time series database;
- [Grafana](https://grafana.com) - The open platform for analytics and monitoring;
- [cAvisor](https://github.com/google/cadvisor) - Analyzes resource usage and performance characteristics of running containers.

## Setup

In order to set the monitoring environment up, follow the steps below:

1. Create the file required for defining the basic authentication in the custom cAdvisor Docker image, by executing the command: `htpasswd -c auth.htpasswd prometheus`
2. Put in the file *prometheus/basic_auth_password* the same password used previously. Prometheus will use this file later on to connect to cAdvisor.
3. Finally, turn everything on through running: `docker-compose up -d`

Alternativelly to manually following the mentioned steps, you can just execute `ansible-playbook playbooks/setup.yml`. You will be prompted to type the password, and then all the steps will be performed automatically.

## Adding hosts

By default, the localhost is automatically monitored. However, you can add other hosts where cAdvisor exposes containers' metrics, by adding more Prometheus targets. To add a host, execute:

`ansible-playbook playbooks/add-cadvisor.yml -e "host=hostname target=ip:8080"`

Replace *hostname* and *ip* with the appropriate values. If cAdvisor exposes the metrics through other port than 8080, change it too. Following the example, the metrics should be available by accessing http://ip:8080/metrics.
