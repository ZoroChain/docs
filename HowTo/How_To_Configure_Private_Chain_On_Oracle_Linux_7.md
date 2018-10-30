How To Configure Private Chain On Oracle Linux 7
================================================

1. Prerequirement
-----------------

Download OL7.5 iso,
https://blogs.oracle.com/linux/oracle-linux-iso-images-download-options
Install it, and then install docker from
http://yum.oracle.com/repo/OracleLinux/OL7/addons/x86\_64/index.html
before you run yum install docker, make sure these channel are enabled
in /etc/yum.repos.d/public-yum-ol7.repo.

ol7\_latest, ol7\_optional\_latest, ol7\_addons, ol7\_developer, and
http://yum.oracle.com/repo/OracleLinux/OL7/developer\_EPEL/x86\_64/index.html

2. Build neo-cli docker image
-----------------------------

set proxy for docker if required: \# cat
/etc/systemd/system/docker.service.d/http-proxy.conf [Service]
Environment="HTTP\_PROXY=http://replacewithyourname:80/"
"HTTPS\_PROXY=http://<replacewithyourname>:80/"

docker pull oraclelinux:7 docker run --name neo-node1 -it oraclelinux:7
\#yum install -y rh-dotnet20-build scl-utils-build leveldb-devel
libunwind liub libunwind-devel libcurl-devel openssl-devel zlib
libicu-devel unzip libsqlite3x-devel rh-dotnet20-dotnet-sdk
rh-dotnet20-dotnet-runtime git \#git clone
https://github.com/NewEconoLab/neo-cli-docker.git \#cd /home/ && wget
https://github.com/neo-project/neo-cli/releases/download/v2.8.0/neo-cli-linux-x64.zip
\#unzip neo-cli-linux-x64.zip \#cd /home/neo-cli \#replace config.json
with the new one like this: { "ApplicationConfiguration": { "Paths": {
"Chain": "ChainPrivate", "Notifications": "NotificationsPrivate" },
"P2P": { "Port": 10333, "WsPort": 10334 }, "RPC": { "Port": 10332,
"SslCert": "", "SslCertPassword": "" } } }

replace protocol.json with:
===========================

{ "ProtocolConfiguration": { "Magic": 9726190, "AddressVersion": 23,
"StandbyValidators": [
"0249b419692a17fb519c3b42ffbe82ae34855a45e54783a9bc940135b1c2753ece",
"03298a54cdfb2b5ffae0638c60633de24e3956b76a44867475e81db5ce87b2a16b",
"02b795b3322f0a171c7e7658371131c0caa77a9279818b85b9d0728a5a63452ff8",
"0366440bd2a1fe4e0d1dcb9bf35995c5c6e858af846abef145064ab2b5e8ebfce8" ],
"SeedList": [ "cli0:10333", "cli1:10333", "cli2:10333", "cli3:10333", ],
"SystemFee": { "EnrollmentTransaction": 1000, "IssueTransaction": 500,
"PublishTransaction": 500, "RegisterTransaction": 10000 } } }

git clone https://github.com/NewEconoLab/neo-cli-docker.git
===========================================================

cp neo-cli-docker/dockerfile/privatenet/docs/neo\_private\_net\*.json to /home/neo-cli
======================================================================================

Then we can save the neo-node1 contains as new image docker commit -m
"ol7 neo-cli test" -a "neo-cli node" f50e0c6bcf4b neo-cli:v0.3

3. Start 4 nodes
----------------

We use docker-compse to start 5 nodes, cli0,1,2,3 are used as consensus
nodes, cli4 used as regular peer nodes pip install -U docker-compose

prepare compose file: \#cat docker-compose.yml

version: "3"

services:

cli0: image: neo-cli:v0.3 container\_name: cli0 ports: -
"10331-10334:10331-10334" networks: - nodeLink volumes: -
"/data/cli0:/home/cli/ChainPrivate" stdin\_open: true tty: true

cli1: image: neo-cli:v0.3 container\_name: cli1 ports: -
"20331-20334:10331-10334" networks: - nodeLink volumes: -
"/data/cli1:/home/cli/ChainPrivate" stdin\_open: true tty: true
depends\_on: - cli0

cli2: image: neo-cli:v0.3 container\_name: cli2 ports: -
"30331-30334:10331-10334" networks: - nodeLink volumes: -
"/data/cli2:/home/cli/ChainPrivate" stdin\_open: true tty: true
depends\_on: - cli1

cli3: image: neo-cli:v0.3 container\_name: cli3 ports: -
"40331-40334:10331-10334" networks: - nodeLink volumes: -
"/data/cli3:/home/cli/ChainPrivate" stdin\_open: true tty: true
depends\_on: - cli2

cli4: image: neo-cli:v0.3 container\_name: cli4 ports: -
"50331-50334:10331-10334" networks: - nodeLink volumes: -
"/data/cli4:/home/cli/ChainPrivate" stdin\_open: true tty: true

networks: nodeLink: driver: bridge

4. login cli0,1,2,3,4 and start consensus
-----------------------------------------

docker attach cli0 scl enable dotnet bash dotnet neo-cli.dll /rpc neo\>
open wallat neo\_private\_net\_0.json neo\> start consensus Then press
Ctrl+p, and hold Ctrl, press q.

docker attach cli1 scl enable dotnet bash dotnet neo-cli.dll /rpc neo\>
open wallat neo\_private\_net\_1.json Then press Ctrl+p, and hold Ctrl,
press q.

docker attach cli2 scl enable dotnet bash dotnet neo-cli.dll /rpc neo\>
open wallat neo\_private\_net\_2.json Then press Ctrl+p, and hold Ctrl,
press q.

docker attach cli3 scl enable dotnet bash dotnet neo-cli.dll /rpc neo\>
open wallat neo\_private\_net\_3.json Then press Ctrl+p, and hold Ctrl,
press q.

Now We can say we have our own private Chain.

5.  Login cli4, start neo-cli for test

For regular peer node, need to replace config.json with: \#cat
config.json { "ApplicationConfiguration": { "Paths": { "Chain":
"ChainPrivate", "ApplicationLogs": "NotificationsPrivate" }, "P2P": {
"Port": 10333, "WsPort": 10334 }, "RPC": { "Port": 10332, "SslCert": "",
"SslCertPassword": "" }, } }

There is only one line difference: -"Notifications":
"NotificationsPrivate" +"ApplicationLogs": "NotificationsPrivate"
