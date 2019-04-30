# AWS

## ECS

  - list ecs clusters

```sh
aws ecs  list-clusters 
```

  - list ecs instances inside a cluster

```sh
aws ecs list-services --cluster <cluster>
```

  - list hosted zones

```sh
aws route53 list-hosted-zones
```

  - list record sets from a hosted zone

```sh
aws route53 list-resource-record-sets --hosted-zone-id <zoneID from above>
```

  - list all s3 buckets

aws s3api list-buckets

