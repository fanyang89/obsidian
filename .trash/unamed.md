```
====== coroutine 3 ====== YIELD
zbs::consensus::DBClusterClient::GetAllFromSnapshot(std::string const&, std::vector<std::pair<std::string, std::string>, std::allocator<std::pair<std::string, std::string> > >*, long*, zbs::Pagination const*) + 455 in section .text of /usr/sbin/zbs-metad
zbs::meta::MetaRpcServer::DumpDb(google::protobuf::RpcController*, zbs::meta::DumpDbRequest const*, zbs::meta::DumpDbResponse*, google::protobuf::Closure*) + 142 in section .text of /usr/sbin/zbs-metad
zbs::meta::MetaService::CallMethod(google::protobuf::MethodDescriptor const*, google::protobuf::RpcController*, google::protobuf::Message const*, google::protobuf::Message*, google::protobuf::Closure*) + 1503 in section .text of /usr/sbin/zbs-metad
zbs::RpcServer::OnRead(std::shared_ptr<zbs::Socket> const&) + 1601 in section .text of /usr/sbin/zbs-metad
zbs::AsyncServer::ReadCallback(unsigned int, std::shared_ptr<zbs::Socket>) + 280 in section .text of /usr/sbin/zbs-metad

```

```
$cat /etc/zbs/zbs.conf 
[network]
data_ip=10.233.193.84
heartbeat_ip=10.233.193.84
vm_ip=172.20.178.60
web_ip=172.20.178.60

[cluster]
role=master
members=10.233.193.82,10.233.193.83,10.233.193.84
zookeeper=10.233.193.82:2181,10.233.193.83:2181,10.233.193.84:2181
mongo=10.233.193.82:27017,10.233.193.83:27017,10.233.193.84:27017
cluster_mgt_ips=172.20.178.56,172.20.178.58,172.20.178.60
cluster_storage_ips=10.233.193.82,10.233.193.83,10.233.193.84
```