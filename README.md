# vagrant-elasticstack

 (c) Andre Lohmann (and others) 2021

## Maintainer Contact
 * Andre Lohmann
   <lohmann.andre (at) gmail (dot) com>

<!---

There is some stuff waiting to be uncommented, when Ansible 2.10 is released

--->

## content

Vagrant template, to deploy an elasticstack (elasticsearch + kibana + beats) test stack.

## Usage

### Configuration

Normally the System should run out of the box, but it sometimes is necessary, to customize some of the domains or IP addresses e.g.

In this case, please edit the files config.yml, ansible_vagrant/custom_vars.yml and ansible_vagrant/*-playbook.yml.

### Run

```
vagrant up
```

The Vagrantfile installs two machines. One (server) is equipped with elasticsearch and kibana, while the other (client) is equipped with beats, shipping metrics to the server.

```
vagrant ssh server
```

```
vagrant ssh client
```

#### Elasticstack

Elasticsearch will be listening on the server public ip on port 9200.

```
curl -i http://elastic.lokal:9200
```

Kibana will be listening on the server public ip on port 5601.

```
http://elastic.lokal:5601
```

#### Prometheus

Prometheus will be listening on the server public ip on port 9090.

```
http://elastic.lokal:9090
```

#### Grafana

Grafana will be listening on the server public ip on port 3000.

```
http://grafana.elastic.lokal:3000
```

The initial username and password are admin:admin.

#### Mailhog

A Mailhog instance is listening on the server as a mail catch all server.

```
http://mail.elastic.lokal
```

#### Icinga2

The Icinga2 monitoring system is listening on the server under port 80.

```
http://icinga.elastic.lokal/icingaweb2
```

#### Graphite

The graphite frontend is listening on the server under port 8888. You can login by using user "root" with password "root".

```
http://icinga.elastic.lokal:8888
```
