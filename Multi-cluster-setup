AWSTemplateFormatVersion: '2010-09-09'
Description: CloudFormation template for setting up a multi-cluster Kubernetes environment using Kind on an Ubuntu instance

Parameters:
  InstanceType:
    Description: EC2 instance type for the Ubuntu server
    Type: String
    Default: t2.micro
    AllowedValues:
      - t2.micro
      - t2.small
      - t2.medium
    ConstraintDescription: Must be a valid EC2 instance type.

Resources:
  EC2Instance:
    Type: AWS::EC2::Instance
    Properties:
      InstanceType: !Ref InstanceType
      ImageId: ami-0c55b159cbfafe1f0 # Ubuntu 20.04 LTS (Focal Fossa)
      KeyName: your-key-name # Replace with your EC2 key pair
      SecurityGroups:
        - !Ref InstanceSecurityGroup
      UserData:
        Fn::Base64: !Sub |
          #!/bin/bash
          set -ex
          # Update package repository
          apt-get update

          # Install dependencies
          apt-get install -y apt-transport-https ca-certificates curl software-properties-common

          # Install Docker
          curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add -
          add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
          apt-get update
          apt-get install -y docker-ce

          # Install Kind
          curl -Lo ./kind https://kind.sigs.k8s.io/dl/latest/kind-linux-amd64
          chmod +x ./kind
          mv ./kind /usr/local/bin/kind

          # Create Kind clusters
          kind create cluster --name cluster1 --wait 5m
          kind create cluster --name cluster2 --wait 5m

          # Verify clusters
          kind get clusters

  InstanceSecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription: Enable SSH access
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: '22'
          ToPort: '22'
          CidrIp: 0.0.0.0/0 # Allow SSH from anywhere. Change for security.

Outputs:
  InstanceId:
    Description: The Instance ID of the created EC2 instance
    Value: !Ref EC2Instance

  PublicIP:
    Description: The Public IP of the created EC2 instance
    Value: !GetAtt EC2Instance.PublicIp

  Cluster1Context:
    Description: kubectl context for cluster1
    Value: kind-cluster1

  Cluster2Context:
    Description: kubectl context for cluster2
    Value: kind-cluster2
