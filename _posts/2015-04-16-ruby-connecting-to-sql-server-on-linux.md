---
title:  "Ruby Connecting to SQL Server on Linux"
date:   2015-04-16
---

Getting ruby on Linux to connect to a SQL Server can be a challenge. Let's take a look at how I was able to accomplish it.

First, many thanks to [this article](http://benscheirman.com/2011/01/connecting-to-sql-server-2008-from-ruby-on-linux/) from Ben Scheirman. It went a long way to helping me get this working. I am redocumenting only because there were some additional steps that I had to go through to get it done.

## Environment
This was done on LinuxMint 17.

## Get unixODBC
We are planning on connecting to the MSSQL server through ODBC, so we need to grab the unixODBC software

```shell
sudo apt-get install unixodbc-dev
```

## Get FreeTDS
TDS is the communication protocol that MSSQL Server uses. FreeTDS is a free library to communicate over this protocol.

```shell
sudo apt-get install freetds-common freetds-dev freetds-bin tdsodbc
```

## Configuration
There are three files that need to be edited. In LinuxMint, they are stored in `/etc/`. Before making changes, look to find a couple libraries that should have been installed - `libtdsodbc.so` and `libtdsS.so`. On my system, they were at the following paths:

* `libtdsodbc.so` - `/usr/local/lib/libtdsodbc.so`
* `libtdsS.so` - `/usr/lib/x86_64-linux-gnu/odbc/libtdsS.so`

Both of those libraries will be used in the first edit. Open `/etc/odbcinst.ini` (if it doesn't exist, create it).

```shell
sudo vim /etc/odbcinst.ini
```

And here are what the contents should look like to define the ODBC drivers that the system will support.:

```ini
[FreeTDS]
Description = FreeTDS Driver
Driver = /usr/local/lib/libtdsodbc.so
Setup = /usr/lib/x86_64-linux-gnu/odbc/libtdsS.so
FileUsage = 1
CPTimeout = 5
CPReuse = 5
```

Next we need to configure FreeTDS to define our data sources

```shell
sudo vim /etc/freetds/freetds.conf
```

There are some example configurations in here that you can modify to suit your purposes. Here is an example configuration:

```ini
[MPS]
	host = 11.22.33.44
	instance = MPSS003DB
	tds version = 7.0
```

If you have questions on how this should be put together, you can run a man command to get help

```shell
man freetds.conf
```

Once you have freetds configured, you can try out your settings to make sure they work

```shell
tsql -S MPS -D MPSP -U userid
```

This should give you output similar to:

```none
Password: <enter your password>
locale is "en_US.UTF-8"
locale charset is "UTF-8"
using default charset "UTF-8"
Setting MPSP as default database in login packet
1> 
```

If you don't get that, you can check your error codes on the [freetds site](http://www.freetds.org/userguide/confirminstall.htm)

The last file to edit is the general system datasources definition:

```shell
sudo vim /etc/odbc.ini
```

Contents using the previous example configuration would look something like this:

```ini
[MPS] # this doesn't have to match what's in freetds.conf
Servername = MPS
Driver = FreeTDS
Database = MPSP
```

## Try it out
Now you can use the unixODBC tools to test out your connection

```shell
isql MPS userid password
```

This should get you in to your MSSQL server where you can now execute SQL.

## Install the gem

```shell
gem install tiny_tds
```

Once it is installed, we can test it out by launching `irb` and then entering the following:

{% highlight ruby linenos %}
require 'tiny_tds'
client = TinyTds::Client.new(:dataserver => 'MPS', :username=>'userid', :password=>'password')
client.closed? # should be false, if it's true you probably had an error connecting
client.execute("SELECT getdate()").each {|row| puts row}
{% endhighlight %}

If you got back a date, then you were successful!! If you got a large stack trace after typing `require 'tiny_tds'` you may have to do one more step.

Error message: `require': libsybdb.so.5: cannot open shared object file: No such file or directory`

You can fix this by running the following command:

```shell
sudo ldconfig /usr/local/lib
```

This assumes that `libsybdb.so.5` is located in `/usr/local/lib`.

With that update, you should be able to run the ruby to get it to connect now.