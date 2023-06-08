# CloudResume
Summary Cloud Computing Openstack
## Cloud Resume

# Systemes d'exploitation supportes par l'openstack:
- Red Hat Entreprise Linux
- openSUSE
- Ubuntu

Compute Nova: Compute Service
Compute Zun: Containers Service

Hardware Lifecycle :
- Ironic: Bare Metal Provisioning Service
- Cyborg: Lifecycle management of accelerators

Storage : 
- Swift: Object store
- Cinder: -Block Storage
- Manila: Shared filesystems

Networking:
- Neutron: Networking
- Octavia: Load balancer
- Designate: DNS service

Shared Services:
- Keystone: Identity service
- Placement: Placement service
- Glance :Image service
- Barbican: Key management

Orchestration:
- Heat: Orchestration
- Senlin: Clustering service
- Mistral: Workflow service
- Zaqar: Messaging Service
- Blazar: Resource reservation service
- Aodh: Alarming Service

Workload Provisioning:
- Magnum: Container Orchestration Engine Provisioning
- Sahara: Big Data Processing Framework Provisioning
- Trove: Database as a Service

Application Lifecycle
- Masakari: Instances High Availability Service
- Murano: Application Catalog
- Solum Software Development Lifecycle Automation
- Freezer: Backup, Restore, and Disaster Recovery

API Proxies:
- EC2API: -EC2 API proxy

Web frontends:
- Horizon: Dashboard
- Skyline: Next generation dashboard (emerging technology)

#Installation
Ubuntu:
add-apt-repository cloud-archive:antelope
apt install nova-compute
apt install mariadb-server python3-pymysql
apt install memcached python-memcache
apt install etcd
systemctl enable etcd
systemctl restart etcd
#Installation Keystone:
apt install keystone
openstack domain create --description "An Example Domain" example
openstack project create --domain default --description "Service Project" service
openstack user create --domain default --password-prompt myuser
openstack role create myrole
openstack role add --project myproject --user myuser myrole
#Installation Glance:
```
apt install glance # Edit the /etc/glance/glance-api.conf file
openstack user create --domain default --password-prompt glance
openstack role add --project service --user glance admin
openstack service create --name glance --description "OpenStack Image" image
service glance-api restart
glance image-create --name "cirros" --file cirros-0.4.0-x86_64-disk.img --disk-format qcow2 --container-format bare --visibility=public
glance image-list
```

#Installation Placement:
```
openstack service create --name placement --description "Placement API" placement
openstack endpoint create --region RegionOne placement public http://controller:8778
apt install placement-api
placement-status upgrade check
```

#Installation du service compute NOVA:
```
apt install nova-api nova-conductor nova-novncproxy nova-scheduler
MariaDB [(none)]> CREATE DATABASE nova_api;
MariaDB [(none)]> CREATE DATABASE nova;
MariaDB [(none)]> CREATE DATABASE nova_cell0;
MariaDB [(none)]> GRANT ALL PRIVILEGES ON nova_api.* TO 'nova'@'localhost' IDENTIFIED BY 'NOVA_DBPASS';
MariaDB [(none)]> GRANT ALL PRIVILEGES ON nova_api.* TO 'nova'@'%' IDENTIFIED BY 'NOVA_DBPASS';
MariaDB [(none)]> GRANT ALL PRIVILEGES ON nova.* TO 'nova'@'localhost' IDENTIFIED BY 'NOVA_DBPASS';
MariaDB [(none)]> GRANT ALL PRIVILEGES ON nova.* TO 'nova'@'%' IDENTIFIED BY 'NOVA_DBPASS';
MariaDB [(none)]> GRANT ALL PRIVILEGES ON nova_cell0.* TO 'nova'@'localhost' IDENTIFIED BY 'NOVA_DBPASS';
MariaDB [(none)]> GRANT ALL PRIVILEGES ON nova_cell0.* TO 'nova'@'%' IDENTIFIED BY 'NOVA_DBPASS';
openstack user create --domain default --password-prompt nova
openstack role add --project service --user nova admin
openstack service create --name nova --description "OpenStack Compute" compute
openstack endpoint create --region RegionOne compute public http://controller:8774/v2.1
openstack endpoint create --region RegionOne compute internal http://controller:8774/v2.1
openstack endpoint create --region RegionOne compute admin http://controller:8774/v2.1
su -s /bin/sh -c "nova-manage api_db sync" nova
service nova-api restart
service nova-scheduler restart
service nova-conductor restart
service nova-novncproxy restart
```

#Installation Neutron(Service Networking):
```
mysql -u root -p
MariaDB [(none)] CREATE DATABASE neutron;
MariaDB [(none)]> GRANT ALL PRIVILEGES ON neutron.* TO 'neutron'@'localhost' IDENTIFIED BY 'NEUTRON_DBPASS';
MariaDB [(none)]> GRANT ALL PRIVILEGES ON neutron.* TO 'neutron'@'%' IDENTIFIED BY 'NEUTRON_DBPASS';
. admin-openrc
openstack user create --domain default --password-prompt neutron
openstack role add --project service --user neutron admin
openstack service create --name neutron --description "OpenStack Networking" network
openstack endpoint create --region RegionOne network public http://controller:9696
openstack endpoint create --region RegionOne network internal http://controller:9696
openstack endpoint create --region RegionOne network admin http://controller:9696
su -s /bin/sh -c "neutron-db-manage --config-file /etc/neutron/neutron.conf --config-file /etc/neutron/plugins/ml2/ml2_conf.ini upgrade head" neutron
service nova-api restart
service neutron-server restart
service neutron-linuxbridge-agent restart
service neutron-dhcp-agent restart
service neutron-metadata-agent restart
service neutron-l3-agent restart
```
#Installation horizon (Dashboard):

```
apt install openstack-dashboard
Fichier de configuration: /etc/openstack-dashboard/local_settings.py
Access the dashboard using a web browser at http://controller/horizon/.
```
#Installation Cinder (Service de ctackage de block):
```
apt install cinder-api cinder-scheduler
su -s /bin/sh -c "cinder-manage db sync" cinder
apt install lvm2 thin-provisioning-tools
pvcreate /dev/sdb
vgcreate cinder-volumes /dev/sdb
apt install cinder-volume tgt
service tgt restart
service cinder-volume restart
apt install cinder-backup
openstack catalog show object-store
service cinder-backup restart
```
