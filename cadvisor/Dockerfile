FROM google/cadvisor:v0.27.4
LABEL maintainer "Gustavo Muniz do Carmo <gmunizcarmo@aubay.com>"

ADD auth.htpasswd /

EXPOSE 8080
ENTRYPOINT ["/usr/bin/cadvisor", "--http_auth_file", "auth.htpasswd", "--http_auth_realm", "localhost"]
