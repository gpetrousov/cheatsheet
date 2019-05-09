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

## Route53

  - list hosted zones

```sh
aws route53 list-hosted-zones
```

  - list record sets from a hosted zone

```sh
aws route53 list-resource-record-sets --hosted-zone-id <zoneID from above>
```

## S3 api

  - list all s3 buckets

```sh
aws s3api list-buckets
```

## ELB

  - list all load balancers

```sh
aws elb describe-load-balancers 
```

## ELBv2

  - list all target groups

```sh
aws elbv2 describe-target-groups 
```

## ACM

  - list all certificates

```sh
aws acm list-certificates
```

