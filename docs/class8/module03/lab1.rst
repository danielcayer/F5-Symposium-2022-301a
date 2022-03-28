1.04 - Choose correct profiles and settings to fit application requirements
===========================================================================

Creating and Importing SSL Certs and Key
----------------------------------------

You want the highest levels of encryption between the BIG-IP and the
client, but you do not require the same levels of encryption between the
BIG-IP and the server and you would like to save server processing
power. You are going to create new SSL certificates with 2048 bit keys
for client-side SSL and import a certificate you own that has a 1024 bit
keys for the server-side SSL processing.

Create the client-side SSL certificate and key.

Go to **System >> File Management >> SSL Certificate List** and select
**Create** and enter the following information\ **.**

+----------------------------+----------------------------+
| Name                       | **new\_ssl\_cert**         |
+============================+============================+
| Issuer                     | **Self**                   |
+----------------------------+----------------------------+
| Common Name                | **ltm301a.f5demo.com**     |
+----------------------------+----------------------------+
| Division                   | **Training**               |
+----------------------------+----------------------------+
| Organization               | **F5**                     |
+----------------------------+----------------------------+
| Locality                   | **Lab**                    |
+----------------------------+----------------------------+
| Country                    | **United States**          |
+----------------------------+----------------------------+
| State                      | **Washington**             |
+----------------------------+----------------------------+
| Email                      | **example@f5demo.com**     |
+----------------------------+----------------------------+
| Subject Alternative Name   | **<leave this blank>**     |
+----------------------------+----------------------------+

By default, a self-sign certificate starts with a Lifetime of 365 days
and the key type is 2048 bit RSA.

Import the SSL certificate and key below to the BIG-IP to be used for
server-side encryption by selecting **Import**.

You can find the cert and key under the **Lab Guides** link on your
browser bookmark bar. Look for the **Cert 301a LTM Architect.txt**
file/link. Open the link and the certificate and key are in there under
**LAB 5 - SSL**

You may want to import the Key first, as it will validate certificate if
your naming both the same. If you were to import the certificate first
and then import a Key with the same name you could technically attached
an invalid key to the certificate.

Import the **Key** (Type), named **import\_ssl\_cert**, leave the
**Password** blank.

**RSA Private Key**::

   -----BEGIN RSA PRIVATE KEY-----
   MIICXQIBAAKBgQCobsrka60VT1TLfQsamdtQCbvfnGC9ibiTtPjaHXRBpNV70prY
   rihQqj3pBNlE4dvK0ucF49gGF5HXpKZDqZWXai3AnJhia248FRyT/ezxAjVqamFh
   c3Lhx5ykSIxWzzw+VBuwTIvg2MS3aE3P/2CQ8MI02/uiLsya0eydw3EyiQIDAQAB
   AoGBAKPN3BP5hALNfDHKEhp0tw1H6ia19n9eiNtdjQbSzlVo8RXS5DUGar7IUh1k
   UcjjvtWp9nOL+nMySOvnfKshKhDbB/73NRg7VZ1eZL6K/0vv37KbDSU8KTQssSb3
   myKxrRVMbxxpPujpUNT3gd6XqdUXyPjWuJOkCGLgVfGhANHhAkEA2ssfpYXgID2a
   0B+VC+FaAB0GBg5P/oQF3mTPSj460totqGWeXi7beuYXgFYLbn5BNHCsDeVclZMy
   ws7k4SZiiwJBAMUTSRX3rjeZBpl0zF6xr/JFfw0Fd4lgKDO+hFZvrbhDUYIU9D08
   ROyH+FViQ0IoV2BtW9IZDtN/GzoroLE/hbsCQQCG5cwaSwOX/UOxZHeJ/qR2A28O
   Vs69dvsDVpZ0CRBNppWWCSlummdaS/lbeDHOK2vagEmku7CszDf04ok9xGPnAkAl
   5rubmfLLhoaaZLgZThsEgREaM/71UKTqrq1M4lWKpoe6eMUwMbMfulasNSWyR4fm
   Z6HNGjybuEpIObPB4vGZAkBo1LKCZXas+mxRfZeucfzPLOx5a8XmwtV57br+ivnK
   n0OIrSExckYHeeWb4sFl8Y/S85yyBqHS5q5v9s/x2fvp
   -----END RSA PRIVATE KEY-----

Import the certificate.  Type **Certificate** and named **import\_ssl\_cert**

**Certificate**::

   -----BEGIN CERTIFICATE-----
   MIIBuzCCASQCCQCTVAeV4noavTANBgkqhkiG9w0BAQUFADAiMSAwHgYDVQQDExds
   aW51eDMyc2VydmVyMS5mNXNlLmNvbTAeFw0xMDA2MTkyMTI2NTZaFw0yMDA2MTYy
   MTI2NTZaMCIxIDAeBgNVBAMTF2xpbnV4MzJzZXJ2ZXIxLmY1c2UuY29tMIGfMA0G
   CSqGSIb3DQEBAQUAA4GNADCBiQKBgQCobsrka60VT1TLfQsamdtQCbvfnGC9ibiT
   tPjaHXRBpNV70prYrihQqj3pBNlE4dvK0ucF49gGF5HXpKZDqZWXai3AnJhia248
   FRyT/ezxAjVqamFhc3Lhx5ykSIxWzzw+VBuwTIvg2MS3aE3P/2CQ8MI02/uiLsya
   0eydw3EyiQIDAQABMA0GCSqGSIb3DQEBBQUAA4GBAH1e+FXvNOnKlP5RO5wKVjG0
   C8F4Xww462beL2LeYZvV3ZjDoTUU0CNkkOnOKMbLkaiTICpBdd836sIiloAyV8M1
   2YZwxgwP/fV4ycTjHUnloEBmmmBVmW3M5DzLGA1k9cB1dUly5koIoRE9mYRWm9V2
   ZzYkbZf96KMh8zH47R4y
   -----END CERTIFICATE-----

*Q1. What is the common name of your imported certificate and when does
it expire?*

SSL Profile and Virtual Servers
-------------------------------

Now you will create a custom client and server side SSL profiles using
your new certificates and key and attach them to you **secure\_vs** and
test.

Create a custom client SSL profile using your **new\_ssl\_cert**
certificate, by going to **Profiles > SSL > Client**.

Name you new profile **my-client-ssl** and place **new\_ssl\_cert** in
the **Certificate** and **Key** drop-downs.

Make sure you **Add** the certificate and key before you Finish.

Go to the SSL Server profiles and create a new profile named
**my-server-ssl** with your **import-ssl-cert** certificate and key.

Replace the current default client-side and server-side SSL profiles
with your new SSL profiles.

Browse to **http://10.1.10.115**.

*Q1. Did it work?*

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

(Optional) Effects of Monitors on Members, Pools and Virtual Servers
--------------------------------------------------------------------

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

(Optional) Create an Inband monitor and Active monitor with an Up Interval
--------------------------------------------------------------------------

In this exercise, you need to limited the amount of monitor traffic to
your back in servers. You will use the basic inband monitor, but you
would like the servers to come up faster than default of 5 minutes. 
You will combine Inband and Active monitors to accomplish this.

Create an inband monitor named **my\_inband**.  Use all the defaults.

Note the 300 second retry timer, after 3 failures in a 30 second period the
BIG-IP will mark the member down and will not check the member again for
5 minutes.

Create a new custom monitor as the active monitor. Make the monitor an **http** monitor called **active\_http**, with an **Up Interval** of **60** seconds and a **Time Until Up** of **30** second.

(Optional) Assign the Inband monitor to a pool and test
-------------------------------------------------------

You are going to begin by removing the current monitors for the
**www\_pool** and replacing them with the **my\_inband** monitor only.

Go to the **www\_pool** and remove all monitors and **Update**. Your
pool members show now be **Unchecked**.

*Q1. What is the status of the* **www\_pool** *and* **www\_vs**
*configuration objects? Is the web site accessible? Why?*

Add the **my\_inband** monitor to the **www\_pool**.

*Q2. What are the status of* **www\_pool** *and* **www\_vs**? *Can you access
the web site?*

Let's simulate a failure. Open a new browser tab to
**https://10.1.1.252:10000** this will bring you to **Webmin** on the
back-end server::

   Username: root 
   Password: default.F5demo.com

Under **Servers** on the side-bar, select the **Apache Webserver** link.
In the upper right corner select the **Stop Apache** link. This will
effectively bring the web sites down.

Go to your pool statistics. After 30 seconds what is the status of the
**secure\_pool** and **www\_pool?**

*Q3. Why is the* **www\_pool** *still showing up?*

Attempt to access **http://10.1.10.100** and refresh several times.

*Q4. What is the status of the* **www\_pool** *now?*

In the **Webmin** tab, in the upper right corner, select **Start Apache**.

Once Apache is started, refresh you pool statistics page. Then try to
browse to **http://10.1.10.100/**

*Q5. What are the pool statuses and why?*

It will be 300 seconds before the BIG-IP attempts to send any traffic to
the offline pool members.

Go to the **www\_pool** and add the **active\_http** monitor to the
pool.

Open a terminal window to BIG-IP and run the following tcpdump::

   tcpdump -nni server_vlan -X -s0 port 80

*Q6. How often to you see monitor traffic to the* **www\_pool**?

In the **Webmin** tab, **Stop Apache** again and attempt to browse
**http://10.1.10.100** and refresh several times. Check the status of your
pools. The **secure\_pool** and **www\_pool** should be offline.

*Q7. How often to you see monitor traffic to the* **www\_pool**?

In the **Webmin** tab **Start Apache**.

*Q8. Did the www\_pool come up within 30 seconds without client traffic?
What did the tcpdump show?*

(Optional) Create an Extended Application Verification (EAV) monitor
--------------------------------------------------------------------

Log on to the F5 DevCentral site **http://devcentral.f5.com** and go to the
following link:

https://devcentral.f5.com/codeshare/http-monitor-curl-basic-get

If you don't have an account then create one. You'll be glad you did.

This monitor is also in **Module 3.14** of this document, but working from
F5 DevCentral is more appropriate.

You will be using this monitor as your new external monitor, a copy of
the code is on your desktop in a plain text file called
\ **eav-http-monitor**.

A copy of this monitor is also in the **/root** directory on the BIG-IP. To
get a feel of how it works go to the BIG-IP CLI and test it against once
of the **www\_pool** members::

   cd /root
   /bin/sh eav-http-monitor 10.1.20.11 80

The first two parameters of an EAV are always IP address and Port. When using an
External monitor on a pool, the pool supplies these. Here you have to supply them.

*Q1. What was the stdout output? Did this indicate the member was
Available?*

Go to **System > File Management > External Monitor Program File List** and
select **Import**

**Choose File** to find the **eav-http-monitor** on your desktop, name
it **eav-http-monitor** and select **Import**

Create a monitor named **eav-http-monitor**, select type **External**
and **eav-http-monitor** as your **External Program**.

Apply the **eav-http-monitor** monitor to **www\_pool**. Remove all
other monitors.

*Q2. Are your members up? What would happen if the external monitor returned* **DOWN**\ *?*


(Optional) Working with Analytics (AVR)
=======================================

You will use an analytics profile that will be used to test Application Visibility and
Reporting (aka Analytics). In interest of time and to avoid typing
errors the iRules and Data Groups have been predefined.

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
