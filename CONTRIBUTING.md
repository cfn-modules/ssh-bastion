# cfn-modules Contributor Guide

### Generate ImageId mappings
To update the ImageId map execute the following lines in your terminal:

```
for region in $(aws ec2 describe-regions --query "Regions[].RegionName" --output text); do ami=$(aws --region $region ec2 describe-images --filters "Name=name,Values=amzn-ami-hvm-2018.03.0.20210224.0-x86_64-gp2" --query "Images[0].ImageId" --output "text"); printf "'$region':\n  ImageId: '$ami'\n"; done
```
