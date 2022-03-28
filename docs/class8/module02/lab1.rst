1.01 - Determine which configuration objects are necessary to optimally deploy an application
=============================================================================================

Working with profiles
---------------------

Create a new pool called **secure\_pool**, using the **https** default
monitor and add the following members, **10.1.20.11:443,
10.1.20.12:443** and **10.1.20.13.443.**

Create new virtual server **secure\_vs** at **10.1.10.115:443** with
**TCP** the profile using your new **secure\_pool**.

Open two separate PuTTy/Terminal windows to the BIG-IP and run the following
tcpdumps using the jumpbox 10.1.10.x IP address.

Window 1::

   tcpdump -nni client_vlan -X -s0 host 10.1.10.199 and 10.1.10.115

Window 2::

   tcpdump -nni server_vlan -X -s0 host 10.1.10.199

Verify your virtual server is available and then browse to
**https://10.1.10.115**. View the TCPDUMPs.

*Q1. Did site work? Why didn't you need to SNAT? Did you need SSL
profiles?*

*Q2. Could you use L7 iRules or profiles to view or modify the request or
response? Why or why not?*

Modify **secure\_vs** to use the HTTP (80) **www\_pool**. 

Verify your virtual server is available and then browse to
**https://10.1.10.115**.  View the TCPdumps.

*Q3. Did site work? Why or not?*

Change SSL Profile to include the default **clientssl** then update.

Browse to **https://10.1.10.115** and observe the tcpdump.

*Q4. Did site work? What did you observe in the tcpdumps? Did you need an
HTTP profile?*

On the **secure\_vs** in the virtual server **Resources** section enable
**cookies** as the **Default Persistence Profile** and then **Update**.

*Q5. Did it work? What was needed to add cookie persistence?*

Browse to **https://10.1.10.115/** scroll and select **Display Cookie** in
the **HTTP Request and Response Information** section on the web page.

*Q6. What nodes do the pictures come from? What is the name of the cookie
inserted begin with?*

Assign the **secure\_pool** to the **secure\_vs** once again. Browse to
**https://10.1.10.115**

*Q5. Did site work? Why or why not?*

Troubleshoot and fix.

*Q6. What profile was needed to correct the error?*


TCP Express
-----------

Set client-side and server-side TCP profiles on your virtual server
properties.

From the drop-down menus place the **tcp-wan-optimized** profile on the
client-side and the **tcp-lan-optimized** profile on the server-side.

Note the custom boxes in each of the TCP profiles you used.

*Q1. What is the idle timeout in each profile? Why might you want to
change it?*

*Q2. What is the Nagle selection in the default TCP, tcp-wan-optimized
and tcp-lan-optimized profiles? Why might you want to change it?*

*Q3. What happens if you increase the proxy buffer sizes?*


1.02 - Determine whether or not an application can be deployed with only the LTM module provisioned
====================================================================================================

1.03 - Identify the difference between deployments (e.g., one arm, two arm, npath, Direct Server Return/DSR)
============================================================================================================

