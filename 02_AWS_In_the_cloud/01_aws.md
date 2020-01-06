# AWS - Running Kubernetes

Create your own network. Keep the ID into a variable, in this case `VPC_ID`.

`VPC_ID=$(aws ec2 create-vpc --cidr-block 10.0.0.0/16 --query Vpc.VpcId --output text)`
