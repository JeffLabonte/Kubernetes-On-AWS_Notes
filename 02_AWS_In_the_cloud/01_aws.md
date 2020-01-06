# AWS - Running Kubernetes

Create your own network. Keep the ID into a variable, in this case `VPC_ID`.

`VPC_ID=$(aws ec2 create-vpc --cidr-block 10.0.0.0/16 --query Vpc.VpcId --output text)`

Enable dns and dns hostname:

```bash
aws ec2 modify-vpc-attribute --enable-dns-support --vpc-id $VPC_ID
aws ec2 modify-vpc-attribute --enable-dns-hostnames --vpc-id $VPC_ID
```
`aws ec2 create-tags --resources $VPC_ID --tags Key=Name,Value=hopper Key=kubernetes.io/cluster/hopper,Value=shared`

Create one private route and one public, and we name them! 

```bash
PRIVATE_ROUTE_TABLE_ID=$(aws ec2 describe-route-tables --filters Name=vpc-id,Values=$VPC_ID --query "RouteTables[0].RouteTableId" --output=text)
PUBLIC_ROUTE_TABLE_ID=$(aws ec2 create-route-table --vpc-id $VPC_ID --query "RouteTable.RouteTableId" --output text)
aws ec2 create-tags --resources $PUBLIC_ROUTE_TABLE_ID --tags Key=Name,Value=hopper-public
aws ec2 create-tags --resources $PRIVATE_ROUTE_TABLE_ID  --tags Key=Name,Value=hopper-private
```
