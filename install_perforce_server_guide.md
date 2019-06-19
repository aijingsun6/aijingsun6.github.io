# perforce server
### 1.Installation
#### 1.1 Configure the Perforce package repository.
##### For CentOS/RHEL 6:
Create the file **/etc/yum.repos.d/perforce.repo**, with the following content:
```
[perforce]
name=Perforce
baseurl=http://package.perforce.com/yum/rhel/6/x86_64/
enabled=1
gpgcheck=1
```
##### For CentOS/RHEL 7:
Create the file **/etc/yum.repos.d/perforce.repo**, with the following content:
```
[perforce]
name=Perforce
baseurl=http://package.perforce.com/yum/rhel/7/x86_64/
enabled=1
gpgcheck=1
```

#### 1.2 Import the Perforce package signing key.
```
# rpm --import https://package.perforce.com/perforce.pubkey
```

#### 1.3 Install the appropriate Perforce service package.
```
# yum install helix-p4d
```

### 2. start service
```# p4d -r ${ROOT} -p ${PORT} -d```
*  -r ROOT:Specify the server root directory. Overrides P4ROOT. Default is current working directory.
*  -p PORT:Specify a port to listen to. Overrides P4PORT. Default 1666.
*  -d: Run as a daemon (in the background)
