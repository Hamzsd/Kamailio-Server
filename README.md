# kamailio-server


# Installation
## Docker Installation 
Download and install [docker](https://docs.docker.com/get-docker/) on your local machine.

### Download Image
Get docker image from docker hub
`docker pull kamailio:5.3.3`

### DockerFile
Create a directory

```
mkdir /home/pi/kamailio
touch Dockerfile
```
Add the following configuration to *Dockerfile*
```
#Kamailio Test Stuff

FROM kamailio/kamailio:5.3.3-stretch

#Copy the config file onto the Filesystem of the Docker instance

COPY kamailio.cfg /etc/kamailio/

#Print out the current IP Address info

RUN ip add

#Expose port 5060 (SIP) for TCP and UDP

EXPOSE 5060

EXPOSE 5060/udp
```

### Build & Run
```
docker image build -t kamtest:0.1 .
docker run kamtest:0.1
```


## Manual Installation 
The following steps should be performed as the *root* user.

### Prerequisites

Update and upgrade package list: 
`apt-get update && upgrade`

The following packages are required before proceeding to the next steps.
```
-   g++: apt-get g++
    
-   git client: apt-get install git
    
-   gcc compiler: apt-get install gcc
    
-   flex - apt-get install flex
    
-   bison - apt-get install bison
    
-   libmysqlclient-dev - apt-get install libmysqlclient-dev
    
-   make - apt-get install make
    
-   if you want to enable more modules, some of them require extra libraries:
    

-   libssl - apt-get install libssl-dev
    
-   libcurl - apt-get install libcurl4-openssl-dev
    
-   libxml2 - apt-get install libxml2-dev
    
-   libpcre3 - apt-get install libpcre3-dev
```    

### Getting Sources from GIT

```
 mkdir -p /usr/local/src/kamailio-5.3
 cd /usr/local/src/kamailio-5.3
```
Download the sources from GIT using the following commands.

```
  git clone --depth 1 --no-single-branch git://git.sip-router.org/kamailio kamailio
  cd kamailio
  git checkout -b 5.3 origin/5.3
```

### Tuning Makefiles
```
make cfg
nano -w modules.lst
```
Add the following line *include_modules= db_mysql* in the file

#### Compile Kamailio
```
make all
```

#### Install Kamailio
```
make install
```
#### Export Variables
```
  PATH=$PATH:/usr/local/sbin
  export PATH
```

### Configure Database
Edit *kamctlrc* file to set database server type `nano -w /usr/local/etc/kamailio/kamctlrc`.
Uncomment the line *DBENGINE*. It should be set to `MYSQL`.

Once file is updated, run the following command to create the database`/usr/local/sbin/kamdbctl create`.

Default User & Passwords are:

- **kamailio** - (with default password 'kamailiorw') - user which has full access rights to 'kamailio' database
- **kamailioro** - (with default password 'kamailioro') - user which has read-only access rights to 'kamailio' database

### Edit Configuration Files

Edit the configuration file as follows:
`nano /usr/local/etc/kamailio/kamailio.cfg` and the following lines at the top of the file

```
#!define WITH_MYSQL
#!define WITH_AUTH
#!define WITH_USRLOCDB
```
### Init.d Script
```
cp /usr/local/src/kamailio-5.3/kamailio/pkg/kamailio/deb/debian/kamailio.init /etc/init.d/kamailio
  chmod 755 /etc/init.d/kamailio
  ```
  
### Start/Stop Kamailio
```
  /etc/init.d/kamailio start
  /etc/init.d/kamailio stop
```

### Add SIP User
`kamctl add test testpasswd`
