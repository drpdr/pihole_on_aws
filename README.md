# Pi-Hole on AWS EC2 (Free Tier eligible)

CloudFormation template to create an EC2 Instance running Ubuntu Server 20.04 with [Pi-Hole](https://pi-hole.net/). Note that some regions do not include t3.micro in the Free Tier. Do a double check! **I do not assume any responsability for unexpected charges!!!** The template will not allow you to choose something different than t2.micro or t3.micro to limit any unwanted costs. Modify this constraint if you need more perfomance, e.g. small, medium instances. 

## Prerequisites

- An [AWS account](https://aws.amazon.com/free)
- A [KeyPair](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html)
- [AWS CLI](https://aws.amazon.com/cli/) (optional but preferred)

## Creating the Stack

You need to know your public IP and pre-existing [KeyPair](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html), e.g. your-keypair.pem.
The following commands assume you have configured AWS CLI properly by setting up your profile, i.e. ```aws configure```.

### From the Command Line     

On Unix/Linux systems:

```Bash
export MYIP=`curl https://ifconfig.co`
aws --profile yourprofile cloudformation \
create-stack --stack-name ph-ec2 \
--template-body file://path/to/ph-ec2.yaml \
--parameters ParameterKey=KeyName,ParameterValue=your-key-pair-name \
ParameterKey=ClientIP,ParameterValue=$MYIP/32 \
ParameterKey=InstanceType,ParameterValue=t3.micro \
--disable-rollback \
--capabilities CAPABILITY_NAMED_IAM
```

### From the Console

Import the stack as explained [here](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/resource-import-existing-stack.html) and follow the steps in the wizard.

## Getting the IP of the instance

```Bash
aws --profile yourprofile cloudformation \
describe-stacks --stack-name ph-ec2 \
--query "Stacks[0].Outputs[?OutputKey=='PublicIP'].​OutputValue" --output text
```

## Accessing the instance

```Bash
ssh -i "your-keypair.pem" ubuntu@instance-public-ip
```

Now you can [set](https://discourse.pi-hole.net/t/password-change/6648#:~:text=The%20Web%20interface%20password%20needs,removed%20from%20the%20web%20interface) the Web Interface password. 

## Deleting the Stack

### From the Command Line

```Bash
aws --profile yourprofile cloudformation \
delete-stack --stack-name ph-ec2
```

### From the Console

Just select it and click "Delete".

## TODO

- IPv6 support
- Automatic creation/deletion of the stack at certain times
