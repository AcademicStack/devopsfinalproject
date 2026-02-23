# Linux Fundamentals Quick Reference

## Navigation

```bash
pwd                        # print current directory
cd /etc                    # change to absolute path
cd ~                       # go to home directory
cd ..                      # go up one level
cd lab03                   # change to relative path
cd /home/user1             # absolute path
cd ../../home/user1        # relative path equivalent
```

## Listing Files

```bash
ls                         # list files in current directory
ls -la                     # list all files with permissions, owner, size, date
```

## Creating Files and Directories

```bash
touch filename.txt
touch file1 fileA
mkdir devops_practice
mkdir -p newfolder/subfolder
vi newfile.txt             # open vi editor (i=insert, ESC=exit insert, :wq=save & quit, :q!=quit no save)
vim index.html
```

## Copying, Moving, Deleting

```bash
cp file1 file2
cp folderA/file* folderB/
cp -r A/SubFolder* C
mv file2 fileB             # rename or move
rm file1
rm -d folderA              # remove empty directory
rm -rf folderB             # force remove directory and all contents
```

## Viewing File Contents

```bash
cat /etc/passwd
tree                       # visual directory structure (install first)
```

## User Management

```bash
sudo useradd devops_user -c "DevOps User"
sudo passwd devops_user
sudo groupadd devops
sudo usermod devops_user -aG devops
```

## Permissions

```bash
sudo chmod 740 A
sudo chmod u+rx devops_practice
sudo chmod g=rx foldername
sudo chown devops_user devops_practice
sudo chgrp devops devops_practice
```

**Permission Reference:**

| Octal | Meaning |
|---|---|
| 7 | rwx — read, write, execute |
| 6 | rw- — read, write |
| 5 | r-x — read, execute |
| 4 | r-- — read only |
| 0 | --- — no access |

*Example: `chmod 754` = owner rwx, group r-x, others r--*

## Networking

```bash
ip addr
ifconfig
ping google.com            # CTRL+C to stop
ping 172.17.0.2
wget https://example.com/file
curl https://example.com/file
```

## Package Management (Red Hat / Amazon Linux)

```bash
sudo dnf update
sudo dnf upgrade
sudo dnf -y update
sudo dnf install nginx
sudo dnf -y install tree
sudo dnf install -y stress
sudo yum install httpd
sudo yum install -y epel-release
sudo amazon-linux-extras enable epel
apt update                 # Debian/Ubuntu
apt install -y vim         # Debian/Ubuntu
```

## Service Management

```bash
sudo systemctl enable nginx
sudo systemctl start nginx
sudo systemctl status nginx
sudo systemctl start httpd
sudo service httpd start
```

## System Info and Monitoring

```bash
uname -a
df -h
ps
top
sudo stress --cpu 1 --timeout 120
sudo poweroff
```

## Downloading and Running a Setup Script

```bash
sudo curl -sSL https://example.com/setup.sh | bash
```

