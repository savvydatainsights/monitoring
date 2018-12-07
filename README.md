# SDI Monitoring

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT) [![Build status](https://travis-ci.org/savvydatainsights/monitoring.svg?branch=master)](https://travis-ci.org/savvydatainsights/monitoring)

Project for developing the SDI monitoring solution, consisted basically in 3 components:

- [Prometheus](https://prometheus.io) - Monitoring system and time series database;
- [Grafana](https://grafana.com) - The open platform for analytics and monitoring;
- [cAvisor](https://github.com/google/cadvisor) - Analyzes resource usage and performance characteristics of running containers.

> The project also has some useful [Ansible playbooks](playbooks). Install [Ansible](https://www.ansible.com) and use them.

## Setup

In order to set the monitoring environment up, follow the steps below:

1. Create the file required for defining the basic authentication in the custom cAdvisor Docker image, by executing the command: `htpasswd -c cadvisor/auth.htpasswd prometheus`
2. Put in the file *prometheus/basic_auth_password* the same password used previously. Prometheus will use this file to set the Authorization header during requests to cAdvisor.
3. Finally, turn everything on through running: `docker-compose up -d`

Alternativelly to manually following the mentioned steps, you can just execute `ansible-playbook playbooks/setup.yml`. You will be prompted to type the password, and then all the steps will be performed automatically.

## Why a custom cAdvisor image?

The goal of building a custom cAdvisor image is bringing security to the data cAdvisor exposes. It's done by implementing **basic authentication** to the cAdvisor's Web application endpoints.

Unfortunatelly the */metrics* endpoint is not suitable for applying basic authentication yet. Regardless the use of basic authentication in Prometheus requests, this endpoint does not require authentication yet.

We have a *todo* task to contribute to the cAdvisor project with this feature. You can follow the issue [#1](https://github.com/savvydatainsights/monitoring/issues/1) to see what's going on :blush:

## Putting cAdvisor behind NGINX

An alternative of securing cAdvisor is using [NGINX](https://www.nginx.com) in front of it, as a [reverse proxy](https://en.wikipedia.org/wiki/Reverse_proxy) and requiring basic authentication. It's a good idea if you already have NGINX in your server, as a proxy server to other services. You restrict all the requests to a single port (80), avoiding cAdvisor from exposing its default port 8080.

The configuration below is an example of how you can configure NGINX. Use the same **auth.htpasswd** file generated during the setup process, described earlier.

```nginx
server {
    listen 80 default_server;

    location /metrics {
        auth_basic "Restricted";
        auth_basic_user_file /etc/nginx/cadvisor/auth.htpasswd;
        proxy_pass http://localhost:8080/metrics;
    }
}
```

## Adding hosts

By default, the localhost is automatically monitored. However, you can add other hosts where cAdvisor exposes containers' metrics, by adding more Prometheus targets. To add a host, execute:

`ansible-playbook playbooks/add-cadvisor.yml -e host=hostname -e target=ip:8080`

Replace *hostname* and *ip* with the appropriate values. If cAdvisor exposes the metrics through other port than 8080, change it too. Following the example, the metrics should be available by accessing http://ip:8080/metrics.

If your Prometheus host is a remote host, you must set the *prometheus_host* parameter, and a [inventory file](https://docs.ansible.com/ansible/latest/user_guide/intro_inventory.html) with the SSH credentials required for Ansible connection:

`ansible-playbook playbooks/add-cadvisor.yml -i inventory -e prometheus_host=remote -e host=hostname -e target=ip:8080`

![Monitoring diagram](https://dev.savvydatainsights.co.uk/nexus/repository/savvy/files/Monitoring.png)

The diagram above shows you can add as many hosts as you want, each host with an instance of cAdvisor from where Prometheus scrapes metrics. The monitored host can have several running containers or just cAdvisor.

## The dashboard

Grafana is available on port 3000. During its setup, the connection with Prometheus is made, and a default dashboard is provisioned.

The *Docker monitoring* dashboard is based on [this one](https://grafana.com/dashboards/193). Differently to the original, this dashboard data is filtered by host. By default, the localhost containers' metrics are shown, but you can switch to any other host you've added.

![Monitoring dashboard](https://dev.savvydatainsights.co.uk/nexus/repository/savvy/files/Dashboard.png)

## Deploying to Azure

With the Ansible playbook [azure.yml](azure.yml) is possible to deploy the monitoring solution to a VM in [Azure](https://azure.microsoft.com). The playbook creates all the required resources and then runs the services in the new remote VM, created from a [baked Ubuntu image](https://github.com/savvydatainsights/ubuntu). After the deployment, Grafana can be accessed through the port 3000.

`ansible-playbook azure.yml -i hosts`
