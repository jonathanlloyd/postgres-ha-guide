# Setting up a HA Postgres cluster with Patroni and LXD

## Step 1 - Enable LXD DNS
LXD comes with a built-in DNS server that makes each container available under
it's own domain name in the format: `<CONTAINER_NAME>.lxd`. By default, the host
machine is not configured to resolve via this DNS server. To enable this run:

```bash
./scripts/enable-dns-on-host
```

| Note: This configuration will not be persisted when the host restarts

## Step 2 - Launch LXD containers
This setup will contain four nodes:
 - 1x HA proxy node - this will act as a loadbalancer across our Postgres
   instances
 - 3x data nodes - each of these nodes will contain a Postgres & HA proxy instance

To launch these nodes, run the following commands:
```bash
./scripts/launch pglb # Our loadbalancer
./scripts/launch data0 # 1st data node
./scripts/launch data1 # 2nd data node
./scripts/launch data2 # 3rd data node
```
