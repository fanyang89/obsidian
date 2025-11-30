```java
final public static String CONFIG_NODE = "/zookeeper/config";
private static final String configZookeeper = ZooDefs.CONFIG_NODE;
```

```java
// org.apache.zookeeper.server.PrepRequestProcessor#pRequest2Txn
case OpCode.delete:
		zks.sessionTracker.checkSession(request.sessionId, request.getOwner());
		DeleteRequest deleteRequest = (DeleteRequest)record;
		if(deserialize)
				ByteBufferInputStream.byteBuffer2Record(request.request, deleteRequest);
		String path = deleteRequest.getPath();
		String parentPath = getParentPathAndValidate(path); // 校验 path
		ChangeRecord parentRecord = getRecordForPath(parentPath);
		ChangeRecord nodeRecord = getRecordForPath(path);
		checkACL(zks, parentRecord.acl, ZooDefs.Perms.DELETE, request.authInfo);
		checkAndIncVersion(nodeRecord.stat.getVersion(), deleteRequest.getVersion(), path);
		if (nodeRecord.childCount > 0) {
				throw new KeeperException.NotEmptyException(path);
		}
		request.setTxn(new DeleteTxn(path));
		parentRecord = parentRecord.duplicate(request.getHdr().getZxid());
		parentRecord.childCount--;
		addChangeRecord(parentRecord);
		addChangeRecord(new ChangeRecord(request.getHdr().getZxid(), path, null, -1, null));
		break;

// org.apache.zookeeper.server.PrepRequestProcessor#getParentPathAndValidate
private String getParentPathAndValidate(String path) throws BadArgumentsException {
	int lastSlash = path.lastIndexOf('/');
	if (lastSlash == -1 || path.indexOf('\0') != -1 || zks.getZKDatabase().isSpecialPath(path)) {
		throw new BadArgumentsException(path);
	}
	return path.substring(0, lastSlash);
}
```

Special path 如下：

- `/`
- `/zookeeper`
- `/zookeeper/quota`
- `/zookeeper/config`
  这些 path 不能被删除。
