

Atomic Honeypot, is a honeypot that strikes back. 

1. Part 1, MySQL Servers are always being attacked. 
	1. Done quickly, DB Servers are a big target.
	2. So we create a high-interaction MySQL Honeypot,  atool that mimics the DB and the MySQL Protocol and bots try to get to it.
2. The MySQL Protocol is actionally Server Initiated.  If you connect to MySQL Server over Telnet you see the version, and other information.  Version + Salt + Plugin.  So listening in is not enough.  So to create the mysql_mimic, made a python module. 
github.com/kelsin/mysql-mimic/tree/main/mysql_mimic

So we start the honeypot with this, and you start to see connections.  You start on a mysql server on an open port on the internet.  You can see the HTTP protocol, mysql protocol also have connection attributes.  MySQL Provides a lot of attributes.  So you start the Honeypot and see the fingerprints of the Connection, you see the version of the client library, the operating system, lots of other stuff.  From there, the part 2 is what kind of attacks you have.  You see two major attacks on MySQL,  the attempt to own and create a backdoor.  It's a wellknown attack and only works on servers circa 2013-2014.  

It uses an old MySQL Client version.  The other major attack is a ransomware attack, it triest to brute-force pwd, download data, drops the databases, and ask for ransom. Same thing as fingerprints,  you see the version, and waht the client is using

So start an atomic version of the honeypot.  It is a honeypot that strikes back. You see what they're doing and download the malicious code they're using. 

So what do we have.
We've been juggling different CVEs in mySQL client, arbitrary fileread, rce's,  mysql server attacking you RCE. This can be used to execute code on the client.  And finally this year we have CVE-2024-21096.  This is another RCE in mySQL client. 
So, mySQL server attacks mySQL client.


1. We have mySQL client arbitrary file read. 
2. You create a server
3. client connects
4. server requests a file. 

1. The second one is, Prereq is you need a shared library uploaded to the client first, then when the connection is established, you can trick the client to execute the shared library, you don't need to execute the shared lib itself, just need the file.

1. Third ons is a POC CVE-2024-21096.
2. MySQLDump is a tool that allows you to make logical scripted dumps of DBs, allows scripts, tables, etc to be transferred to another server.
3. So using mysqldump to mysql you can do a dump.  It's pretty close, however a rogue mySQL server can issue a system shell command.  (system(\!) can execute a system shell.)
4. So how this works.  By default, mysqldump tries to protect everything it receives from the server, it tries to talk to version, it tries to not protest, it is not sanitized, it sends back to output as is.  We leverage my creating our own server.
5. A patched version of a server you just make a new line ` \! touch /tmp /pwn` putting right after the server versioun would cause an execution

So you make a honeypot that allows you to exploit this vuln.  By passing specific configuration options to this. 
We try to run code on the clients that connect to our honeypot with this vuln.

So we then start an HTTP server. 

mySQL dump on the file gets us a popup with the pwnd messag.

- So now we succesfully pwnd the client. 

Trying to figure out how we attack the bad guys.

Two attacks working on. 
First attack is  we have the fingerprint w/ os, client, platform, version etc.
So we can already see what we can or cannot do.  
We don't know if we can do RCE, no mySQLDump, But we do have arbitrary file read.

So using the arbitrary fileread, you can use RogueSQL.py. (by jib1337)
The clinent connects to fake server, and you can read a file.
So the client, It goes through and says download tool, sta


So if you connect to an old connectionso using this thing you can do arbitrary file reads.  So you gotta see what version of windows the bots are using.  A number of ways is the XML version from the file download.  Through this you see it is an old version of windows, IE) windows server 2003.  You can understand and get more details about what the attacker is using from this.

So on Windows you need to deliver a DLL to the client to be able to trigger it.  So starting to think, how do you deliver the code??
A dLL doesn't need to be triggered on the client.  So they just collected the actual commands the client were running or trying to run on the server.  Through this, they were trying to download malicious clode on the server.  So they're running an FTP server on their own machine, and we know the pass, user, and port.  So The plan was:

1/ Upload DLL to a pub location of FTP server. 
Next we run atomic honeypot to trigger the DLL.
Then we get a sheeeellll

So we created this RCE via a simulation.
So first we connect to the FTP server,  then we just put the evil DLL onto the server, because you have the FTP creds.

After you put the evil DLL in a new location you start the server/honeypot.  So we get the startup script, get the plugin directory, and trigger the execution of the plugin DLL.
But basically, it's a tmySQL plugins directory traversal + injection.
So you use the directory traversal to trigger a DLL open.

So now we have, our stuff right, we call "dir (downloads)". we have mysql uploaded to the client.  
The client is connecting to us.  So we try to connect, and it executes the RCE immediately.

So this is done, we have Arbitrary file read, RCE via plugins,  


The second attack is more interesting,  we see later versions of mysql client.  We know the RCE via plugin will not work, but what we see immediately is MySQL dump.  We actually found a way to do the code execution.

So So we go ahead and see how the attack is being executed.  The app that connects to our server, it connects to show databases, it executes mysql dump utility, and passes database and tablename.

So the goal is to download the DB fso they can claim they downloaded your data.  But they're only downloading 10 records.  So don't.  They claim they got it all, but they only have 10 records.

We can't download via arbitrary file read,  and we can't execute code via plugin.

so how does this attack happen?
Using the MariaDB connector you have the application.  The MySQL Dumps on the server, from the CLI on linux, Then our application connects back. 
We are providing the server.  Whatever client will execute we will see immediately.  They send this all back up to us.
So if this is what they're doing maybe we can do a command injection.
So they made a fake database,  just a classic command injection in schema_name.  And it actually worked.  They used a command injection on the client attack, and they get to see all of the information back to them.  So they downloaded the evil code on these attackers.  It's a mysql dump, you have a command injection.  And then you can get their code.....

So they successfully got their goal, but not the way they thought. They executed the command injection.  So at home.
1. Start a normal burner mysql db server.
2. Create a root with no password
3. create a db or table with a command injection
4. create database '; command;';
open port 3306 from internet
wait for injections.