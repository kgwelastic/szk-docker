<img src="https://welastic.pl/wp-content/uploads/2021/10/logo-black.svg" alt="Welastic logo" width="100" align="left">
<br><br>
<br><br>
<br><br>

# Configuration Kerberos Authentication for Docker on Linux

## Prerequisites

1. Active Directory Controller
2. Linux VM for Docker Host

## Tasks to do



### 1. Preparation
-   Add Linux VM to Active Directory
### 2. Configuration
- On DC create ServicePrincipalName(SPN).
- On DC create keytab file and copy it to Linux Host
- On Linux Docker Host copy /etc/krb5.conf to application directory
- On Linux Docker Host create launch.sh in application directory
- On Linux Docker Host create a dockerfile

## Preparation

### Add Linux VM to Active Directory

1.	Set hostname on Linux Machine 
``
  echo myhost.domain.com > /etc/hostname
``
2.	Specify full domain controller name in the /etc/hosts file
``
echo DC_IP dc_name.domain.com dc_name >> /etc/hosts
``
3. Set a DNS server on the configured computer
``
sudo vi /etc/resolvconf/resolv.conf.d/head
``

#### Change the file as follows

```
domain domain.com
search domain.com
nameserver <domain controller IP address>
nameserver 8.8.8.8 
```
4. Configure time synchronization

#### Install ntp tool

```
sudo apt-get install ntp
sudo vi /etc/ntp.conf
```
#### Change the file as follows

```
# You do need to talk to an NTP server or two (or three).
server dc_name.domain.com
```
5.	Reboot Linux machine
6.	Install a Kerberos client
``
sudo apt-get install krb5-user libpam-krb5 libpam-ccreds auth-client-config
``
7. Install Samba, Winbind and NTP
``
sudo apt-get install samba winbind ntp
``
8. Edit the /etc/krb5.conf file to add full domain name, domain controller name and the realm parameter
``
sudo vi /etc/krb5.conf
``
#### Change the file as follows
```
[libdefaults]
default_realm = DOMAIN.COM

[realms]
DOMAIN.COM = {
    kdc = DC_IP
    admin_server = DC_IP
    default_domain = DOMAIN.COM
}

[domain_realm]
.domain.com = DOMAIN.COM 
domain.com = DOMAIN.COM
```
9. Edit the /etc/samba/smb.conf file to add short domain name and full domain name
``
sudo vi /etc/samba/smb.conf
``
#### Change the file as follows
```
[global]
   workgroup = DOMAIN       
   realm = DOMAIN.COM  
```

10.	Enter the domain 

``
net ads join -U privilagedaccount@DOMAIN.COM
``

11.	Verify that authentication for an Active Directory user is successful      

``
kinit privilagedaccount@DOMAIN.COM
``
#### Enter password for account
``
  klist
``

## Configuration
1. On DC create ServicePrincipalName (SPN). 

#### In Powershell run commands

```
setspn -S HTTP/linux_host_name.domain.com windows_computer_name
setspn -S HTTP/LINUX_HOST_NAME@DOMAIN.COM WINDOWS_COMPUTER_NAME
```

2. On DC create keytab file
#### In Powershell run command

```
ktpass -princ HTTP/kg-vm- linux_host_name.domain.com@DOMAIN.COM -pass myKeyTabFilePassword -mapuser DOMAIN\WINDOWS_COMPUTER_NAME$ -pType KRB5_NT_PRINCIPAL -out c:\keytab_file_name.HTTP.keytab -crypto AES256-SHA1
```
3. Copy keytab_file_name.HTTP.keytab to Linux Docker Host
4. On Linux Docker Host create app direcotry and copy /etc/krb5.conf that directory
```
sudo mkdir /appdirecotry
sudo cp /etc/krb5.conf /appdirectory
```

5. On Linux Docker Host create launch.sh
``
sudo vi /appdirecotry/launch.sh
``

```
kinit HTTP/linux_host_name.domain.com@DOMAIN.COM -k -t /keytablocation/keytab_file_name.HTTP.keytab 
dotnet /app/Application.dll
```

6. On Linux Docker Host create dockerfile
``
sudo vi /appdirectory/dockerfile
``
```
FROM image

--mount type=bind,source=/keytablocation/,target=/keytablocation
--mount type=bind,source=/appdirectory/,target=/app

application_definition

apt update && apt dist-upgrade -y
apt install -y krb5-config krb5-user
cp /app/krb5.conf /etc/krb5.conf

ENTRYPOINT /launch.sh
```


## END

<br><br>

<p align="right">&copy; 2022 Welastic Sp. z o.o.<p>
