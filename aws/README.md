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

## EC2

  - describe network interfaces

```sh
aws ec2 describe-network-interfaces
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

  - create a listener rule to redirect to HTTPS and port 443 with status code 301 for *.example.com domain

```sh
 aws --profile=default --region=us-east-1 elbv2 create-rule --listener-arn arn:aws:elasticloadbalancing:us-east-1:560303188884:listener/app/test-redirect/713af720ce825f1d/5d6ace4ce0e93a57 \\
 --priority 10 --conditions Field=host-header,Values='*.example.com'  --actions Type=redirect,RedirectConfig='{'Protocol'='HTTPS','Port'='443','StatusCode'='HTTP_301'}'
 ```

  - modify a listener rule to redirect to HTTPS and port 443 with status code 301

```sh
 aws --profile=default --region=us-east-1 elbv2 modify-rule \\
 --rule-arn arn:aws:elasticloadbalancing:us-east-1:560303188884:listener-rule/app/test-redirect/713af720ce825f1d/5d6ace4ce0e93a57/413d44b250c8cad1 \\
 --actions Type=redirect,RedirectConfig='{'Protocol'='HTTPS','Port'='443','StatusCode'='HTTP_301'}'
 ```

## ACM

  - list all certificates

```sh
aws acm list-certificates
```

