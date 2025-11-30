| branch            | commit                                   |
| ----------------- | ---------------------------------------- |
| master            | 255bf00dd165b5dc8475c9919ea134b9b8b9dab4 |
| SMTX OS `v4.0.10` | fd2b87ba0a5dd21b220b45af5a98f67a72322a0f |
| SMTX OS `v5.0.6`  | 70063ceee224a0a703e1433e9b83030af9126526 |

SMTX OS 5.0.6 ` pyzbs-5.0.8-rc26.0. git. g14e7087f3. el7. centos`
SMTX OS 4.0.10 `4.0.10-rc37.0.git.g17bf5d15c.el7.centos`
heads/v4.0.10-rc37

# branch commit list

```
# heads/v5.0.8-rc26
['ZBS-24968', 'ZBS-24956', 'ZBS-25311', 'ZBS-19472', 'PYZBS-3130', 'ZBS-21367', 'ZBS-21404', 'PYZBS-3124', 'ZBS-21027', 'ZBS-23048', 'ZBS-20288', 'ZBS-13105', 'TUNA-2346', 'ZBS-14048', 'ZBS-14047', 'ZBS-11206', 'ZBS-11192', 'ZBS-10566', 'TUNA-152', 'ZBS-9030', 'TUNA-57']
```

```
# heads/v4.0.10-rc37
['ZBS-11206', 'ZBS-11192', 'ZBS-10566', 'TUNA-152', 'ZBS-9030', 'TUNA-57']

fd2b87ba0	Luo Jiebin	2020-06-11	Fixes ZBS-11206 tuna: write ip to temporary file first and copy back
10aec829f	yutian	2019-02-25	Fixes ZBS-11192 grep failed when ip address look similar
cf3dffd80	yutian	2018-08-14	Fixes ZBS-10566 reroute failed after link aggregation to one scvm
a5dd26a86	yiran	2018-03-15	Fixes TUNA-152 change esxi get route cli
03a0c8e8a	yutian	2018-01-24	ZBS-9030 reroute support stretched cluster
e011e8baf	Qiuping Wang	2017-12-27	Fixes TUNA-57 migrate deployment code to tuna
```

```
# v4.0.x
['ZBS-25311', 'ZBS-24968', 'ZBS-24956', 'ZBS-19472', 'PYZBS-3130', 'PYZBS-3124', 'ZBS-21367', 'ZBS-21027', 'ZBS-20415', 'ZBS-20288', 'ZBS-23048', 'ZBS-13105', 'ZBS-11206', 'ZBS-11192', 'ZBS-10566', 'TUNA-152', 'ZBS-9030', 'TUNA-57']

ecfd7e366	chenqi	2023-05-15	ZBS-25311: set ICMP default payload size to 1400
fff7b9428	chenqi	2023-02-07	Fixes ZBS-24968 ioreroute: change session alive limit from 180 to 10
c7a2bd7f4	chenqi	2023-02-03	Fixes ZBS-24956: ioreroute check the session alive time of nfs target
ce6913168	chenqi	2023-02-02	Fixes ZBS-19472 ioreroute: add session alive check when local scvm back to normal
cebd113e6	Li Feng	2023-01-30	Fixes PYZBS-3130 ioreroute: fix the timeout cmd
40a4d0b21	Sai Wang	2022-08-22	PYZBS-3124 io_reroute: fix active ip array may lost item
7c94886d8	chenqi	2022-09-01	ZBS-21367 Too many session update commands running while upgrading
87d78c6de	yutian	2022-07-09	ZBS-21027 reroute faile in ESXi 6.0
747cb9494	Jiewei Ke	2021-12-15	ZBS-20415 add route to the correct data ip when local scvm is back
3cd5dc6a3	yiwu.cai	2021-10-27	ZBS-20288 refine reroute to local scvm data ip scene
3541fb7d2	yutian	2021-11-19	ZBS-23048 change_route failed
ccaa501d8	yiwu.cai	2021-09-24	ZBS-13105 refine io-reroute about vmware deployment process
fd2b87ba0	Luo Jiebin	2020-06-11	Fixes ZBS-11206 tuna: write ip to temporary file first and copy back
10aec829f	yutian	2019-02-25	Fixes ZBS-11192 grep failed when ip address look similar
cf3dffd80	yutian	2018-08-14	Fixes ZBS-10566 reroute failed after link aggregation to one scvm
a5dd26a86	yiran	2018-03-15	Fixes TUNA-152 change esxi get route cli
03a0c8e8a	yutian	2018-01-24	ZBS-9030 reroute support stretched cluster
e011e8baf	Qiuping Wang	2017-12-27	Fixes TUNA-57 migrate deployment code to tuna
```

```
# v5.0.8
['ZBS-24968', 'ZBS-24956', 'ZBS-25311', 'ZBS-19472', 'PYZBS-3130', 'ZBS-21367', 'ZBS-21404', 'PYZBS-3124', 'ZBS-21027', 'ZBS-23048', 'ZBS-20288', 'ZBS-13105', 'TUNA-2346', 'ZBS-14048', 'ZBS-14047', 'ZBS-11206', 'ZBS-11192', 'ZBS-10566', 'TUNA-152', 'ZBS-9030', 'TUNA-57']

70063ceee	chenqi	2023-02-07	Fixes ZBS-24968 ioreroute: change session alive limit from 180 to 10
70a5d2b4e	chenqi	2023-02-03	Fixes ZBS-24956: ioreroute check the session alive time of nfs target
2eada2acb	chenqi	2023-05-15	ZBS-25311: set ICMP default payload size to 1400
a7dff76cd	chenqi	2023-02-02	Fixes ZBS-19472 ioreroute: add session alive check when local scvm back to normal
754f2f47d	Li Feng	2023-01-30	Fixes PYZBS-3130 ioreroute: fix the timeout cmd
de4b1a350	chenqi	2022-09-01	ZBS-21367 Too many session update commands running while upgrading
ffb696189	yuhua	2022-09-05	ZBS-21404: get currect local session uuid
22eb4c828	Sai Wang	2022-08-22	PYZBS-3124 io_reroute: fix active ip array may lost item
923830baa	yutian	2022-07-09	ZBS-21027 reroute faile in ESXi 6.0
511081d55	yutian	2021-11-19	ZBS-23048 change_route failed
a63dbdf5a	yiwu.cai	2021-10-27	ZBS-20288 refine reroute to local scvm data ip scene
33b05beb2	yiwu.cai	2021-09-24	ZBS-13105 refine io-reroute about vmware deployment process
b5c13ea9b	Yiran Zhou	2020-11-12	Fixes TUNA-2346 Rename zbs_ip to vmware_access_ip for io-reroute
6affa569a	Jiewei Ke	2020-09-25	Fixes ZBS-14048 reroute: add zbs ip support
6e612f9b5	Jiewei Ke	2020-09-25	Fixes ZBS-14047 speedup reroute when scvm is down
e6ef92646	Luo Jiebin	2020-06-11	Fixes ZBS-11206 tuna: write ip to temporary file first and copy back
10aec829f	yutian	2019-02-25	Fixes ZBS-11192 grep failed when ip address look similar
cf3dffd80	yutian	2018-08-14	Fixes ZBS-10566 reroute failed after link aggregation to one scvm
a5dd26a86	yiran	2018-03-15	Fixes TUNA-152 change esxi get route cli
03a0c8e8a	yutian	2018-01-24	ZBS-9030 reroute support stretched cluster
e011e8baf	Qiuping Wang	2017-12-27	Fixes TUNA-57 migrate deployment code to tuna
```

```
# master
['ZBS-25495', 'ZBS-25463', 'ZBS-25338', 'TUNA-5367', 'ZBS-25380', 'ZBS-25364', 'ZBS-25322', 'ZBS-25311', 'ZBS-25296', 'ZBS-25257', 'ZBS-25234', 'TUNA-5042', 'TUNA-5031', 'TUNA-4907', 'TUNA-4802', 'TUNA-4845', 'ZBS-24967', 'ZBS-24956', 'PYZBS-3130', 'ZBS-24854', 'ZBS-24769', 'ZBS-24750', 'ZBS-24707', 'ZBS-13081', 'ZBS-13081', 'ZBS-21367', 'ZBS-21404', 'PYZBS-3124', 'ZBS-21027', 'ZBS-23048', 'ZBS-20288', 'ZBS-13105', 'TUNA-2346', 'ZBS-14048', 'ZBS-14047', 'ZBS-11206', 'ZBS-11192', 'ZBS-10566', 'TUNA-152', 'ZBS-9030', 'TUNA-57']

255bf00dd	chenqi	2023-06-19	ZBS-25495: do not diff manage ip list if not enable secondary ips
4ee1c6693	chenqi	2023-06-12	ZBS-25463: ioreroute login to rest server constantly if rest server is not ready
4cd8e0f51	chenqi	2023-05-25	ZBS-25338: change log path of ioreroute
529be1892	shibo zheng	2023-05-23	TUNA-5367 ci: support E501 line too long lint
0190f5346	chenqi	2023-05-15	ZBS-25380: change the ip sent by ioreroute heartbeat for rdma
e6a1e36c2	chenqi	2023-05-15	ZBS-25364: do not check local scvm when reroute manually
e2c7f6433	chenqi	2023-05-04	ZBS-25322: update reroute version for ioreroute
fb40ceaf4	chenqi	2023-04-28	ZBS-25311: set ICMP default payload size to 1400
1ef3e5847	chenqi	2023-04-26	ZBS-25296 ioreroute: change ip order for rerouting and exclude remote zone manage ip
a67a9e6b6	chenqi	2023-04-18	ZBS-25257 ioreroute send heartbeat to insight server
f52dc485e	chenqi	2023-04-13	ZBS-25234: revert upgrade py2 to py3 for ioreroute python file
409db08e4	shibo.zheng	2023-03-14	Fixes TUNA-5042 use ruff auto format code
4e9481a64	xu han	2023-03-14	Fixes TUNA-5031 fix tuna unittest in py3
e0fc3ab4e	Qiuping Wang	2023-02-23	Fixes TUNA-4907 Tuna:upgrade to python3 by manually
57733ae04	shibo.zheng	2023-02-20	Fixes TUNA-4802 manually fix tuna unittest errors
2c79fa02e	shibo.zheng	2023-02-09	Fixes TUNA-4845 use 2to3 and pyupgrade automated convert py2 to py3
be04adeb4	chenqi	2023-02-07	ZBS-24967 optimize ioreroute to handle zk leader crash
8194c1b3b	chenqi	2023-02-03	Fixes ZBS-24956: ioreroute check the session alive time of nfs target
f4280499f	Li Feng	2023-01-30	Fixes PYZBS-3130 ioreroute: fix the timeout cmd
abfb52261	chenqi	2022-12-29	ZBS-24854 Add login stage while calling zbs rest api
0c3fcfa0d	chenqi	2022-12-07	ZBS-24769 ioreroute: change log path to original path
2a013fcc8	chenqi	2022-12-05	ZBS-24750 ioreroute: Add schema check for result of session rest api
781424a31	chenqi	2022-11-16	ZBS-24707 ioreroute: update deploy script for reroute.py
add03f326	chenqi	2022-11-14	ZBS-13081 ioreroute: imporove handling scvm back to normal scenario
3fb3f80e0	chenqi	2022-10-21	ZBS-13081 ioreroute: use python to refactor ioreroute script
38d5415f1	chenqi	2022-09-01	ZBS-21367 Too many session update commands running while upgrading
80bd5b6b3	yuhua	2022-09-05	ZBS-21404: get currect local session uuid
c89722ad0	Sai Wang	2022-08-22	PYZBS-3124 io_reroute: fix active ip array may lost item
923830baa	yutian	2022-07-09	ZBS-21027 reroute faile in ESXi 6.0
511081d55	yutian	2021-11-19	ZBS-23048 change_route failed
a63dbdf5a	yiwu.cai	2021-10-27	ZBS-20288 refine reroute to local scvm data ip scene
33b05beb2	yiwu.cai	2021-09-24	ZBS-13105 refine io-reroute about vmware deployment process
b5c13ea9b	Yiran Zhou	2020-11-12	Fixes TUNA-2346 Rename zbs_ip to vmware_access_ip for io-reroute
6affa569a	Jiewei Ke	2020-09-25	Fixes ZBS-14048 reroute: add zbs ip support
6e612f9b5	Jiewei Ke	2020-09-25	Fixes ZBS-14047 speedup reroute when scvm is down
e6ef92646	Luo Jiebin	2020-06-11	Fixes ZBS-11206 tuna: write ip to temporary file first and copy back
10aec829f	yutian	2019-02-25	Fixes ZBS-11192 grep failed when ip address look similar
cf3dffd80	yutian	2018-08-14	Fixes ZBS-10566 reroute failed after link aggregation to one scvm
a5dd26a86	yiran	2018-03-15	Fixes TUNA-152 change esxi get route cli
03a0c8e8a	yutian	2018-01-24	ZBS-9030 reroute support stretched cluster
e011e8baf	Qiuping Wang	2017-12-27	Fixes TUNA-57 migrate deployment code to tuna
```
