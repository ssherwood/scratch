

Pre-requisites
==============
- Install VirtualBox: https://www.virtualbox.org/wiki/Downloads
-- Follow instructions for Debian Xenial

- Install BOSH CLI: https://bosh.io/docs/cli-v2.html#install
- Install BOSH "create-env" dependencies (sudo apt-get ...)

Installing BOSH-lite
====================

* Clone bosh-deployment github repo

```
% git clone https://github.com/cloudfoundry/bosh-deployment
% cd bosh-deployment
```

```
% bosh create-env bosh.yml \
--state ./state.json \
-o virtualbox/cpi.yml \
-o virtualbox/outbound-network.yml \
-o bosh-lite.yml \
-o bosh-lite-runc.yml \
-o jumpbox-user.yml \
--vars-store ./creds.yml \
-v director_name="Bosh Lite Director" \
-v internal_ip=192.168.50.6 \
-v internal_gw=192.168.50.1 \
-v internal_cidr=192.168.50.0/24 \
-v outbound_network_name=NatNetwork
```

* Alias vbox

```
% bosh -e 192.168.50.6 --ca-cert <(bosh int ./creds.yml --path /director_ssl/ca) alias-env vbox  
```

* Log in to the BOSH Director

```
% export BOSH_CLIENT=admin
% export BOSH_CLIENT_SECRET=`bosh int ./creds.yml --path /admin_password`
% bosh -e vbox env
```

* Add route

```
% sudo route add -net 10.244.0.0/16 gw 192.168.50.6 # linux
```

* Log in via ssh

```
bosh int ./creds.yml --path /jumpbox_ssh/private_key > ~/.ssh/bosh-virtualbox.key  
chmod 600 ~/.ssh/bosh-virtualbox.key  
ssh -i ~/.ssh/bosh-virtualbox.key jumpbox@192.168.50.6  
```

Install Cloud Foundry
=====================

```
% git clone https://github.com/cloudfoundry/cf-deployment 
% cd cf-deployment
```

Upload the required stemcell

```
% bosh -e vbox upload-stemcell https://bosh.io/d/stemcells/bosh-warden-boshlite-ubuntu-trusty-go_agent
```

Update the cloud config

```
% bosh -e vbox update-cloud-config iaas-support/bosh-lite/cloud-config.yml  
```

Run the BOSH deployment

```
% bosh -e vbox -d cf deploy cf-deployment.yml \
-o operations/bosh-lite.yml \
--vars-store deployment-vars.yml \
-v system_domain=bosh-lite.com
```

>> This might take a while...

```
% sudo route add -net 10.244.0.0/16 gw 192.168.50.6 # Linux 
% export CF_ADMIN_PASSWORD=$(bosh int ./deployment-vars.yml --path /cf_admin_password)
% cf auth admin $CF_ADMIN_PASSWORD
```

Log in
------
```
% cf api https://api.bosh-lite.com --skip-ssl-validation
```

Setup Org and Space
-----------------------
```
% cf create-org cloudfoundry
% cf target -o cloudfoundry
% cf create-space development
% cf target -o cloudfoundry -s development
```

Push App
--------
```
% cf push hop-demo -p target/hop-demo-0.0.1-SNAPSHOT.jar -b java_buildpack -m 1G
```

Now visit the deployed apps health endpoint:  http://hop-demo.bosh-lite.com/health

Show Logs
---------
```
% cf logs hop-demo --recent
```

Or to just "tail" the logs

```
% cf logs hop-demo
```

Scale the App
-------------

```
cf scale hop-demo -i 4
```

Kill Instances
--------------

```
CF_TRACE=true cf apps hop-demo | grep \"url\"
```

Get the URL of the application

```
cf curl <URL>/instances/0 -X 'DELETE'
```

```
watch cf apps hop-demo
```


Final Clean-up
--------------

Stop the VM
```
vboxmanage controlvm $(bosh int state.json --path /current_vm_cid) savestate
```

Restart the VM
```
vboxmanage startvm $(bosh int state.json --path /current_vm_cid) --type headless
```

Remove it ALL
```
bosh delete-env bosh.yml \
--state state.json \
-o virtualbox/cpi.yml \
-o virtualbox/outbound-network.yml \
-o bosh-lite.yml \
-o bosh-lite-runc.yml \
-o jumpbox-user.yml \
--vars-store creds.yml \
-v director_name="Bosh Lite Director" \
-v internal_ip=192.168.50.6 \
-v internal_gw=192.168.50.1 \
-v internal_cidr=192.168.50.0/24 \
-v outbound_network_name=NatNetwork
```

Sources:

* https://bosh.io/docs/cli-v2.html#install
* http://www.starkandwayne.com/blog/bosh-lite-on-virtualbox-with-bosh2/
* http://www.starkandwayne.com/blog/running-cloud-foundry-locally-on-bosh-lite-with-bosh2/
* https://banck.net/2017/03/deploying-cloud-foundry-virtualbox-using-bosh-cli-v2/

* https://www.youtube.com/watch?v=xRkB9TcCFkU
