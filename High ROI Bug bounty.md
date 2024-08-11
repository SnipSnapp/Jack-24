
target: https://dev.epicgames.com
Get an org, network etc.  We are going to focus on the account privilege escalations. You need to use two separate email addresses.  
Can add things and grab an alias. 
- So we're going to grab a second account through a second email address.  
- goal he has is to leave this talk, knowing how to find these sorts of vulns. 

Hope is to show where you can find those vulnerabilities. 
Step 1
- Make two epicgames accounts

This is not the most technical
This is not the most complex
but often gets medium/high bounties.  And the medium's and low's can pay the bills. 
IF you want the technical stuff.  

#Caido-is-the-tool-we-will-use-for-this
Max(AVV)/30 min
Avv = `impact*viability/friction.  
- impact = impact of success
- viability = chance of success
- friction = attempt or effort to attempt
Friction is interesting because it affects dupability. So the more steps someone has to do to replicate something, the more friction it makes. so balance what all the steps are. 

Threat modeling like a BBH
- Folks who are new, will approach a target, take all their lab stuff, and blindly stab at an app. There's no direction related to it, everyone does it. Rite of passage.
- Solution to this is threat modeling. 
- First understand what the app is
- Then understand how to attack it
- Understand what you should and should not be able to do.
- Search into things we're not supposed to be able to do in an app, and then try to do them. 
	- This can seem really hard
	- FNGs don't try to threat model. 
	- You have more success, the more you sit down and try to play with the application(s). And the more docs and everything that you read.
- Goal: First **RTFM**
	- become world expert on that application.  Knowing the app should be central to everything.
	- You should be able to tell someone about their own application in depth. 
	- Need to see and check on everything related to that application and how it works. 
	- **RTFM** and use it.
		- google that stuff.
- You can just google things, and then at the top it gives you some time to read.  So for this example, read the epicgames docs.  Sit down and read it. 

Key things found when reading it
- Can use the dev portal to set up and configure game apps, and games store and online services things.  So there is varying levels of access across the tools.
- Within the sign-in section, you can sign-in in various ways.  These ways to sign-in give us  ideas. can we abuse the account sign in methods to result in a confusion or takeover.  They assume 3rd party accounts are your epic games accounts. 
- In an epic games store, or epic online services, things are organized in a hierarchy. 
- Within the organization section, product, and next section. What we find that's interesting includes:
	- member access
	- multiple products
	- multiple product versions
- So can we force a user to be a member of an organization. Which may be their email, or their name.  Are there separations of trust and trust boundaries? That's a question to ask
- There's lots of objects here which is good for access controls and IDORs
- A sandbox contains games store relate info mod info etc.
	- can we pivot sandbox access for one deployment to another to leak IDs and gadgets?
- A public sandbox exists, can be used for deployments of live products dev/stage
	- Outside of your org, vs. inside of org but assigned issue? Can we mix em? Can we force using one we don't have perms via the webapp?

Sandbox types
- When setup a product, by default there is one called live.
	- Can get dev/staged which default to private 
		- (not supposed to be accessible, so this is a target.)
	- Can get two additional default sandboxes by applying for them
		- ok can we do it w/o appplying? that's the question.
	- Getting sandboxes for different use for diff IDPs.  
		- Again, can we mix em' and in what ways, each can be individual bounty.

Deployment
- Unique = no cross deployment access.
	- It says they're unique, so is there cross-deployment access? Can we force it?
Client
- Clients are any software program that accesses EOS services for your product - epic online services
- Trusted servers as clients
	- Services are known as trusted servers, trusted servers require access to your products user data, so you have to add them as a client. 
		- so how do we become a trusted server, and how do we become a client???
	- You must associate a client with client policies
		- do we tho??
	- You cannot create a client without a client policy in place
		- but really tho?
Core Bounty Trigger words:
	cannot
	must
	Won't
	Basically anything that says you can't do something.


Application
- App settings you must configure epic account service settings as an app of your product.
	- Can we use those services without configuring as an app of our product??
- There's often white-labeling in orgs, where a user gets beta or something like that. `something to remember`
- User permissions, can we use ones we didn't request, **or adjecent ones**
	- If we give a user access to webhooks, do they get more than just webhooks, can they configure and test them?

So Painting broad strokes
	- limited to our org not limited to product admins is something to look into
	- sandbox access == all sandbox access?
		- are api endpoints scoped like this? 
	- Private sandbox == private sandbox access? any dev/qa differentiation?
- Attack perspectives
	- Admin -> Admin/Owner attack (PR:H)
	- Trusted client server -> Org (PR: H)
	- Inside-org priv esc (PR:L)
	- Q/A Sandbox tester priv esc (are they "in org" if QA testing) (PR:L/N)
	- Outside of org -> org attack (PRN:N)
	- client-> Org(PR:N)
	- Client -> End User (PR:N)
		- what can a client do to an end users?
	- Org -> Epic attack (bypassing application processes, ect) (PR:N)
- Tons of business functionality to test
	- Brand settings -> Injection attacks
	- RACE
		- It says you can add one or more clients to product, clients determine consent dialog users see when they launch your product.
		- So can you change the permissions when someone clicks yes and accept? So can you change the API request in the backend to change the perms?
	- Policy bypasses etc. 
 If you take the reading tmie they say into account, there are at least 23 attack vectors.

Reading docs is the best way of figuring out where to start, and what to do. 

Next step, stay locked in think about the target everywhere, even while not looking into anything about the target. 
- Do this when you figure out how to figure out how to do race or whatever attacks. 

Back to the thing, And epicgames example.
This from the low level member's perspective, you see a limited number of options on the side panel,  but you see a change log for when the developer portal was last updated. So you can see when new things were added, when new perms could've been added, so if there's a changelog maybe you can see what was changed and validate it. 
Now on admin perspective, we have a lot of differences,  we should use differences between user and admin to find what the differences are so we know what an admin can do, but what a non-admin may not be able to do. It gives us a lot of knowledge and things to tell us where to pivot to next. 

Next step is to become an M&R power user. 
So
let's say you have a browser;  what does the Browser do, what are we given by the server.  IE) some massive giant JS file we don't care about so much, it's there because people suck at things.  
But at some point the browser does something cool, it hits the API whether it is readily surfaced or not.  So it has to know what role you are, so it knows you're not an admin.
The browser is controlled on our computer, so we can use the API that tells the app what we are, to abuse it.
We want to do this first then read the stupid JS file. Because they're always long, and take forever. And easy wins can be gotten often times by not reading the JS file.

Now, whole point of match and replace is to simulate a state in the browser.
Normally with m&r response body, you can simulate a state in the client side.  like being a super admin, or like being whatever. 
We want to change the userRole from "user" to "admin"
Caido can help with match and replace things, to simulate state on the client side. so use it. 

You can provide a condition inside of the caido tool. 
The top level request, is highlighted, it's the sec fetch mode headers for when you change the URL on the browser,  navigate vs. document. vs. iframe, etc.  All API reqs originate from the top level request. 
github.com/bebiksior/even-better

caido-workflow automation is really easy. So you can do rapid prototyping

How do we find these?
- Often in JSON blobs in `<script>` tags in html response
- look at fetch request
- look at fetch requests for things related to roles, permissions, etc.
- look at fetch requests related to user information (eg /users/me)
- look for jwt's and their signed content. 
- Figure out how the thing is making role/auth decisions on the client-side, and manipulate that with M&R so that low level user client side looks high-level on the client side. We are seeking to get the feel of the high level user as a low level one. 
- Want to know what role the client has, the response it has, and what it can do. 
	- From here we will be able to get more perms, attack more, etc. 
- So we want to look at things like requests related to users, roles, etc. And if using caido will want to expand the path in caido, the one in this case we want is probably very long. 
huntr.com, hackinghub.io.  Sometimes it can be tricky. 
a complex exampleis good.

So on the developer portal, because we can, run diff on admin vs. user. 
So looking under /portal/api/v2/services/users/organizations/o-blah/roles
There we can find something. Walking throught he solution:
- This is the req we're interested in.  
- In here, we have 'user' inside of it, which is why it's interesting.  so from the admin side, just paste the same request on the member side, so use the normal user's request, and replace it with the admin. 
- See, in this instance, in the respponse we find the difference.  Do a search for data, and replace it with the admin user's API response. 
- Now when the server comes back, and says what are the permissions from the super-low-level user? Oh it's all the admin user stuf.
- If you put something at the end, for somethin that doesn't exist, the server will ignore it if it's not a part of the original json. 

IF successful, you start seeing a lot more menu's. 
So what it should look like is a normal admin interface, instead of the avg joe user interface. 
Once you do this, you can jump into admin interaces, see all the normal sidebars, and can interact with things similarly. Next you click through and see if you have privilege escalation, do you have privesc within that flow?

Now to identify the feature flags.  What you're doing is essentially telling the browser to show you the admin interface, but you'll see 403 reqs everywhere, if you see anything populate then you got a bug,  because most things shouldn't be loading. 
You can match and replace the javascript to always return true for if you have a permission.  The JSON blob is loaded by the API and returns things correctly though in this instance. 

Hacking takes time, people expect to login to an app, but it depends on amount of time spent.  That's what gets you actual value when hacking.  The time put into the single thing.
- Feature flags are very helpful. In an application, it's a way for a backend thing to open up a feature that gets slow rolled out.  They're backend switches for sets of users, to tell if something is helpful, other times they control access to specifics like various members to roles, or beta testers, whatever. 
- alot of time, they're a features.json that gets loaded up on the client side. 

identifying feature flags
- you comb through the app looking for feature flags.  Look at json blobs in `<script>` tags,  in the JWT tokens, in the static JSON files.  And usually you can find it.  And then you can modify them to get more features, go fuckin figure. 
- And you can search for vulns in those pre-released features. 


Now these feature flags here are easy to find in the viewsource in the script tag, and this will give you the enable reports, and other various things to enable. 
When you flip on this you can turn on maintenance mode.  Luckily caido lets us stack match and replace rules just like firewalls. 
Next, because we had maintenance. mode. next we can see something that looks like a bunch of announcements. now there are feature flags for announcements that weren't previously enabled.  AND now there's an option to access financials in the lefthand tab.  This means, we have new scope.  There could be client or server-side vulns within here.  
Feature flags are amazing for scope to expand. 
Maintenance page is useful. 
`enable([^"]*)":false` with `enable$1:true`
somethin like dat

fff -feature flag finder
https://ctbb.show/fff
this is an automation to show all the features. it looks for feature flags, things that these could be called, and alerts you for when you see on this page. 

Next paywall bypasses
https://sketchfab.com/plans
these are really easy to execute, and can be free money in most scenarios. 
Basic/pro/premium/enterprise tier. 
Lots of times you can match and replace those tiers, and if you access any of those features, you can make it say the guy says you can't access the features, and you can.  But you need to point it out, make it clear with their own matrix. 

Bug bounty metagaming - milking it

Vuln extrapolation 
- when you find a vuln, ask
	- where did it occur? is there something funky about this part of the app
	- x section of app
	- x url path or microservice has perm issues
	- x structure has issues, 
	- x resource and adjacent resources
- Figure out why it worked,  What other things look similar that you can then pull on
	- Sometimes there are access controls you can turn on, sometimes you can abuse other aspects. 
	- think, what other pages have access controls, that have things populated in the html response vs, being pulled in. 
	- Are there signed docs or adjacent resources?
- Why did the finding occur?
	- regex issue?
	- sanitization issue
	- permission issue?
	- find these issue types, and then you can be off tot he races.
- when/who did this finding occur with
		- who wrote it, and when they wrote it.  You select a specific hacker1 report, hackerone.com/reports/2381253

Gadgets useful
- OpenRedirect
- XSS on a subdomain
	- set cookies for subdomain
- leaking window.location.href
	- oath leak, id leak
- client-side path traversal
	- + OR = business logic error
	- + json control
- id oracle
- self xss
- login csrf
- redirect url path traversal
- window hijacking.
ctbb.show/gadget/defcon