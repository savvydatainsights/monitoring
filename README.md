# SDI Monitoring

Project for developing the SDI monitoring solution, consisted basically in 3 components:

- [Prometheus](https://prometheus.io) - Monitoring system and time series database;
- [Grafana](https://grafana.com) - The open platform for analytics and monitoring;
- [cAvisor](https://github.com/google/cadvisor) - Analyzes resource usage and performance characteristics of running containers.

In order to set the monitoring environment up, follow the steps below:

1. Create the file required for defining the basic authentication in the custom cAdvisor Docker image, by executing the command: `htpasswd -c auth.htpasswd prometheus`
2. Put in a file called *basic_auth_password* the same password used previously. Prometheus will use this file later on to connect to cAdvisor.
3. Finally, turn everything on through running: `docker-compose up -d`

Alternativelly to manually following the mentioned steps, you can just execute `ansible setup.yml`. You will be prompted to type the password, and then all the steps will be performed automatically.
