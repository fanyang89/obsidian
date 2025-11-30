```
====== co 3, last_run_tid_: 93899, stack_: 0x55d893e49000 ""
out: 55d893e67730
zbs::meta::DrainManager::GenerateCmdsUnlocked(zbs::IdMap const&, std::vector<std::shared_ptr<zbs::meta::DrainExtentInfo>, std::allocator<std::shared_ptr<zbs::meta::DrainExtentInfo> > > const&, bool, std::vector<std::shared_ptr<zbs::meta::DrainExtentInfo>, std::allocator<std::shared_ptr<zbs::meta::DrainExtentInfo> > >*) + 177 in section .text of /usr/sbin/zbs-metad
zbs::meta::DrainManager::SinkVolume(unsigned int const*, unsigned int) + 1137 in section .text of /usr/sbin/zbs-metad
zbs::meta::MetaRpcServer::DoSinkVolume(zbs::meta::VolumePath const*) + 511 in section .text of /usr/sbin/zbs-metad
zbs::meta::MetaRpcServer::SinkVolume(google::protobuf::RpcController*, zbs::meta::VolumePath const*, zbs::Void*, google::protobuf::Closure*) + 2087 in section .text of /usr/sbin/zbs-metad
zbs::meta::MetaService::CallMethod(google::protobuf::MethodDescriptor const*, google::protobuf::RpcController*, google::protobuf::Message const*, google::protobuf::Message*, google::protobuf::Closure*) + 455 in section .text of /usr/sbin/zbs-metad
```
