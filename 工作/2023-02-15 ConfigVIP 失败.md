# 日志

```
I0214 18:55:59.667798 160577 task_server.cc:118] Successfully served as leader, restore priority
I0214 18:55:59.668694 160570 task_client.cc:52] Connecting to remote: 0.0.0.0:0-->127.0.0.1:10600
I0214 18:55:59.668740 160570 task_client.cc:55] Connected to remote: 0.0.0.0:0-->127.0.0.1:10600
I0214 18:55:59.668817 160570 proto_async_client.h:180] Connect to 0.0.0.0:0-->127.0.0.1:10600 with timeout_ms 200
I0214 18:55:59.668848 160577 task_server.cc:133] Done starting the taskd service
I0214 18:55:59.668886 160577 quorum_server.cc:74] Get quorum cluster event: Members_Changed
I0214 18:55:59.668906 160577 quorum_server.cc:93] Members changed.
I0214 18:55:59.669021 160570 proto_async_client.h:366] Connected to 127.0.0.1:51488-->127.0.0.1:10600
E0214 18:55:59.669157 160580 ippattern.h:53] unable to parse ip address: 123
I0214 18:55:59.672665 160577 task_vip_service.cc:388] Get Zoo event: 4 for service: vip
I0214 18:55:59.682847 160577 task_vip_service.cc:278] set vip for  iscsi ip : 10.0.0.251 on if :ens33
I0214 18:55:59.683336 160577 task_vip_service.cc:418] Add VIP success, service: iscsi

I0214 18:56:00.678601 160577 task_vip_service.cc:388] Get Zoo event: 3 for service: iscsi
I0214 18:56:00.680603 160577 task_vip_service.cc:339] Clean VIP for service: iscsi vip: 10.0.0.251
I0214 18:56:00.691463 160577 task_vip_service.cc:278] set vip for  iscsi ip : 10.0.0.252 on if :ens33
I0214 18:56:00.691951 160577 task_vip_service.cc:498] Reset vip success, service: iscsi

I0214 18:56:01.687734 160577 task_vip_service.cc:388] Get Zoo event: 4 for service: vip
I0214 18:56:01.697924 160577 task_vip_service.cc:278] set vip for  nfs ip : 10.0.0.253 on if :ens33
I0214 18:56:01.698473 160577 task_vip_service.cc:418] Add VIP success, service: nfs

E0214 18:56:02.692250 160577 task_vip_service.cc:538] ConfigVIP failed!
E0214 18:56:02.692291 160577 task_server.cc:340] error encountered, restart:
Traceback:
[EZKNoNode]:  [GET] path: /zbs_test/taskd/vip/iscsi
I0214 18:56:02.692314 160577 quorum_server.cc:51] Quorum server begins stopping, this: 0x56465c036000
```
