```
add_zbs_executable_uninstall(tower-license
add_zbs_executable_uninstall(zbs-bench ${zbs-bench-objects})
add_zbs_executable_uninstall(zbs_test ${zbs_test_SOURCES}

$ rg 'add_zbs_executable'
1034:add_zbs_executable(zbs-iscsi-redirectord iscsi_redirector/redirector_main.cc ${libzbs_OBJECTS})
1056:add_zbs_executable(zbs-taskd task/task_main.cc
1069:add_zbs_executable(zbs-inspectord inspector/inspector_main.cc
1083:add_zbs_executable(zbs-ntpd ntp/ntp_main.cc
1091:add_zbs_executable(dump-meta
1099:add_zbs_executable(zbs-license
1118:add_zbs_executable(zbs-metad
1158:add_zbs_executable(zbs-chunkd
1184:add_zbs_executable(zbs-aurorad
1192:add_zbs_executable(zbs-aurora-monitord
1220:    add_zbs_executable(data_client
1224:    add_zbs_executable(data_server
1242:add_zbs_executable(zbs-debug-tool
1258:add_zbs_executable(zbs-lsm-tool
```
