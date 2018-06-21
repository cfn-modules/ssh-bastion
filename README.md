# cfn-modules: SSH bastion

SSH bastion (jump server, bastion host) based on Amazon Linux with a fixed public IP address (Elastic IP), running in a 1:1:1 auto scaling group, [alerting](https://www.npmjs.com/package/@cfn-modules/alerting), and [IAM user SSH access](https://github.com/widdix/aws-ec2-ssh).


## Install

> Install [Node.js and npm](https://nodejs.org/) first!

```
npm i @cfn-modules/ssh-bastion
```

## Usage

```
---
AWSTemplateFormatVersion: '2010-09-09'
Description: 'cfn-modules example'
Resources:
  Function:
    Type: 'AWS::CloudFormation::Stack'
    Properties:
      Parameters:
        VpcModule: !GetAtt 'Vpc.Outputs.StackName' # required
        AlertingModule: !GetAtt 'Alerting.Outputs.StackName' # optional
        HostedZoneModule: !GetAtt 'HostedZone.Outputs.StackName' # optional
        KeyName: '' # optional
        IAMUserSSHAccess: false # optional
        InstanceType: 't2.nano' # optional
        LogGroupRetentionInDays: 14 # optional
        SubDomainNameWithDot: 'ssh.' # optional
      TemplateURL: './node_modules/@cfn-modules/ssh-bastion/module.yml'
```

## SSH

### Single user: ec2-user

Specify the same `KeyName` parameter for the SSH bastion and all other stacks you want to connect to.

Use `ssh -J ec2-user@$BastionPublicIpAddress $TargetPrivateIpAddress` and replace `$BastionPublicIpAddress` with the `PublicIpAddress` output of the SSH bastion module stack; `$TargetPrivateIpAddress` with the private IP address of the EC2 instance you want to connect to.

### Personalized users (IAMUserSSHAccess := true)

Enable the `IAMUserSSHAccess` parameter for the SSH bastion and all other stacks you want to connect to.

Use `ssh -J $UserName@$BastionPublicIpAddress $TargetPrivateIpAddress` and replace `$UserName` with your IAM user name; `$BastionPublicIpAddress` with the `PublicIpAddress` output of the SSH bastion module stack; `$TargetPrivateIpAddress` with the private IP address of the EC2 instance you want to connect to.

## Parameters

<table>
  <thead>
    <tr>
      <th>Name</th>
      <th>Description</th>
      <th>Default</th>
      <th>Required?</th>
      <th>Allowed values</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>VpcModule</td>
      <td>Stack name of <a href="https://www.npmjs.com/package/@cfn-modules/vpc">vpc module</a></td>
      <td></td>
      <td>yes</td>
      <td></td>
    </tr>
    <tr>
      <td>AlertingModule</td>
      <td>Stack name of <a href="https://www.npmjs.com/package/@cfn-modules/alerting">alerting module</a></td>
      <td></td>
      <td>no</td>
      <td></td>
    </tr>
    <tr>
      <td>HostedZoneModule</td>
      <td>Stack name of <a href="https://www.npmjs.com/search?q=keywords:cfn-modules:HostedZone">module implementing HostedZone</a></td>
      <td></td>
      <td>no</td>
      <td></td>
    </tr>
    <tr>
      <td>KeyName</td>
      <td>Key name of the Linux user ec2-user to establish a SSH connection to the EC2 instance</td>
      <td></td>
      <td>no</td>
      <td></td>
    </tr>
    <tr>
      <td>IAMUserSSHAccess</td>
      <td>Synchronize public keys of IAM users to enable personalized SSH access (https://github.com/widdix/aws-ec2-ssh)?</td>
      <td>false</td>
      <td>no</td>
      <td>[true, false]</td>
    </tr>
    <tr>
      <td>InstanceType</td>
      <td>The instance type for the EC2 instance</td>
      <td>t2.nano</td>
      <td>no</td>
      <td></td>
    </tr>
    <tr>
    <tr>
      <td>LogGroupRetentionInDays</td>
      <td>Specifies the number of days you want to retain log events</td>
      <td>14</td>
      <td>no</td>
      <td>[1, 3, 5, 7, 14, 30, 60, 90, 120, 150, 180, 365, 400, 545, 731, 1827, 3653]</td>
    </tr>
    <tr>
      <td>SubDomainNameWithDot</td>
      <td>Name that is used to create the DNS entry with trailing dot, e.g. ${SubDomainNameWithDot}${HostedZoneName}. Leave blank for naked (or apex and bare) domain. Requires HostedZoneModule parameter!</td>
      <td>test.</td>
      <td>no</td>
      <td></td>
    </tr>
  </tbody>
</table>
