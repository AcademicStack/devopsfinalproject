# Cloud Infrastructure Quick Reference

## AWS Academy — Starting the Lab

1. Go to `https://awsacademy.instructure.com/login/canvas`
2. Open **AWS Academy Learner Lab → Modules → Launch AWS Academy Learner Lab**
3. Click **Start Lab** — wait for the circle next to "AWS" to turn green
4. Click **AWS** to open the console
5. Click **End Lab** when finished

## Get AWS Credentials (for CLI / GitHub Actions)

1. Click **AWS Details → Show** next to AWS CLI
2. Copy `AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`, `AWS_SESSION_TOKEN`

---

## EC2 — Launch an Instance

1. Services → Compute → EC2 → **Launch Instance**
2. Configure:

| Setting | Value |
|---|---|
| Name | DevOps Server / Final Project |
| AMI | Red Hat Enterprise Linux 9 — Free tier eligible |
| Instance Type | `t3.medium` (or `t2.micro` for free tier) |
| Key Pair | Create new → RSA → `.ppk` (Windows) or `.pem` (Mac/Linux) |
| Security Group | Create new → Allow SSH from Anywhere |
| Storage | 1x 10 GiB gp3 |

3. Click **Launch Instance**
4. After launch, click the instance ID → copy **Public IPv4 DNS**

## EC2 — Change Instance Type

1. Select instance → **Instance state → Stop instance**
2. Once stopped: **Actions → Instance settings → Change instance type**
3. Enter new type (e.g., `t3.medium`) → **Change**
4. **Instance state → Start instance**

![Description](https://devops-final-project-jc.s3.us-east-1.amazonaws.com/Screenshot%202026-02-23%20110144.png)


## EC2 — Connect via Browser Console

1. Select instance → **Connect → Connect**

## EC2 — Connect via SSH (Mac/Linux)

```bash
ssh -l ec2-user -i ~/.ssh/Western-Student ec2-XX-XX.compute-1.amazonaws.com
```

## EC2 — Basic Verification Commands

```bash
uname -a
df -h
sudo dnf -y update
```

## EC2 — Install and Start Apache

```bash
sudo yum install httpd
sudo systemctl start httpd
# or
sudo service httpd start
```

---

## Security Groups

1. EC2 Dashboard → **Security Groups** → select group (`launch-wizard-1`)
2. **Edit inbound rules → Add rule**

| Purpose | Type | Port | Source |
|---|---|---|---|
| SSH | SSH | 22 | Anywhere-IPv4 |
| Web server | HTTP | 80 | Anywhere-IPv4 |
| Docker docs | Custom TCP | 8081 | Anywhere-IPv4 |
| Docker CMS | Custom TCP | 8090 | Anywhere-IPv4 |
| Ping test | All ICMP – IPv4 | — | Anywhere-IPv4 |
| Lab (open all) | All traffic | All | Anywhere-IPv4 |

3. Click **Save rules**

---

## S3 — Create a Bucket

1. Services → Storage → S3 → **Create bucket**
2. Enter unique bucket name (e.g., `my-cicd-bucket-yourusername`)
3. Uncheck **Block all public access** → acknowledge
4. Click **Create bucket**

![description](https://devops-final-project-jc.s3.us-east-1.amazonaws.com/Screenshot%202026-02-23%20110949.png)

## S3 — Bucket Policy (Public Read)

Permissions tab → Bucket policy → Edit → paste (replace `YOUR_BUCKET_NAME`):

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::YOUR_BUCKET_NAME/*"
    }
  ]
}
```

## S3 — Upload Files

Objects tab → **Upload → Add files** → select files → **Upload**

## S3 — Static Website Hosting

1. Properties tab → **Static website hosting → Edit**
2. Enable → set Index document to `index.html`
3. Save — copy the **Bucket website endpoint** URL

## S3 — Enable Versioning

Properties tab → **Bucket Versioning → Edit → Enable → Save changes**

## S3 — Lifecycle Rule (Archive/Delete)

Management tab → **Create lifecycle rule**
- Transition to Glacier after 30 days
- Delete after 90 days (optional)

## S3 — Reference Image in Markdown

```markdown
![Description](https://bucket-name.s3.us-east-1.amazonaws.com/image.png)
```

---

## IAM — Create a User

1. Services → IAM → **Users → Add User**
2. Set username → enable **Console access**
3. Set password → uncheck "must reset at next sign-in"
4. **Attach policies directly** → search and select policy (e.g., `AmazonS3FullAccess`)
5. **Create user**

---

## CloudWatch — CPU Alarm

1. Search **CloudWatch** → Alarms → **Create alarm**
2. Select metric → EC2 → Per-Instance Metrics → **CPUUtilization**
3. Threshold: Greater than **70%**
4. Name and create the alarm

## Generate CPU Load to Trigger Alarm

```bash
sudo amazon-linux-extras enable epel
sudo yum install -y epel-release
sudo yum install -y stress
sudo stress --cpu 1 --timeout 120
```

---

## GitHub Secrets for AWS/EC2

Settings → Secrets and variables → Actions → **New repository secret**

![Description](https://devops-final-project-jc.s3.us-east-1.amazonaws.com/Screenshot%202026-02-23%20111535.png)


| Secret Name | Value |
|---|---|
| `EC2_SSH_KEY` | Contents of `.pem` key file |
| `EC2_HOST` | EC2 Public IPv4 DNS |
| `AWS_ACCESS_KEY_ID` | From AWS Details |
| `AWS_SECRET_ACCESS_KEY` | From AWS Details |
| `AWS_SESSION_TOKEN` | From AWS Details |
| `AWS_REGION` | `us-east-1` |
| `S3_BUCKET_NAME` | Your bucket name |

