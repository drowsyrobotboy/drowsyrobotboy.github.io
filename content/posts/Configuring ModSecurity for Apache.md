---
title: Configuring ModSecurity for Apache
date: 2021-03-09T12:00:00+05:30
draft: false
tags:
  - apache
  - security
  - self-hosted
categories:
  - tech
---
### Prerequisites
Install latest versions (devel) of LibCurl and LibXML2

```
# yum install libxml2-devel
# yum install libcurl-devel
```

### Download Source Code
Go to [https://modsecurity.org/download.html](https://modsecurity.org/download.html?ref=drowsyrobotboy.com) and download the latest Stable Release Quality Source Code for Apache

```
# wget https://www.modsecurity.org/tarball/2.9.3/modsecurity-2.9.3.tar.gz
```

### Extracting and Installing Library
Extract the source code

```
# tar -xzvf modsecurity-2.9.3.tar.gz
```

Navigate into the newly extracted folder and run the following commands

```
# cd modsecurity-2.9.3

# ./configure --with-apxs=/opt/apache-httpd-2.4.41/bin/apxs
# make
# make install
```

Once the installation completes, the `mod_security2.so` library file will be extracted to `/usr/local/modsecurity/lib` and `/opt/apache-httpd-2.4.41/modules`

### Default Config file and Core Rule Sets
Download Default OWASP Core Rule Set

```
  # wget https://github.com/coreruleset/coreruleset/archive/v3.2.0.tar.gz
  # tar -xvzf v3.2.0.tar.gz

  # ln -s coreruleset-3.2.0/ crs
```

Downloading Default ModSecurity Config file

```
  # cd /opt/apache-httpd-2.4.41/conf/
  # mkdir modsecurity.d
  # cd modsecurity.d/
  
  # wget https://raw.githubusercontent.com/SpiderLabs/ModSecurity/v3/master/modsecurity.conf-recommended
  # cp modsecurity.conf-recommended modsecurity.conf

  # wget https://raw.githubusercontent.com/SpiderLabs/ModSecurity/v3/master/unicode.mapping
```

## Configuration of ModSecurity for Apache
### httpd.conf
Add following lines to httpd.conf

```
LoadFile /usr/lib64/libxml2.so.2
LoadFile /usr/lib64/liblua-5.1.so

LoadModule security2_module modules/mod_security2.so

<IfModule security2_module>
    Include conf/modsecurity.d/modsecurity.conf
    Include conf/modsecurity.d/crs/crs-setup.conf
    Include conf/modsecurity.d/crs/rules/*.conf
</IfModule>
```

Also uncomment following line to httpd.conf

```
#LoadModule unique_id_module modules/mod_unique_id.so
```

### extra/httpd-ssl.conf or wherever you have VirtualHosts defined
Add the following lines to all VirtualHost(s) where you want to include ModSecurity

```
SecRuleEngine On
SecRule ARGS:testparam "@contains test" "id:1234,deny,status:403,msg:'Our test rule has triggered'"
```
#### _Restart_ Apache
## Testing
Navigate to `https://your-domain.tld/?testparam=test` You must get a HTTP 403 Response.