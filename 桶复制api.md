1  前置条件：
源桶和目标桶都必须先要开启多版本， 接口是标准 aws s3 接口，可以参考 aws s3 的 api 文档


2 然后创建一个target， 创建target 过程中 server 端会查看  桶是否已经开启多版本，如果没有开启， SetRemoteTarget 接口会返回 403 错误
  如果创建成功，则返回 一个 "arn:rustfs:replication:us-east-1:725e9883-2f8f-40b5-8765-d15238588094:destbucket" 的 arn；


3 然后调用 getbucketreplicaiton-标准接口, 获取到已经存在的 bucket replication config， 当然可能没有，那么就是 404 了


4 把第二步得到的 arn 追加到 第三部获取到的配置之中， 然后调用 putbucketreplication 接口 覆盖已经存在的数据
<ReplicationConfiguration>
  <Rule>
    <ID>d1kug93dpk0hrlrihdu0</ID>       //
    <Status>Enabled</Status>
    <Priority>0</Priority>
    <DeleteMarkerReplication>
      <Status>Enabled</Status>          // 根据用户需要是否设置为enable
    </DeleteMarkerReplication>
    <DeleteReplication>
      <Status>Enabled</Status>          // 根据用户需要是否设置为 enable
    </DeleteReplication>
    <Destination>
      <Bucket>arn:rustfs:replication:us-east-1:725e9883-2f8f-40b5-8765-d15238588094:destbucket</Bucket>  // 这个为上面第二步获取的
    </Destination>
    <Filter>
      <Prefix></Prefix>
      <And></And>
      <Tag></Tag>
    </Filter>
    <SourceSelectionCriteria>
      <ReplicaModifications>
        <Status>Enabled</Status>
      </ReplicaModifications>
    </SourceSelectionCriteria>
    <ExistingObjectReplication>
      <Status>Enabled</Status>
    </ExistingObjectReplication>
  </Rule>
  <Role></Role>
</ReplicationConfiguration>



### SetRemoteTarget- 非s3 标准协议

----- REQUEST -----
Method: PUT
URL: http://0.0.0.0:9000/rustfs/admin/v3/set-remote-target?bucket=srcbucket
Headers:
  Host: 0.0.0.0:9000
  User-Agent: MinIO (linux; amd64) madmin-go/3.0.70 mc/DEVELOPMENT.2024-10-14T12-52-25Z
  Content-Length: 511
  Accept-Encoding: zstd,gzip
  Authorization: AWS4-HMAC-SHA256 Credential=rustfsadmin/20250706//s3/aws4_request, SignedHeaders=host;x-amz-content-sha256;x-amz-date, Sign
ature=52a25f8881dd34629e111b1cfbeb034f48851c80695c405abfa1e6b415afede5
  X-Amz-Content-Sha256: 17d493c3d00628b974ea074e1dd69aa7b421d98fa69fd6bdc7b58750a0bb89a6
  X-Amz-Date: 20250706T030612Z
Body:
{"sourcebucket":"","endpoint":"0.0.0.0:10005","credentials":{"accessKey":"rustfsadmin","secretKey":"rustfsadmin","expiration":"0001-01-01T00
:00:00Z"},"targetbucket":"destbucket","secure":false,"path":"auto","api":"s3v4","type":"replication","replicationSync":false,"healthCheckDur
ation":60000000000,"disableProxy":false,"resetBeforeDate":"0001-01-01T00:00:00Z","totalDowntime":0,"lastOnline":"0001-01-01T00:00:00Z","isOn
line":false,"latency":{"curr":0,"avg":0,"max":0},"edge":false,"edgeSyncBeforeExpiry":false}
----- RESPONSE -----
Status: 200
Headers:
  vary: origin, access-control-request-method, access-control-request-headers
  access-control-allow-origin: *
  access-control-expose-headers: *
  content-length: 82
  date: Sun, 06 Jul 2025 03:06:12 GMT
Body:
"arn:rustfs:replication:us-east-1:725e9883-2f8f-40b5-8765-d15238588094:destbucket"

body 收到之后需要保存起来，需要再 

### deletebucketreplication-标准接口， 参考 aws api 文档即可；
https://docs.aws.amazon.com/AmazonS3/latest/API/API_DeleteBucketReplication.html



### bucket location-标准接口， 参考 aws api 文档即可

https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetBucketLocation.html



### putbucketreplication-标准接口，参考 aws api 文档即可

https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutBucketReplication.html




### getbucketreplicaiton-标准接口，参考 aws api 文档即可

https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetBucketReplication.html 










### ListRemoteTargets-非 s3 标准协议

----- REQUEST -----

Method: GET

URL: http://0.0.0.0:9000/minio/admin/v3/list-remote-targets?bucket=srcbucket&type=

Headers:

  Host: 0.0.0.0:9000
  
  User-Agent: MinIO (linux; amd64) madmin-go/3.0.70 mc/DEVELOPMENT.2025-05-06T16-41-33Z
  
  Accept-Encoding: zstd,gzip
  
  Authorization: AWS4-HMAC-SHA256 Credential=rustfsadmin/20250626//s3/aws4_request, SignedHeaders=host;x-amz-content-sha256;x-amz-date, Signature=ff38b6e8056ab738e89e10601d10c36d468e965b6d1900fb7230e9f49f362474
  
  X-Amz-Content-Sha256: e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855
  
  X-Amz-Date: 20250626T162557Z
  
Body:

----- RESPONSE -----

Status: 200

Headers:

  vary: origin, access-control-request-method, access-control-request-headers
  access-control-allow-origin: *
  access-control-expose-headers: *
  content-length: 708
  date: Thu, 26 Jun 2025 16:25:57 GMT
  
Body:

[
  {
    "sourcebucket": "srcbucket",
    "endpoint": "0.0.0.0:9001",
    "credentials": {
      "accessKey": "rustfsadmin",
      "secretKey": "rustfsadmin",
      "session_token": null,
      "expiration": "0001-01-01T00:00:00Z"
    },
    "targetbucket": "destbucket",
    "secure": false,
    "path": "auto",
    "api": "s3v4",
    "arn": "arn:minio:replication:us-east-1:8dec15ee-2bb9-4b68-a1a6-59decfbf33ec:destbucket",
    "type": "replication",
    "region": null,
    "bandwidth_limit": null,
    "replicationSync": false,
    "storage_class": null,
    "healthCheckDuration": 60000000000,
    "disableProxy": false,
    "resetBeforeDate": "0001-01-01T00:00:00Z",
    "reset_id": null,
    "totalDowntime": 0,
    "last_online": null,
    "isOnline": false,
    "latency": {
      "curr": 0,
      "avg": 0,
      "max": 0
    },
    "deployment_id": null,
    "edge": false,
    "edgeSyncBeforeExpiry": false
  }
]

