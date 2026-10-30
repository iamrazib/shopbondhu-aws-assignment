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
