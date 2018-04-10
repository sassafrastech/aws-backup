# aws-backup

Forked by Sassafras

Bash script for Automatic EBS Snapshots and Cleanup on Amazon Web Services (AWS)

Original by Casey Labs Inc.

## Functionality

ebs-snapshot.sh will:

- Determine the instance ID of the EC2 server on which the script runs
- Gather a list of all volume IDs attached to that instance
- Take a snapshot of each attached volume
- The script will then delete all associated snapshots taken by the script that are older than `RETENTION_DAYS` days (default = 7)

Pull requests greatly welcomed!

## Options

Options are set via env vars.

* `AWS_CONFIG_FILE` - The location of your AWS config file.
* `RETAIN_FOREVER` - (Optional) If set to any value, old backups won't be deleted. Overrides all other retention settings.
* `RETENTION_DAYS` - (Optional) Number of days to retain backups. Default is 7.
* `RETAIN_DAY_OF_WEEK` - (Optional) An integer indicating a day of the week for which you would like to retain backups indefinitely. 1 is Monday, 2 is Tuesday, etc.
* `CLIENT`, `PROJECT` - (Optional) If given, AWS tags will be set of the same name will be set on the created snapshots. Useful for cost tracking.

## Requirements

**IAM User:** This script requires that new IAM user credentials be created, with the following IAM security policy attached:

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "Stmt1426256275000",
            "Effect": "Allow",
            "Action": [
                "ec2:CreateSnapshot",
                "ec2:CreateTags",
                "ec2:DeleteSnapshot",
                "ec2:DescribeSnapshots",
                "ec2:DescribeVolumes"
            ],
            "Resource": [
                "*"
            ]
        }
    ]
}
```

## Installation

```
sudo su -
cd /root

apt-get install python-pip -y
pip install awscli
aws configure

AWS Access Key ID: (Enter in the IAM credentials generated above.)
AWS Secret Access Key: (Enter in the IAM credentials generated above.)
Default region name: (The region that this instance is in: i.e. us-east-1, eu-west-1, etc.)
Default output format: (Enter "text".)

git clone https://github.com/sassafrastech/aws-backup
```

## Manual Test

```
SNAPSHOT_NAME_PREFIX=foo CLIENT="John Smith" PROJECT="Happy Appy" aws-backup/ebs-snapshot.sh
```

## Cron Setup

You should then setup a cron job in order to schedule a nightly backup. Example crontab jobs:
```
0 3 * * * AWS_CONFIG_FILE="/root/.aws/config" RETAIN_DAY_OF_WEEK=4 SNAPSHOT_NAME_PREFIX=foo CLIENT="John Smith" PROJECT="Happy Appy" /root/aws-backup/ebs-snapshot.sh
```
