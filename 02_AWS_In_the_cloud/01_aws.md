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

PRIVATE_SUBNET_ID=$(aws ec2 create-subnet --vpc-id $VPC_ID --availability-zone ca-central-1a --cidr-block 10.0.0.0/20 --query "Subnet.SubnetId" --output text)

aws ec2 create-tags --resources $PRIVATE_SUBNET_ID --tags Key=Name,Value=hopper-private-1a Key=kubernetes.io/cluster/hopper,Value=owned Key=kubernetes.io/role/internal-elb,Value=1

PUBLIC_SUBNET_ID=$(aws ec2 create-subnet --vpc-id $VPC_ID --availability-zone ca-central-1a --cidr-block 10.0.16.0/20 --query "Subnet.SubnetId" --output text)

aws ec2 create-tags --resources $PUBLIC_SUBNET_ID  --tags Key=Name,Value=hopper-public-1a Key=kubernetes.io/cluster/hopper,Value=owned Key=kubernetes.io/role/internal-elb,Value=1

aws ec2 associate-route-table --subnet-id $PUBLIC_SUBNET_ID --route-table-id $PUBLIC_ROUTE_TABLE_ID

INTERNET_GATEWAY_ID=$(aws ec2 create-internet-gateway --query "InternetGateway.InternetGatewayId" --output text)

aws ec2 attach-internet-gateway --internet-gateway-id $INTERNET_GATEWAY_ID --vpc-id $VPC_ID

aws ec2 create-route --route-table-id $PUBLIC_ROUTE_TABLE_ID --destination-cidr-block 0.0.0.0/0 --gateway-id $INTERNET_GATEWAY_ID

NAT_GATEWAY_ALLOCATION_ID=$(aws ec2 allocate-address --domain vpc --query AllocationId --output text)

NAT_GATEWAY_ID=$(aws ec2 create-nat-gateway --subnet-id $PUBLIC_SUBNET_ID --allocation-id $NAT_GATEWAY_ALLOCATION_ID --query "NatGateway.NatGatewayId" --output text)

aws ec2 create-route --route-table-id $PRIVATE_ROUTE_TABLE_ID --destination-cidr-block 0.0.0.0/0 --nat-gateway-id $NAT_GATEWAY_ID
