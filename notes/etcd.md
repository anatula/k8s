[Run etcd clusters inside containers](https://etcd.io/docs/v3.5/op-guide/container/)

Configure a Docker volume to store etcd data:

```
export NODE1=192.168.1.87
export DATA_DIR="etcd-data"
REGISTRY=quay.io/coreos/etcd
docker volume create --name etcd-data

docker run \
  -p 2379:2379 \
  -p 2380:2380 \
  --volume=${DATA_DIR}:/etcd-data \
  --name etcd ${REGISTRY}:v3.4.35 \
  /usr/local/bin/etcd \
  --data-dir=/etcd-data --name node1 \
  --initial-advertise-peer-urls http://${NODE1}:2380 --listen-peer-urls http://0.0.0.0:2380 \
  --advertise-client-urls http://${NODE1}:2379 --listen-client-urls http://0.0.0.0:2379 \
  --initial-cluster node1=http://${NODE1}:2380
```

Using homebrew on macOS

`brew install etcd`

Start etcd server
`etcd`

Use etcd client to set a key-value pair:
`etcdctl put name Ana`
Retrieve the key value pair:
`etcdctl get name`
