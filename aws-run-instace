#!/bin/bash
cat  instance_type.txt | while read line
do
    InstanceID=$(
               aws ec2 run-instances \
               --image-id ami-c13078b8 \
               --count 1 \
               --subnet-id subnet-59875e2e \
               --block-device-mappings '[{"DeviceName":"/dev/sda1","Ebs":{"VolumeSize":20,"VolumeType":"gp2"}}]' \
               --security-group-ids "sg-35b2634d" \
               --key-name hailong-eu-west-1 \
               --instance-type ${line} \
               --query 'Instances[0].[InstanceId]' | grep -o -E "i-\w{17}" )
    echo "##########"
    echo "InstanceID=${InstanceID}"
    aws ec2 create-tags --resources ${InstanceID} --tags Key=Name,Value="hailong-${line}"
done
