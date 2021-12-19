# Setting up a HA Postgres cluster with Patroni and LXD

## Step 1 - Enable LXD DNS
LXD comes with a built-in DNS server that makes each container available under
it's own domain name in the format: `<CONTAINER_NAME>.lxd`. By default, the host
machine is not configured to resolve via this DNS server. To enable this run:

```bash
./scripts/enable-dns-on-host
```

| Note: This configuration will not be persisted when the host restarts

## Step 2 - Create base image
Next, we will create a base lxc image with openssh installed and the ssh key
from your machine added to the authorized key set:

```bash
./scripts/create-base-image
```

## Step 3 - Launch LXD containers
This setup will contain four nodes:
 - 1x HA proxy node - this will act as a loadbalancer across our Postgres
   instances
 - 3x data nodes - each of these nodes will contain a Postgres & HA proxy instance

To launch these nodes, run the following commands:
```bash
./scripts/launch pglb # Our loadbalancer
```

Test that you have ssh access to the nodes:
```bash
ssh pglb.lxc
```

## Step 4 - Provision etcd cluster
Run the following script against each data node -
this will download & run etcd in a 3 node cluster:

```bash
./scripts/deploy-etcd data0.lxd
./scripts/deploy-etcd data1.lxd
./scripts/deploy-etcd data2.lxd
```

Verify the cluster by ssh'ing into data0:

```bash
ssh data0.lxd
etcdctl endpoint status --cluster -w table
```

