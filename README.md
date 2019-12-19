# NoXss  
![issues](https://img.shields.io/github/issues/lwzSoviet/NoXss)
![release](https://img.shields.io/badge/release-v1.0--beta-brightgreen)
![license](https://img.shields.io/github/license/lwzSoviet/NoXss)

NoXss is a cross-site script vulnerability scanner. It's very fast and suitable for big traffic data, and support reflected xss and dom-based xss.It can scan a single url or many urls from text file,also support to scan traffic from burpsuite.It has found some xss vulnerabilities in Bug Bounty program.
# Features
+ Payloads based on injection postion(not fuzz,more accurate,faster)
+ Multi-process
+ Async request(use gevent)
+ Support Dom-based xss(use chrome or phantomjs) and reflected xss
+ Support single url,file and traffic from Burpsuite
+ Traffic filter based on interface
+ Support speicial headers(referer,cookie,customized token,e.g.)
+ Support rescan quickly by id
# Directory
```
├── engine.py
├── logo
├── cookie.py
├── url.txt
├── cookie
│   └── test.com_cookie
├── traffic
│   ├── 49226b2cbc77b71b.traffic    #traffic file(pickled)
│   └── 49226b2cbc77b71b.reflect    #reflected file(pickled)
├── config.py
├── start.py
├── url.txt.filtered    #filtered urls
├── util.py
├── README.md
├── banner.py
├── requirements.txt
├── result
│   └── 49226b2cbc77b71b-2019_10_29_11_24_44.json   #result
├── model.py
└── test.py
```
# Screenshot
### NoXss:  
![s1](https://github.com/lwzSoviet/download/blob/master/images/s1.png)  
### Use --browser:   
![s2](https://github.com/lwzSoviet/download/blob/master/images/s2.png)
# Environment
Linux  
Python2.7  
Browser:Phantomjs or Chrome
# Install
### Ubuntu
+ 1.`apt-get install flex bison phantomjs`
+ 2.`pip install -r requirements`
### Centos
+ 1.`yum install flex bison phantomjs`
+ 2.`pip install -r requirements`
### MacOS
+ 1.`brew install grep findutils flex phantomjs`
+ 2.`pip install -r requirements`  
-----
*If you want to scan use "--browser=chrome",you must install chrome mannually. You can use "--check" to test the installation.*  
`python start.py --check`
# Usage
```
python start.py --url url --save
python start.py --url url --cookie cookie --browser chrome --save  
python start.py --file ./url.txt --save  
python start.py --burp ./test.xml --save
```
### Options    
**--url**        scan from url.  
**--id**        rescan from *.traffic file by task id.  
**--file**        scan urls from text file(like ./url.txt).  
**--burp**        scan *.xml(base64 encoded,like ./test.xml) from burpsuite proxy.  
**--process**        number of process.  
**--cookie**        use cookie.  
**--browser**        use browser(chrome or phantomjs) to scan,it's good at DOM-based xss but slow.  
**--save**        save results to ./result/id.json.
### How to scan data from Burpsuite
In Proxy,"Save items" ==> "test.xml"  
![s3](https://github.com/lwzSoviet/download/blob/master/images/s3.png)  
Then you can scan test.xml:  
`python start.py --burp=./test.xml`
### How to rescan
After scanning firstly,there will be taskid.traffic and taskid.reflect in ./traffic/:  
+ taskid.traffic: Web traffic of request(pickled).
+ taskid.reflect: Reflected result (pickled)that included reflected params,reflected position,type and others.  
NoXss will use these middle files to rescan:  
`python start.py --id taskid --save`
# How does NoXss work?
### Payloads
NoXss use only 5 payloads for scanning.These payloads are based on param's reflected position.Fewer payloads make it faster than fuzzing.
### Async&multi-process
NoXss is highly concurrent for using coroutine.
### Support dom-based xss
More and more page is using dom to render html.NoXss can parse it with using Phantomjs(default) or chrome.   
### Analysis files
Some xss is difficult to scan.NoXss will save some files in traffic/ for analysing,include:
+ *.traffic(traffic file during scanning)
+ *.reflect(param's reflected result)
+ *.redirect(30x response)
+ *.error(some error happened such as timeout,connection reset,etc.)
+ *.multipart(when request is multupart-formed,not easy to scan)
# Example
As you see in [Screenshot](https://github.com/lwzSoviet/NoXss#screenshot),the poc is `"http://dgwestore.nestlechinese.com/weixin/asset/Show.aspx?openid=abcdef&id=xssjs%22%3B"`.That means use the payload `"xssjs%22%3B"` in param "id":  
![poc](https://github.com/lwzSoviet/download/blob/master/images/poc.png)  
Then you can end the double qoutes use payload `xssjs";alert(1);//`.The final exploit is `http://dgwestore.nestlechinese.com/weixin/asset/Show.aspx?openid=abcdef&id=xssjs";alert(1)//`:  
![poc](https://github.com/lwzSoviet/download/blob/master/images/result.png) 
