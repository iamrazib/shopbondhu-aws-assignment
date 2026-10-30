# ShopBondhu — Command Execution Log

## 0. Windows PowerShell — local environment

```powershell
ssh -V
git --version

New-Item -ItemType Directory -Force "$HOME\.ssh"
Move-Item "$HOME\Downloads\shopbondhu-key.pem" "$HOME\.ssh\shopbondhu-key.pem"

Set-Location "$HOME\Documents\shopbondhu-aws-assignment"
Get-ChildItem
```

## 1. VPC and subnet setup — AWS Console actions

1. Selected AWS Region: `ap-south-1`
2. Created custom VPC `shopbondhu-vpc` (`10.0.0.0/16`).
3. Created public subnet `shopbondhu-public-subnet` (`10.0.1.0/24`, public IP auto-assign enabled).
4. Created private subnet `shopbondhu-private-subnet` (`10.0.2.0/24`, public IP auto-assign disabled).
5. Created and attached `shopbondhu-igw`.
6. Allocated Elastic IP and created **Zonal/Public** NAT Gateway `shopbondhu-nat` in the public subnet.
7. Created public route table and set `0.0.0.0/0 -> IGW`, associated with public subnet.
8. Created private route table and set `0.0.0.0/0 -> NAT`, associated with private subnet.
9. Launched `shopbondhu-web` and `shopbondhu-backend` using Ubuntu and the correct subnet/security group.

## 2. Windows PowerShell — connect to the public EC2

```powershell
ssh -i "$HOME\.ssh\shopbondhu-key.pem" ubuntu@13.201.86.168
```


## 3. Ubuntu terminal on shopbondhu-web — install Nginx

```bash
sudo apt update
sudo apt install -y nginx curl
sudo systemctl start nginx
sudo systemctl enable nginx
sudo nginx -t
sudo systemctl status nginx --no-pager
curl -I http://localhost
```

## 4. Windows PowerShell — upload frontend (run inside project directory)

```powershell
scp -i "$HOME\.ssh\shopbondhu-key.pem" -r .\frontend ubuntu@13.201.86.168:/home/ubuntu/shopbondhu-frontend
```

## 5. Ubuntu terminal on shopbondhu-web — publish site

```bash
sudo rm -f /var/www/html/index.nginx-debian.html
sudo cp -a /home/ubuntu/shopbondhu-frontend/. /var/www/html/
sudo nginx -t
sudo systemctl reload nginx
ls -lah /var/www/html
curl -I http://localhost
```

## 6. Windows PowerShell — connect to private EC2 via jump host

```powershell
Set-Service ssh-agent -StartupType Manual
Start-Service ssh-agent
ssh-add "$HOME\.ssh\shopbondhu-key.pem"
ssh -i "$HOME\.ssh\shopbondhu-key.pem" -J ubuntu@13.201.86.168 ubuntu@10.0.2.93


Then on **shopbondhu-web** (while in the SSH-agent-forwarded session):

```bash
ssh ubuntu@10.0.2.93
```


## 7. Ubuntu terminal on shopbondhu-backend — NAT validation

```bash
hostname
hostname -I
sudo apt update
curl -I https://www.google.com
curl -4 https://checkip.amazonaws.com
```

## 8. Windows PowerShell — verify direct private SSH is unavailable

```powershell
Test-NetConnection -ComputerName 10.0.2.93 -Port 22

## 9. Git — commit history and GitHub push 

```powershell
# At milestone 1: frontend source and .gitignore
git init -b main
git add frontend .gitignore
git commit -m "feat: create ShopBondhu responsive landing page"

# At milestone 2: network setup and documentation
git add README.md commands.md screenshots
git commit -m "docs: document custom AWS VPC, subnets and routes"

# At milestone 3: tested deployment and screenshots
git add README.md commands.md screenshots
git commit -m "docs: verify Nginx deployment and backend NAT tests"

# At milestone 4: remove billable NAT resources, document final cleanup
git add README.md commands.md screenshots
git commit -m "docs: record NAT Gateway deletion and EIP release"

git log --oneline
git remote add origin https://github.com/iamrazib/shopbondhu-aws-assignment.git
git push -u origin main
```