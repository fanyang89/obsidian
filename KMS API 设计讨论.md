
```
GET /kms/kmip/A  {}

/kms/aws/

/kms

/kms/v1/kmip /....
/kms/v2

kmip serivce = A
aws  serivce = B

GET    /path
POST   /path
PUT    /path
DELETE /kms?id=KMS-AWS-123 {body}

POST   /kms/rotate {body}

GET    /kms

{
  type: aws
	id:   KMS-AWS-123
	name: f
	descprtion:
	
	kmip: null
	aws:  {
	
	}
}

GET /kms

[
{ type: aws, ... kmip: { name: xxx }, aws: { name: xxx } }
{ type: kmip }
]
```