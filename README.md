An Ansible role to configure mirrors of package sources for CentOS 7.

### Overview

Currently following mirrors are support.

* [Base](http://mirror.centos.org/centos/) 
* [EPEL](http://download.fedoraproject.org/pub/epel)
* [PyPI](https://pypi.python.org/pypi)
* [InfluxData](https://repos.influxdata.com/)
* [Grafana](http://docs.grafana.org/installation/rpm/)
* [PostgreSQL](https://download.postgresql.org/pub/repos/yum/)
* [RabbitMQ](https://www.rabbitmq.com/install-rpm.html)
* [Grafana](http://docs.grafana.org/installation/rpm/)

They won't be setup unless explicitly enabled which would be cover in later sections.

#### Scenarios

As a engineer in China, the most painful experience with Ansible is package installation from Internet like yum, pip. The timeout would typically cause playbook failure and that is the main reason why this role is created.

Besides, most organizations would have their own sources mirrors so this role could be used too.

Finally, some softwares such as RabbitMQ, PostgreSQL have their own repos which could be configured by this role.

#### Security

There several ways to prevent malformed packages installation.

* By default the official repos will be used
* RPM supports [verification](https://blog.packagecloud.io/eng/2014/11/24/howto-gpg-sign-verify-rpm-packages-yum-repositories/) by GPG signatures and the GPG key will be import from the official path only.
* HTTPS is used by default and HTTP should be used only in LANs.
* PyPI doesn't support signature verification so make sure only trusted mirrors are used. HTTPs mirrors should be preferred.

### Dependencies
None

### Tested Environment
* Ansible 2.3
* CentOS 7

### Installation
```
ansible-galaxy install rhops.centos-mirrors
```

### Role Variables

All the mirrors will not get configured by default and following variables are used as switches.

* centos_epel_mirror_enabled
* centos_base_mirror_enabled
* pypi_mirror_enabled
* postgresql_mirror_enabled
* rabbitmq_mirror_enabled
* influxdata_mirror_enabled
* grafana_mirror_enabled

For example, in order to use grafana repo, `grafana_mirror_enabled: yes` has to be set. All  the  other options could be found from [default variables list](defaults/main.yml) which I believe are quite self-explanatory.

#### System Repos

| Name                       | Default Value                         | Description                                                                                                                                                                            |
|----------------------------|---------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| centos_base_mirror_enabled | no                                    | Whether to configure CentOS yum base repo or not.                                                                                                                                      |
| centos_epel_mirror_enabled | no                                    | Whether to configure CentOS yum epel repo or not.                                                                                                                                      |
| centos_base_host           | http://mirror.centos.org/centos       | The common part of the base urls. Base repo actually contains serveral sub repos such as base, extras and updates. There is no fine-grained configuration for sub repos at the moment. |
| centos_epel_host           | http://download.fedoraproject.org/pub | The common part of the epel repo urls.                                                                                                                                                 |

**N.B.**  The values of `centos_base_host` and `centos_epel_host` are subject to the directory structure.  For example, the whole url of base repo would be exploded as `baseurl={{ centos_base_host }}/$releasever/os/$basearch/` in the jinja2 template file. Most mirrors follow this structure. Keep that in mine in case there is exception.

#### PyPI Variables

| Name                | Default Value                  | Description                                                                                                                                                                                                                                                                                        |
|---------------------|--------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| pypi_mirror_enabled | no                             | Whether to configure PyPI mirror or not                                                                                                                                                                                                                                                               |
| pypi_index_url      | https://pypi.python.org/simple | The index url of pip                                                                                                                                                                                                                                                                               |
| pypi_search_index   | https://pypi.python.org/pypi   | The search index of pip                                                                                                                                                                                                                                                                            |
| pypi_timeout        | 6000                           | PyPI default timeout                                                                                                                                                                                                                                                                               |
| pypi_trusted_host   | ""                             | If the mirror server does not support https, the host must be marked as trusted explicitly.  Basically,  mirror with https should be preferred.                                                                                                                                                    |
| pypi_users          | [ {{ ansible_ssh_user }} ]     | Just like `pip`, `easy_install` also leverages PyPI server. But its configuration file could be placed only in the home directory which means different users requires a dedicated copy. This variable is a list which contains the users who intends to use  `eays_install` with the PyPI mirror. |

#### PostgreSQL Variables

| Name                      | Default Value                                                          | Description                                                                                                                                                                                                                     |
|---------------------------|------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| postgresql_mirror_enabled | no                                                                     | Whether to configure postgresql yum repo or not                                                                                                                                                                                    |
| postgresql_short_version  | 96                                                                     | The short version of the PostgreSQL. We intend to support different versions of PostgreSQL which are signed with different keys.  Check [official repo](https://download.postgresql.org/pub/repos/yum/) for supported versions. |
| postgresql_repo_url       | https://download.postgresql.org/pub/repos/yum/9.6/redhat/rhel-7-x86_64 | The *whole* url of the repo.                                                                                                                                                                                                    |
| postgresql_repo_gpgcheck  | yes                                                                    | Apply gpg check during installation                                                                                                                                                                                             |
| postgresql_repo_enabled   | yes                                                                    | Enable repo or not                                                                                                                                                                           |

#### RabbitMQ variables

| Name                    | Default Value                                       | Description                                |
|-------------------------|-----------------------------------------------------|--------------------------------------------|
| rabbitmq_mirror_enabled | no                                                  | Whether to configure rabbitmq yum repo or not |
| rabbitmq_repo_url       | https://dl.bintray.com/rabbitmq/rabbitmq-server-rpm | The *whole* url of the repo                |
| rabbitmq_repo_enabled   | yes                                                 | Enable rabbitmq repo or not                |
| rabbitmq_repo_gpgcheck  | yes                                                 | Apply gpg check during installation        |

#### InfluxData Variables

| Name                      | Default Value                                        | Description                                  |
|---------------------------|------------------------------------------------------|----------------------------------------------|
| influxdata_mirror_enabled | no                                                   | Whether to configure influxdata yum repo or not |
| influxdata_repo_url       | https://repos.influxdata.com/centos/7/x86_64/stable/ | The *whole* url of the repo                  |
| influxdata_repo_enabled   | yes                                                  | Enable influxdata repo or not                |
| influxdata_repo_gpgcheck  | yes                                                  | Apply gpg check during installation          |

#### Grafana Variables

| Name                   | Default Value                                                   | Description                                  |
|------------------------|-----------------------------------------------------------------|----------------------------------------------|
| grafana_mirror_enabled | no                                                              | Whether to configure grafana yum repo or not |
| grafana_repo_url       | https://packagecloud.io/grafana/stable/el/$releasever/$basearch | The *whole* url of the repo                  |
| grafana_repo_enabled   | yes                                                             | Enable grafana repo or not                   |
| grafana_repo_gpgcheck  | yes                                                             | Apply gpg check during installation          |
                                                                                                                                              
### License
Apache 2.0

### Author Information

This role was created in 2017 by [Jeff Li](https://blog.jeffli.me)


