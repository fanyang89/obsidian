```java
[INFO] Results:
[INFO] 
[ERROR] Failures: 
[ERROR]   FileChangeWatcherTest.testCallbackErrorDoesNotCrashWatcherThread:269
[ERROR]   FileChangeWatcherTest.testCallbackWorksOnFileAdded:178
[ERROR]   FileChangeWatcherTest.testCallbackWorksOnFileChanges:99 Wrong number of events expected:<1> but was:<0>
[ERROR]   FileChangeWatcherTest.testCallbackWorksOnFileDeleted:219
[ERROR]   FileChangeWatcherTest.testCallbackWorksOnFileTouched:142

[ERROR]   ServerIdTest.setUp:81->ClientBase.setUpWithServerId:524->ClientBase.startServer:537->ClientBase.startServerInstance:424 waiting for server up

// [ERROR]   QuorumPeerMainTest.testBadPeerAddressInQuorum:489 complains about host
[ERROR]   QuorumPeerMainTest.testLeaderElectionWithDisloyalVoter:1508->testLeaderElection:1573 Server 0 should have joined quorum by now
[ERROR]   QuorumPeerMainTest.testQuorumV6:157->testQuorumInternal:101 waiting for server 1 being up
[ERROR]   QuorumAuthUpgradeTest.testAuthLearnerAgainstNoAuthRequiredServer:126->QuorumAuthTestBase.startQuorum:78 waiting for server 0 being up

[ERROR]   QuorumAuthUpgradeTest.testAuthLearnerAgainstNullAuthServer:106->QuorumAuthTestBase.startQuorum:78 waiting for server 0 being up
[ERROR]   QuorumAuthUpgradeTest.testAuthLearnerServer:148->QuorumAuthTestBase.startQuorum:78 waiting for server 0 being up
[ERROR]   QuorumAuthUpgradeTest.testRollingUpgrade:194->restartServer:232 waiting for server0being up

[ERROR]   QuorumDigestAuthTest.testValidCredentials:82->QuorumAuthTestBase.startQuorum:78 waiting for server 0 being up

[ERROR]   QuorumKerberosAuthTest.testValidCredentials:106->QuorumAuthTestBase.startQuorum:78 waiting for server 0 being up
[ERROR]   QuorumKerberosHostBasedAuthTest.testConnectBadServer:152->QuorumAuthTestBase.startQuorum:78 waiting for server 0 being up
[ERROR]   QuorumKerberosHostBasedAuthTest.testValidCredentials:131->QuorumAuthTestBase.startQuorum:78 waiting for server 0 being up

// [ERROR]   ClientPortBindTest.testBindByAddress:91 waiting for server up

[ERROR] Errors: 
[ERROR] org.apache.zookeeper.test.ReconfigTest.testJMXBeanAfterRemoveAddOne(org.apache.zookeeper.test.ReconfigTest)
[ERROR]   Run 1: ReconfigTest.testJMXBeanAfterRemoveAddOne:906 » Runtime java.net.BindException...
```


```java
[INFO] Results:
[INFO] 
[ERROR] Failures: 
[ERROR]   FileChangeWatcherTest.testCallbackErrorDoesNotCrashWatcherThread:269
[ERROR]   FileChangeWatcherTest.testCallbackWorksOnFileAdded:178
[ERROR]   FileChangeWatcherTest.testCallbackWorksOnFileChanges:99 Wrong number of events expected:<1> but was:<0>
[ERROR]   FileChangeWatcherTest.testCallbackWorksOnFileDeleted:219
[ERROR]   FileChangeWatcherTest.testCallbackWorksOnFileTouched:142
[ERROR]   QuorumPeerMainTest.testBadPeerAddressInQuorum:489 complains about host
[ERROR]   QuorumAuthUpgradeTest.testAuthLearnerAgainstNoAuthRequiredServer:126->QuorumAuthTestBase.startQuorum:78 waiting for server 0 being up
[ERROR]   QuorumAuthUpgradeTest.testAuthLearnerAgainstNullAuthServer:106->QuorumAuthTestBase.startQuorum:78 waiting for server 0 being up
[ERROR]   QuorumAuthUpgradeTest.testAuthLearnerServer:148->QuorumAuthTestBase.startQuorum:78 waiting for server 0 being up
[ERROR]   QuorumAuthUpgradeTest.testRollingUpgrade:194->restartServer:232 waiting for server0being up
[ERROR]   QuorumDigestAuthTest.testValidCredentials:82->QuorumAuthTestBase.startQuorum:78 waiting for server 0 being up

[ERROR]   QuorumPeerMainTest.testQuorumV6:157->testQuorumInternal:101 waiting for server 1 being up
[ERROR]   ClientPortBindTest.testBindByAddress:91 waiting for server up
```

```
[ERROR] Failures: 
[ERROR]   FileChangeWatcherTest.testCallbackErrorDoesNotCrashWatcherThread:269
[ERROR]   FileChangeWatcherTest.testCallbackWorksOnFileAdded:178
[ERROR]   FileChangeWatcherTest.testCallbackWorksOnFileChanges:99 Wrong number of events expected:<1> but was:<0>
[ERROR]   FileChangeWatcherTest.testCallbackWorksOnFileDeleted:219
[ERROR]   FileChangeWatcherTest.testCallbackWorksOnFileTouched:142

[ERROR]   ServerIdTest.setUp:81->ClientBase.setUpWithServerId:524->ClientBase.startServer:537->ClientBase.startServerInstance:424 waiting for server up

[ERROR]   QuorumPeerMainTest.testBadPeerAddressInQuorum:489 complains about host
[ERROR]   QuorumPeerMainTest.testQuorumV6:157->testQuorumInternal:101 waiting for server 1 being up

[ERROR]   ClientPortBindTest.testBindByAddress:91 waiting for server up

[ERROR] Errors: 
[ERROR] org.apache.zookeeper.test.ReconfigTest.testJMXBeanAfterRemoveAddOne(org.apache.zookeeper.test.ReconfigTest)
[ERROR]   Run 1: ReconfigTest.testJMXBeanAfterRemoveAddOne:906 » Runtime java.net.BindException...
```