1.04 - Choose correct profiles and settings to fit application requirements
===========================================================================

HTTP Optimization - RamCache Lab
--------------------------------

Go to your virtual server and refresh server times. Note the Source Node
for the pictures of the BIG-IPs. They change depending on where the
connection is coming from. The Source Node information is part of the
picture.

Go to **Local Traffic > Profiles > Services > Web Acceleration** or 
**Acceleration > Profiles > Web Acceleration**

Create a new profile named **www-opt-caching** using
**optimized-caching** as the Parent Profile.

Take all the defaults, no other changes are required.

*Q1. What resource would be consumed if you increased the* **Cache Size** *setting?*

Open up your **www\_vs** virtual server.

At the HTTP Profile drop down menu make sure http is selected.

Under **Acceleration** at **Web** **Acceleration** **Profile** select
your new caching profile; **www-opt-caching**

Clear the statistics on the **www\_pool**.

Browse to **http://10.1.10.100**. Refresh the main web page several times.

*Q2. The pictures do not change. Why do you think that is?*

*Q3. Go to your pool. Are all pool members taking connections?*

Now go to **Statistics > Module Statistics > Local Traffic** on the sidebar,
from the **Statistics Type** drop down menu select **Profiles Summary**
and select the **Web Acceleration** profile view link. Note the
information.

You can get more detailed information on Web Acceleration profils and cache entries at the
CLI level

Log onto the CLI of your BIG-IP via SSH using the root account (root/default.F5demo.com).

At the CLI go into **tmsh** at the (tmos)# prompt and enter::

   show ltm profile ramcache www-opt-caching

HTTP Optimization - HTTP Compression Lab
----------------------------------------

Browse to **http://10.1.10.100**. On the web page under, **HTTP Request and
Response Information** select the **Request and Response Headers** link.

*Q1. Does the browser accept compression?*

Go to Local **Traffic > Profiles > Service > HTTP** **Compression** or
**Acceleration > Profiles > HTTP Compression**

Create a new profile, named **www-compress** using the
**wan-optimized-compression** default profile.

*Q2. At what point would the BIG-IP quit compressing responses?*

Open up your **www\_vs** virtual server.

At the **Web Acceleration** drop down menu select **None**

.. NOTE::

  For purpose of this lab we don't want caching interfering with our
  response headers.

At the **HTTP Compression** drop down menu select the HTTP compression
profile you just created.

Browse the virtual server web site and on the web page under **Content Examples
on This Host** select the **HTTP Compress Example** and **Plaintext
Compress Example** link.

Now off to **Statistics** on the sidebar, under the **Local Traffic**
drop down menu select **Profiles Summary**

Select the **View** link next to the **HTTP Compression** profile type

On the web page under, **HTTP Request and Response Information** select
the **Request and Response Headers** link. Notice you no longer see the
**Accept-Encoding** header in the **Request Headers Received** at the
Server section.

You can also browse to **https://10.1.10.115** and note what the
request/response looks like unchanged.

Simple (Source Address) Persistence 
-----------------------------------

You have already seen cookie persistence at work, but if the client or
application (ie. ftp) does not support cookies you must use and
alternate method. The most common is Simple Persistence which is based
on the source IP address/network.

Verify your **www\_pool** is using **Round Robin** load balancing and the
priority groups are disabled.

Browse to **http://10.1.10.100** and refresh several times. You should see
all 3 servers respond.

Go to **Local Traffic > Profiles** and select the **Persistence** tab and
from the **Persistence** **Profiles** screen select the **Create**
button.

Create a new persistence profile named **my-src-persist** with a
**Persistence Type** of **Source Address Affinity** and set the
**Timeout** to **120** seconds and leave **Mask: None**

.. NOTE:: 

   The **Mask: None** defaults to **255.255.255.255** which means each new IP address will create a new
   persistence record.

Now let's attach the new persistence profile to the **www\_vs** virtual
server.

.. HINT:: 

   When you create a Virtual Server everything is on a single page,
   when you return to modified the Virtual Server the Properties and
   Resources are on different pages.

Set the **Default Persistence Profile** to **my-src-persist**.

Test your Source Address Affinity persistence profile.

At this point you may want to open a second browser window to the BIG-IP
GUI.

Go to **Statistics > Module Statistic > Local Traffic** and select
**Persistence Records** from the **Statistics Type** menu.

In this window, you can watch you persistence records. You may want to
set **Auto Refresh** to 20 seconds.

In another BIG-IP GUI window go to **www\_pool** and clear the member
statistics.

Browser to **http://10.1.10.100** and refresh several times.

*Q1. How many members are taking traffic?*

*Q2. Check you Persists Records window, are the any persistence records?*

*Q3. Refresh you web page prior to the Age column reaching 120. What
happens?*

While the persistence recorded is still active **Disable** the member
you are persisted too and refresh the browser page.

*Q4. Could you access the web site? Why?*

While the persistence recorded is still active, go the member specific
menu of the member you are persisted too and do a **Force Offline** and
refresh the browser page.

*Q5. Could you access the web site? Why?*

.. IMPORTANT::

   Re-enable the pool members before continuing.


Securing web applications with the HTTP profile
-----------------------------------------------

Here you are going to perform some custom profile alterations to help
secure the web site. You are going to make sure hackers cannot see error
codes returned, scrub the response headers of extraneous and potentially
dangerous information and encrypt the persistence cookie to prevent
tampering.

Obtain the cookie name and information by browsing to
**https://10.1.10.115/** and open the **Display Cookie**. The cookie name is
everything in front of the **=** sign. How BIG-IP creates cookies for
Cookie Insert persistence can be found at https://support.f5.com/csp/article/K6917. After reading this article you could craft a cookie to hit a particular server.

*Q1. What is the cookie name? Note the information after the cookie.*

Let's begin by creating a custom HTTP profile.

+----------------------------------------+------------------------------------------+---------------------------------------------+
| Name:                                  | **secure-my-website**                    |                                             |
+========================================+==========================================+=============================================+
| Set the Fallback Host                  | **http://www.f5.com**                    |                                             |
+----------------------------------------+------------------------------------------+---------------------------------------------+
| Fallback on Error Codes                | **404 500-505**                          | The fallback site if an error is received   |
+----------------------------------------+------------------------------------------+---------------------------------------------+
| Response Headers Allowed               | **Content-Type Set-Cookie Location**     |                                             |
+----------------------------------------+------------------------------------------+---------------------------------------------+
| Encrypt Cookies                        | **<cookie name you obtained earlier>**   |                                             |
+----------------------------------------+------------------------------------------+---------------------------------------------+
| Cookie Encryption Passphrase           | **xcookie**                              |                                             |
+----------------------------------------+------------------------------------------+---------------------------------------------+
| Confirm Cookie Encryption Passphrase   | **xcookie**                              |                                             |
+----------------------------------------+------------------------------------------+---------------------------------------------+
| Insert XForwarded For                  | **Enable**                               | Example of modify headers                   |
+----------------------------------------+------------------------------------------+---------------------------------------------+

*Q2. What is in the X-Forwarded-For header? Why might you want to enable
it?*

Attach your new HTTP Profile to your **secure\_vs** virtual server

Browse to **https://10.1.10.115**.

Do the web pages appear normal? On the web page under, **HTTP Request
and Response Information** select the **Request and Response Headers**
link.

Open a new browser to **http://10.1.10.100**. On the web page under, **HTTP
Request and Response Information** select the **Request and Response
Headers** link.

*Q3. Are they the same? What is different?*

Now browse to a bad page.

For example, **https://10.1.10.115/badpage**

*Q4. What is the result?*

Under, **HTTP Request and Response Information** select the **Display
Cookie** link.

*Q5. What is different from the cookie at the start of the task?*

.. NOTE::

   Even though the data is encrypted between your browser and the
   virtual server, the LTM can still modify the data (i.e. resource
   cloaking) because the data is unencrypted and decompressed within TMOS.

Using iRules
------------

By now you should be thoroughly sick of trying to remember to type https:// in
every time you want to access your secure web site. Not only is that
easily rectify on the BIG-IP, but it is much more secure than opening up
port 80 on your secure web servers, so that they can perform a redirect.

While it would be easy to write your own redirect iRule, you note F5 has
one prebuilt that you can use.

Example of simple redirect iRule::

   when HTTP_REQUEST {
      HTTP::redirect https://[HTTP::host][HTTP::uri]
   }

Go to **Local Traffic > iRules**

In the search box at the top of the list of iRules, type **redirect**
and hit **Search**.

Open the iRule and take a quick look. This is a F5 Verified and F5
supported iRule.

Create your HTTP-to-HTTPS redirect virtual server.

Go to Local **Traffic > Virtual Servers** and create a new virtual
server.

+------------------------------+-------------------------------------------------------------+
| Name                         | redirect\_to\_secure\_vs                                    |
+==============================+=============================================================+
| Destination                  | <same IP as secure\_vs>                                     |
+------------------------------+-------------------------------------------------------------+
| Service Port                 | 80 (HTTP)                                                   |
+------------------------------+-------------------------------------------------------------+
| Source Address Translation   | None <you don't need this, this traffic is going nowhere>   |
+------------------------------+-------------------------------------------------------------+
| iRule                        | \_sys\_https\_redirect                                      |
+------------------------------+-------------------------------------------------------------+

Hit **Finished**

WOW! That didn't go too far did it. You just got an error. If you are
going to redirect the HTTP request you need the HOST and URI information
and that requires looking into the HTTP protocol.

Test your new virtual by going to **http://10.1.10.115**.

You should be redirected to the HTTPS virtual server.

As you can see very small iRules can make a very big difference. On the
exam, you may be asked to identify the iRule that would best solve an
issue. So, you should be familiar with basic iRules syntax.


(Optional) Default Monitors
---------------------------

You will be setting up a default monitor to test any node created. You
can also choose to use custom monitors and monitor on a per node basis.

Go to **Local Traffic > Nodes**, note the status nodes.

As you can see the nodes in this table, even though they were never
specifically configured in the Node portion of the GUI. Each time a unique IP
address is placed in a pool a corresponding node entry is added and
assigned the default monitor, if configured.

Also note, the node status is currently a blue square (**Unchecked**).

*Q1. What would happen if a node failed?*

Select the **Default Monitors** tab.

Notice you have several options, for nodes you want a generic monitor,
so we will choose *icmp*.

Select **icmp** from **Available** and place it in **Active**.

Select **Node List** or **Statistics** from the top tab.

*Q2. What are your node statuses?*

Select **Statistics > Module Statistics > Local Traffic**

*Q3. What are the statuses of your nodes, pool and virtual server?*

(Optional) Content Monitors
---------------------------

The default monitor simply tells us the IP address is accessible, but we
really don't know the status of the particular application the node
supports. We are now going to create a monitor to specifically test the
application we are interested in. We are going to check our web site and
its basic authentication capabilities.

Browse to **http://10.1.10.100** virtual server and select the **Basic
Authentication** link under **Authentication Examples**. Log on with the
credentials **user.1/password**.

.. HINT::

   You may have to scroll down the page to find the link.

You could use text from this page or text within the source code to test
for availability. You could also use HTTP statuses or header
information. You will be looking for the HTTP status **200 OK** as
the receive string to determine availability.

Note the URI is **/basic/**. You will need this for your monitor.

Select **Local Traffic > Monitor** on the side-bar and create and new
HTTP monitor called **www_test**.

.. list-table::
   :widths: 40 100

   *  - Name 
      - **www_test**
   *  - Type
      - **http**
   *  - Send String
      - **GET /basic/ \\r\\n**
   *  - Receive String
      - **200 OK**
   *  - User Name
      - **user.1**
   *  - Password
      - **password**

.. NOTE:: In case you were wondering, the receive string is NOT case sensitive.
 
   By default, in v11.x (which you are being tested on) the default HTTP monitor uses HTTP v1.0.  
   If you application required HTTP 1.1 you would require a different send string, something like
   **GET /basic/ HTTP/1.1 \\r\\n Host: <host name>\\r\\n\\r\\n**.
   
   An excellent reference for crafting HTTP monitors can be found on ASK F5 at https://support.f5.com/csp/article/K2167. 
   

Click **Finish** and you will be taken back to **Local Traffic > Monitors**

Do you see your new Monitor?

.. HINT:: 

   Check the lower right hand corner of the Monitors list, here you
   can go to the next page or view all Monitors. You can change the number of records 
   displayed per page in **System > Preferences**.

Go to **www\_pool** and replace the default **http** monitor with your
**www\_test** monitor.

*Q1. What is the status of the pool and its members?*

*Q2. Go to* **Virtual Servers** *or* **Network Map** *, what is the status of
your virtual server?*

Just for fun **Reverse** the monitor. Now when **200 OK** is returned it
indicates the server is not responding successfully.

*Q3. What is status of your pool and virtual server now?*

You can see where this would be useful if you were looking for a 404
(bad page) or 50x (server error) response and pulling the failed member
out of the pool.

.. WARNING::

   Be sure to un-reverse your monitor before continuing.

(Optional) Pool Member and Virtual Servers
------------------------------------------

In this task, you will determine the effects of monitors on the status
of pools members.

Create **mysql** monitor for testing.

Go to **Local Traffic > Monitors** and select **Create**.

+----------------------+------------------+
| **Name**             | mysql\_monitor   |
+======================+==================+
| **Parent Monitor**   | mysql            |
+----------------------+------------------+
| **Interval**         | 15               |
+----------------------+------------------+
| **Timeout**          | 46               |
+----------------------+------------------+

Effects of Monitors on Members, Pools and Virtual Servers
---------------------------------------------------------

Go to **Local Traffic > Pools > www\_pool** and assign **mysql\_monitor** to the pool.

Observe Availability Status of **www\_pool.** The pool status
momentarily changes to **Unknown**.

*Q1. Since the* **mysql\_monitor** *will fail, how long will it take to
mark the pool offline?*

Go to **Local Traffic > Pool > www\_pool** and then **Member** from the
top bar and open member **10.1.20.13:80** and note the status of the
monitors.

Open **Local Traffic > Network Map > Show Map**

*Q2. What is the icon and status of* **www\_vs**?

*Q3. What is the icon and status of* **www\_pool**?

*Q4. What is the icon and status of the* **www\_pool** *members?*

*Q5. How does the status of the pool configuration effect the virtual
server status?*

Clear the virtual server statistics.

Browse to **http://10.1.10.100** and note the browser results,
statistics and tcpdump.

Disable **www\_vs** and clear the statistics and ping the virtual
server.

*Q6. What is the icon and status of* **www\_vs**?

Browse to **http://10.1.10.100** and note the browser results,
statistics and tcpdump.

*Q7. Did traffic counters increment for* **www\_vs**?

*Q8. What is the difference in the tcpdumps between Offline (Disabled) vs
Offline (Enabled)?*

.. WARNING::

   Make sure all virtual servers, pools and pool members are **Available** before continuing.

(Optional) More on status and member specific monitors
------------------------------------------------------

Go to **Local Traffic > Pool > www\_pool** and then **Member** from the
top bar and open member **10.1.20.13:80.** Enable the **Configuration:
Advanced** menus.

*Q1. What is the status of the Pool Member and the monitors assigned to
it?*

In **Health Monitors** select **Member Specific** and assign the
**http** monitor and **Update.**

Go to the **Network Map**.

*Q2. What is the status of* **www\_vs**, **www\_pool** *and the pool
members? Why?*

Browse to **http://10.1.10.100** and note results of browser and
tcpdump.

*Q3. Did the site work?*

*Q4. Which* **www\_pool** *members was traffic sent to?*


1.05 - Choose virtual server type and load balancing type to fit application requirements
=========================================================================================

Ratio Load Balancing
--------------------

Go to Local **Traffic > Pools** and select **www\_pool** and then
**Members** from the top bar or you could click on the **Members** link
in the **Pool List** screen.

.. HINT:: 

   When we created the pool, we performed all our configuration on
   one page, but when we modify a pool the Resource information is under
   the Members tab

Under **Load Balancing** section change the **Load Balancing Method** to **Ratio (Member)**

In drop-down menu, notice most load balancing methods have two options, **(Node)** or **(Member)**.

*Q1. What is the difference between Node and Member?*

Select the first member in the pool **10.1.20.11:80** and change the
**Ratio** of the member to **3**

Open the pool statistics and reset the statistics for **www\_pool**.

Browse to **http://10.1.10.100** and refresh the browser screen several
times.

*Q2. How many Total connections has each member taken? Is the ratio of
connections correct?*

Now go back and put the pool back to **Round Robin** Load Balancing
Method

Reset the pool statistics and refresh the virtual server page several
times.

*Q3. Does the ratio setting have any impact now?*

Priority Groups Lab
-------------------

Let's look at priority groups. In this scenario we will treat the 10.1.20.13
server as if it was is in a disaster recovery site that can be reached
over a backhaul. You want to maintain at least two members in the pool for
redundancy and load.  You would traffic to be distributed t0 10.1.20.13 only during maintenance of one on the two primary servers or if one to the two other pool members fails.

.. NOTE::

   Remove any caching profiles from the www\_vs virtual server (10.1.10.100).

Go to the **www\_pool** **Members** section. Make sure the load
balancing method is **Round Robin**.

Set the **Priority Group Activation** to less than **2** Available
Members.

Select the pool members **10.1.20.11** and **10.1.20.12** and set their
**Priority Group** to **10**.

Review your settings and let's see how load balancing reacts now.

Select the **Statistics** tab, reset the pool statistics, browse to
**http://10.1.10.100** and refresh several times.

*Q1. Are all members taking connections? Which member isn't taking
connections?*

Let's simulate a maintenance window or an outage by disabling a pool
member **10.1.20.11:80** in the highest priority group. This should
cause low priority group to be activated, since number of active members
in our high priority group has dropped below 2.

*Q2. Is the lower priority group activated and taking connections?*

Select a member in the **Priority Group** 10 and **Disable** that pool
member.

Once again, select **Statistics**, reset the pool statistics, browse to the
virtual server and see which pool members are taking hits now.

.. IMPORTANT::

   Once you are done testing re-enable your disabled pool member.

1.06 - Determine how to architect and deploy multi-tier applications using LTM
==============================================================================

(Optional) Working with Analytics (AVR)
=======================================

AVR Lab Setup - Verify provisioning, iRules and Data Group
----------------------------------------------------------

In this task you prep the BIG-IP for the Application Visibility and
Reporting (AVR) lab. In the interest of time AVR has already been
provisioned, a data group has been built and two iRules have been
prepopulated on the BIG-IP.

AVR is **NOT** provisioned by default, but should be already be
provisioned on this BIG-IP. You can verify this by going to **System >>
Resource Provisioning**. Application Visibility and Reporting should be
set to Nominal.

*Q1. What resources does AVR require to be provisioned?*

Go to **Local Traffic > iRules > iRules List** and select **Data Group
List** from the top-bar

A **Data Group** named **user\_agents** has already been created for
you.

+--------------+-------------+
| **String**   | **Value**   |
+==============+=============+
| agent        | IE9         |
+--------------+-------------+
| agent1       | IE11        |
+--------------+-------------+
| agent2       | IE11        |
+--------------+-------------+
| agent3       | Chrome      |
+--------------+-------------+
| agent4       | Firefox     |
+--------------+-------------+
| agent5       | Safari      |
+--------------+-------------+
| agent6       | iPhone5     |
+--------------+-------------+
| agent7       | iPhone6     |
+--------------+-------------+
| agent8       | iPhone6     |
+--------------+-------------+
| agent9       | Android     |
+--------------+-------------+

To save time and typing errors, the iRules required for this lab have
already been configured on the BIG-IP. Find the iRules below under
**Local Traffic > iRules > iRule List** and verify the iRules exist.
We use these iRules to modify traffic and give Analytics something
interesting to see.

**random\_client\_ip** - randomizes the client IPs and user agents using
the data group you built::

   when CLIENT_ACCEPTED {
   # Create a random IP address and use it to replace the client IP to simulate many clients
   # going through the virtual
      snat [expr int(rand()*255)].[expr int(rand()*255)].[expr int(rand()*255)].[expr int(rand()*254)]
      virtual avr_virtual2
   }
   when HTTP_REQUEST {
   # When the HTTP request comes in, select a random user agents and put that agent
   # in the user-agent HTTP header to simulate many different user agents
      set my_index [expr int(rand()*10)]
      set user_agent [class element -value $my_index user_agents]
         HTTP::header replace user-Agent $user_agent
   }

*Q2. Review the iRule, what profiles are required on the virtual server?*

**delay_server** - introduces delay into server-side traffic::

   when LB_SELECTED {
   # After a member has been selected by the load balancing algorithm introduce delay
   # (in milliseconds) on the specified URL or server
      if {([LB::server addr] equals "10.1.20.13") and ([HTTP::uri] equals "/welcome.php")} { after 10}
	
      if {[LB::server addr] equals "10.1.20.13"} {after 20}
   }

*Q3. Review the iRule, what profiles are required on the virtual server?*

Create an Analytics Profile
---------------------------

Create an analytics profile that will be used with a virtual server.

In the Configuration Utility, open the **Local Traffic > Profiles >
Analytics** page, and then click **Create**.

Create an analytics profile using the following information, and then
click **Finished**.

+--------------------------+-----------------------------------------+
| **Profile Name**         | custom_analytics                        |
+==========================+=========================================+
| **Collected Metrics**    | Max TPS Throughput                      |
|                          |                                         |
|                          | Page Load Time                          |
+--------------------------+-----------------------------------------+
| **Collected Entities**   | URLs                                    |
|                          |                                         |
|                          | Countries                               |
|                          |                                         |
|                          | Client IP Addresses                     |
|                          |                                         |
|                          | Client Subnets                          |
|                          |                                         |
|                          | Response Codes                          |
|                          |                                         |
|                          | User Agents                             |
|                          |                                         |
|                          | Methods                                 |
+--------------------------+-----------------------------------------+
 
Create a Web Application
------------------------

.. NOTE:: 

   The **avr_virtual2** destination address is the default gateway of the web servers.

.. list-table::
   :widths: 40 30

   *  - Name 
      - **avr_virtual2**
   *  - Destination Address
      - **10.1.20.240**
   *  - Service Port
      - **80 (HTTP)**
   *  - Configuration
      - **Advanced**
   *  - HTTP Profile
      - **http**
   *  - Source Address Translation
      - **Auto Map**
   *  - Analytics Profile
      - **custom_analytics**
   *  - iRules 
      - **delay_server**
   *  - Default Pool 
      - **www_pool**
 
Create another virtual server using the following information, and then
click Finished.

.. NOTE:: 

   Within the iRule attached to this virtual you are pointing traffic to the virtual server you created above, so avr_virtual2 had to be created first.

.. list-table::
   :widths: 40 30

   *  - Name 
      - **avr_virtual1**
   *  - Destination Address
      - **10.1.10.90**
   *  - Service Port
      - **80 (HTTP)**
   *  - HTTP Profile
      - **http**
   *  - iRules 
      - **random_client_ip**

Visit the Web Site to Generate AVR Data 
---------------------------------------

Use a web browser to access the virtual server, and then view the
**Analytics** statistics.

Use a new tab to access **http://10.1.10.90**. It is recommended you use
private browsing.

Type **<Ctrl>F5** several times to refresh the page. Do this for each of
the next steps.

Click the **Welcome** link, and then click the banner at the top of the
page to return to the home page.

Click the **Stream Profile Example** link. Click the banner at the top
to return to the home page.

Click on the **Multiple Stream Example** link. Click the banner at the
top of the page to return home.

Click the **Request and Response Headers** link. Click the banner at the
top of the page to return home.

Close the F5 vLab Test Web Site tab.

Open the **Statistics > Analytics > HTTP > Overview page**.

.. HINT::

   If you don't see anything, set your Auto Refresh to 1 minute. It may
   take up to 5 minutes for analytics data to load.

View the Analytics Reports
--------------------------

Use the **Analytics** page to view statistics information on the BIG-IP
system.

In the Configuration Utility, refresh the **Statistics > Analytics >
HTTP > Overview** page until you see statistics.

Once you have data set the **Override** time range to list box, select
**Last Hour**.

Open the **Transactions** page from the top bar. Let's review some of
the various data compiled.

From the **View By** list box, select **Pool Members**.

From the **View By** list box, select **URLs**.

From the **View By** list box, select **Response Codes**.

Users are complaining of intermittent slow responses.

Open the **Latency > Server Latency** page, and then from the **View
By** list box, select **Pool Members**.

*Q1. Does a particular pool member seem to be an issue?*

In the **Details** section, click **10.1.20.13:80**, and then from the
**View By** list box, select **URLs**.

Go to **Transactions**.

*Q2. What country has the most transactions?*

*Q3. What are the top two User Agents?*
