Getting more than subdomains

In-depth workshop on amass project.  Good Os tool, more than network mapping. 
- Get to know the advancements in data collection capabilities, leading to an osint system. 

Adam Rosick is a major contributor to amass. 
How to leverage the work doing. 
github.com/The-Inceptions
linkedin.com/ademrosic

# Overview
Brief History and Evolution, 
- Started 6 years ago as a turnkey to understand attack surface
- Broader solution now, incorporates pieces that expand the project capabilities.
- Users can execute multiple enums in the background while the engine does the busy work
- New architecture allows extending capabilities in an efficient and trivial manner, thanks to OAM.
- Idea is to evolve to a broader solution. Expanding on capabilities. 
- Amass is the one solution to create capablities for, and for running multi-enum. And engine does the rest. 
Architecture
- Allows trivial extensions thanks ot OAM

# Core funcs

Used for organizational surveillance.  With amass providing visualization, can monitor to spot anomalies. Can use to actively monitor your own attack surface, and spot out waht anomalies exist and don't.  It's used for security assessments that could otherwise be missed.  This is all done systematically which can otherwise be missed. 
With blueteaming and red teaming it has equal value just different motives. 
Real benefit of OAM is that you can have other separate tools from AMASS, that are separate from the data model. Not everything has to be baked into it.  You can use it to create your own reporting, and instead of dealing with a mess of findings, using the OAM/data model it's all just in there with the associations, what's related to what, and it makes it easier to answer the complex questions with analysis tools. So you can build it how you want it done. 

Amass has opened up, it uses postgres, it's easy to work with now.
Amass is what is now the standard in doing recon. 

understanding amass darta collection capabilities, look in the code for how it works, and a demo of new OAM asset types being collected by. amass, and then a Q&A

# Understanding the data collection

- It has a new event driven architecture to handle events that have handlers that are supportef.
- handlers are responsible for the data that comes in, so you can put things back into the pipeline.
- The architecture we have is careless or doesn't matter which events come into it. A processing mechanism that gives you what you need, and can jump back in a cyclic process. 
- From day 1, amass project has a cyclic architecture.  You go out get config, pul, sort, print.  Amass is recursive instead. 
- All of the things happen at the same time. Get found, jump back into the pipelines.  Only based on or only constraint is the OAM data model. And if there's a plugin that says you want to receive events for taht OAM asset type, then it's going to receive it, it'll go get more findings on it. 

# pipeline example

From FQDN -> to IP -> From IP -> to netblock
So in this case, we have an FQDN, some seed data.  If we have a handler that could produce IPs, it'd look for handlers that can do taht, and whether or not it produces anything is fine. When it does produce something it'll turn it into another event, with another pipeline to go off of.  So if the FQDN produces 20 IPs, all 20 IPs would get put back into the pipeline. 
So all of this repeats until it comes to a halt, it stops when it can't do anything else at the time with the events available. 

What we have are the supported OAM types
- FQDNS
- IPs
- Netblocks
- CIDR
- Email addresses
- RRorgs

Couple of tool sused to grab the email addresses, and it's pretty simple. 
Modular architecture and events works perfectly fine. 
Beauty of it, is in its ease of creation. 
Integrations are always enhanced and easy.
They used to use LUA. 
You can make your own API and put it in the plugins directory, once it is built, you can just have it works seamlessly.

If you're an org, and have some secret capability, you can keep it, and have your cool guy plugin. 
Amass's updates won't change your own plugins that much. 


it's easy to write, you can just build plugin, recompile. 

make a struct, make sure it aligns with the interface they use. 
All you have to do is put the name,
put the plugin itself
the transforms (produce)
and what you want to come in.
The callback is a callback function they have.

grepapp.go searches github repos for data, handles data, and have a regex pattern matching emails will get you emails. 
- Once it gets the emails, it'll send em back into the event pipeline. 
- All now in golang.

# registry

Helps event pipeline determine what we need to fulfill the user's needs.
The plugins which have handlers cement themselves within the registry.  It says if anything comes in, if we can produce what the user wants we'll make it happen. 
If you setup your config so the only thing you want it to do is something simple, you set up your config for that, and your session is never going to execute except for relevant things to the subdomain enum. 
If the handler says "X data comes in". I will try to trun it into "Y" if you're not interested in Y, engine doesn't call the callback for that. So when engine is started up, if some of the things are missing, it just says we won't make make the pipeline for the things you don't have. It does this dynamically. 

The venn diagram where things overlap is what will happen when things execute. 
Registry is the seamless thing that makes all things happen.  It isn't touched really. 

As more datatypes get added to the OAM, the engine registry doesn't need to be updated. So it's built to be extensible. 

Configuration with Amass
- YAML vs. INI files.  
- Folks were confused with best approach or best options to set.  There were alot in the previous tool which made things confusing for users to ID what was best for their situation
- With the YAML config, it is simple, and readable. It isn't too specific. As far as getting what you want, you just have to say I expect "X" and when "X" comes in I want "Y"
- Possible to exclude handlers, it is a declarative approach. 

# OAM and assetDB the inner engine

OAM - New standard for asset definitions.  Describes framework and categorizing for what you have.  Whatever we want it to be, we have categorization for that.  Information for each type we usually see on the same basis is value.  Can hold everyhting in SQLite, posgresql, etc.  just check github. 
The asset DB, along with storing assets, it stores the relations that exist within it as well.  So each part is a mapping to another part.  So the relations table as the A record says, from this FQDN, it points to the A record which goes to this other IP, but you can get mroe relations. You can even build graph nodes through this if you want to based on this, and how you query for that result. 

Benefits of OAM and assetDB.
- can ask any questions you may not find in a standard tool
- It saves the info. 
- Everything stored in one spot, and can make things as complex or simple, and make it happen. 

Got used to using amass where if you want the data, you gotta run the enum again. Sitting around for these things
Only time you need to run the enum again is when you think something should be refreshed. 

A lot of work to do so you don't have to do it.
Can change workflows to make use of things. 

An example is, you can make your own web interface, using graphana is possible. 

A list or a table of the things, a table of all the things found relating to the domains.
Finding IP2Location for local DB stuff.   whatever

Can manage workflows, set sessions, what is the seed data, what's in scope. What's refreshed, etc.

- You set it, run it, and forget it. 
- Some like geo-mapping, because it spots things in places, but yeah, no worthless.

# Demonstration of new OAM asset types

Free to follow.  use docker compose to get full capabilities. (hate docker)
note: with commandline interface, you're not stuck using it, you can use any api request to the collection engine, and it'll do the work. 
Can use docker desktop

then you need docker compose
github.com/owasp-amass/amass-docker-compose.git

change directories into the local compose repo
```sh
mv amass-docker-compose compose
cd compose
```
change the passwords in the `assetdb.env` file.

Execute an enum against owasp.org:
```sh
docker compose run -rm amass enum -d owasp.org
```
heading to the terminal from here. 

One way is to install docker desktop.  To run all this, other way is to go to docs.docker.com/compose then your OS.
There you should have a one-liner to remove your conflicting packages. 
You can use the apt repository to set all the things up too. 
If your'e using docker desktop then you can just use the web interface just fine. 

Then install latest docker packages (It's in the docs.docker.com.  std stuff )

if you want to run w/o "sudo" but still as sudo, you can add and make a docker group, add yourself to it, whatever. 

amass docker compose, follow step 2 of  what it says on their girhub from here
```sh
git clone https://github.com/owasp-amass/amass-docker-compose.git
```
cd into ti.
then you just run somethin ez pz
Quick note, make sure you check the `data` and `logs` directory, if they don't have permission for rw for everyone, there will be problems, all of the rights coming through a single container, it doesn't have that because most things don't have perms to write to the directories they need.  Give both directories chmod 777

```sh
docker compose up
```

To get it working, one of the things you'll want ot check out is the `config/assetdb.env`

This has the postgres password, db, user, so once it changes it, it'll mess things up, do it now on setup if you want to or leave it, because meh. you've already logged in.  may as well have access to your *stuff*. 

They have an option there called database, so change this accordingly to match up with what's in the assetdb file under the config.yaml file inside of `config` or be big sad.
In here it says you have your transforms, you can add the things you want, or remove what you want in there, and add exclusions, this is under `transformations`
There is also an `alterations` tab which uses wordlists to use for some name alterations. 

moving forward with data sources...
inside of `config/datasources.yaml` define the data sources that are in use within the engine. the key part is, if you want different datasources used, you just uncomment whatever. 

OK next part. Finally. speed of slow

now run
```sh
docker compose run --rm amass enum -d #put the domain you want here
```
example:
```sh
docker compose run --rm amass enum -d owasp.org
```

Then it'll pull all the container stuff, all the typical docker *stuff* it needs to run, and build everything.  This is another reason why docker is awful. but hey, whatever makes people happy.

Everything should come up healthy at this point.  If anything comes up as not healthy check the `data` and `logs` directory for `777`
If you did that, it should look like this for now.

There are some interesting sponsors for the project. Folks are happy with it. 
There are a large number fo targets being managed and tracked by amass. Can be fired at large scope and it should keep up. New version has postgres, and it's fine. 
when a handler finds a lot fo data, it'll bounce the progress bar back. This is because one handler will find 40 different events to the pipeline. 
Now if you just want emails you can do something like
```sh
docker compose run --rm amass emails -d owasp.org
```
If you click enter, it'll poop out all the emails.
There's a verification handler, so if you attempt to verify things, and it doesn't come out confident enough, the data will be pulled and thrown out. 
So if there is a point where you can't verify it, and there's no true way to know, just dump things in there. 
If you do have credits for an API where it does verification, results may vary. 

other commands:
```sh
docker compose run --rm amass subdomains <etc.>
docker compose run --rm amass domains #<I really wasn't looking at this moment>
docker exec -it assetdb /bin/bash #Jump into the container running the assetdb
```

How do we avoid FPs
- everything the engine finds, if your plugin or handler finds something, if you have support, it'll get validated, so we do the dns lookups to see things are still there, we reach out to the IPs, when we find the email addresses, if you have the support turned on, the verifiers will test if these things are active. We do our best to validate findings.
- If ran a month ago, and no longer valid, if you refresh the data, it'll show it was found a month ago, but wasn't found on this run. So even if you don't have the datasources turned on any more, it'll look again and see if active, so it's easy to say a month ago, if it was discovered, now it's not discovered. So you can say "show me only the things that have been found, but are newer and popping up". All history is maintained.  another query once inside the bin-bash

```sh
psql -U amass -d assetdb
```
```psql
select * from assets;
#command to show tables can be used to see things as they're added......
```
This can let you get everything. Simple or complicated to get what you're looking for. Everything will show up in here. 
```psql
select * from relations;
```
gets the relationships built ^^^
They have all the popular extractions of data and whatever in the db..... dude got mad because someone asked a really really dumb question. 

If you wanted to see the web UI and have the compose running
you just go to a web browser and then the port check it with
```sh
docker compose ps -a
```
check wheere grafana is,  default is port 3000

ip2locationlite is the free or community version, and get the CSVs for IPVv4 and IPv6 and the option with the largest number of fields (db11) if you pull those down, pull the csvs into the compose directory.  Then run the sciprt ip2locationdata and it will create the db, upload data for you, and the UI will pull from that DB and populate the map.  It's as easy as you can get it. 
(make an API query to run the script and pull the data. use burp if confused or dne to find the get)

Attack surface mapping is more than just infrastructure.  For this case, think about how social engineering can be done. When you're trying to social engineer someone, you get the victim to feel for you.
Traditional IP mappings won't do it for oyu, looking at people, what they do, areas, etc to dig into weakpoints. 

Upcoming OAM assets include more IT assets, and non-IT assets:
- whois
- tls certs
- domain name registrars
- exposed apps
- ports & tech stacks
- accounts & creds
- api endpoints
- hosted brand mats
- individuals
- orgs
- phone numbers
- street addresses
- affiliates
- third party vendors
- and more. 

Had to leave early. lots of waiting on docker... because docker bad.

