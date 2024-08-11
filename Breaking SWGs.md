
Most work is now done in the browser from storage to docs to transactions.
If you look at secure web browsing you have secure web gateways which are a part of SASE solutions. And there are endpoint security things, which have no all visibility into any browsers.  when it comes to SWGs.  It can do SSL intercept, malware detect, DLP, firewall, sandbox, URL and file type filters, etc. 
SASE, which is the overall business aspect, is a massive market .  But every single SASE vendor is vulnerable to these architecture attacks.

Mandatory reqs for SASE 
You need to be able to stop malware from downloading, have threat prevention, and logging.
It is an architectural problem. 

Vendors guarantee they can prevent 100% of all website malware from leaking through the proxies, and need to give credits for all of these leaks. 
SWGs in attack detection. 
SSL intercept and everything has been around for 15-20 years ago. 
folks needed a way to moinior and block things, everything was simpler though too.
SWGs look at net traffic and infer app layer attacks.  So they have no webapp context, no context of window or tab, and without this info they have to figure out the actual attack vendor side. 
There are also cloud processing limitations, but this all depends on each vendor. Most vendors can do at most 50mb, but many not even 50mb

They also suck ass at doing files/archives/other. 
Why have vendors dgone through all of these limitations?
- All of these products are priced similarly.  SASE vendors go ahead and sell between 2-4$/endpoint/user/month.
- To look at all of these files they have to put limitors on somewhere or it's cost prohibitive. 

How file scanning works in SWGs. 
- An SWG receives all of the traffic from the user's browser, sees content disposition trigger. 
- Caches the file in the cloud, starts scanning, then blocks.  HTML/CSS/JS. new spear phish, heuristics/ML vendor hends up detecting it, so if the attacker takes the website, and puts in a new URL they should supposedly be able to still block it. 

Difficulty and issue witht he architecture?
- SWGs only look at network traffic.  Attacker has a compute machine available once things leave the proxy.  
- Browsers, within them, there's a lot when a site loads, DOM changes, user interactivity, browser events etc.  It is rare it all operates blindly.  It's impossible to sync it all back to the cloud. 
- Data and the basics of SWG. How do we bypass malware detection on SWGs?

SWGs.
- Pure client-side attacks like clickjacking, hard because client-side.
- Unmonitored channels
- last-mile-reassembly attacks. 

-There are so many new protocols so it's hard to keep up and build monitoring and interception for them all.  They can also be difficult to intercept and analyze. 
So how does SWG stuff work?
First example, is file download without a secure gateway.
- Browser.security.  If anyone uses browsers/sase, you can go test it after this talk. 

Attacks detected by SWGs out of the box, are shown in the site. 
So looking at the same example with an SWG:

- So vendor file test one.  The moment the file download starts, they cache it, scan it, and feel it's benign they allow the download.  But the moment we click on it, you'll see the attack being blocked.  Now vendor class 2, it was unusual. because all vendors are competing with each other on trying to make things more seamless.  It will allow the file to begin, but if they think it's odd they kill the file in between, so you get a truncated file.
- Vendor class 2, we can get a 14kb file instead of a 1-00-some kb file, so you get file parts.

File Upload without a secure web gateway
- They all say they allow you can build web DLP rules, so you can use regex to find all the bad *stuff. 
- Just went through all this stuff for malicious file upload, it works w/o a proxy, but with the proxy it will get blocked by the DLP rule.

So evading SWG through unmonitored channels. 
- Comms channels webtransport, servers ide events, webtorrent, firebase cloud messaging etc. All completely unmonitored, they basically don't give a *stuff* about chat. so you can smuggle malicious files in all threat feeds through web rtc or server-side event channel. 
	- It used to require coding, but with code LLMs it's stupid easy.
	- A lot of their demo's some folks just build it with chatgpt for the lulz
- If you're on an SWG, these unmonitored channels are hard to monitor.  They try to get user's to block these on the browser, but if these are blocked folks' get upsetti spagetti
- browser.security has all of these classes of attack. 
- It is as simple as you click it downloads. The user experience doesn't change, but the seemless parts do.  Instead of the user seeing typical HTTP based comms, they see webrpc, grpc and a bunch of other options. So when you set it up it doesn't even feel interesting. 

last-data-reassembly-attack
	web-assembly in recent years has gottem very popular, allows folks to run native libs within the browser itself.  So you set up a JS bridge.  With this used a lot is on sites w/ image processesing and everything client-side. So if you wanna use web-assembly. 

Go ahead and package the file inside of a file which is a payload inside of this, which is in a binary format,  so they can't analyze WASM files. HTML/CSS loads, and you get a payload dumper from wasm which dumps the fle, triggers a download event, and the end-user experience is exactly the same. 

See you get malicious files, thatare the same, 

So steganography - you can hide malware in steganography in files, and making a custom scheme is super trivial. But doing it, and putting malware in png and bmp is also very easy. 
So the image just has all of the malware, so the attacker loads a webpage via social media,  on the page there are images which have the malware,  with js we construct the malware all client-side and drop it on the system. 

Can use the standard HTML components. 
- HTML, can embed same malicious file. in CSS.  And you can have them pick the payload from CSS. Attack is slightly embedded based, but you have to put it all in a JS/CSS/SVG file. 

Last mile file chunking also works,  so you break the file into different chunks
First you split the file into different chunks, trigger the download to fetch all of the chunks, on the client-side you make net reqs to fetch the chunks, then you just use JS to put themback together correctly. So now they won't know if it's malicious. 
Doing a reverse split, you can take a chunk, reverse content, and reanalyze it. So let's say you take different chunk sizes you can also bypass it all.  You can also mix and match all of this. And some things will be reversed, and others not.  Attackers will know the correct order, and what is vs. isn't reversed. And client-side you can put them all together and trigger a download. 
Finally you can encrypt the file, then decrypt it after or on download with JS
