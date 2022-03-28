2.03 - Determine how to perform basic device configuration
==========================================================

NTP is essential for a number of BIG-IP functions, in particular, when
creating Device Service Clusters. DNS configured on the BIG-IP can also
be of value.

Configure DNS and NTP
---------------------

.. NOTE::

   The BIG-IP DNS has been preconfigured in the UDF environment

Go to **System > Configuration > Device > General**

Using the **Device** dropdown on the top-bar you can select DNS and NTP configuration UIs. 

Configure DNS to use **8.8.8.8** Google open DNS server and verify it
works. In BIG-IP command line terminal window test DNS from the CLI or
TMSH enter::

   dig pool.ntp.org

Now that you've configure DNS, configure NTP using **pool.ntp.org**.

Configure VLAN tagging
----------------------

Here you will set up multiple VLANs on the same interface and assign IP
addressing. You will be using one of these VLANs when you do the High
Availability lab.

Go to **VLANs** and create two **tagged** VLANs on interface **1.3**.

The first VLAN will be named **vlan-30** have a
tag of **30** and on interface **1.3** will be placed in the **Tagged** box.

The second tagged VLAN will be named **vlan-40** on interface **1.3** and have
a tag of **40**.

Make sure you place the interface into correct box.

Create a new self IP named **HA-IP** and **10.1.30.245/24** and assign
it to vlan **vlan-30**.

You will be using this IP address for building a device service cluster
in a later lab.

Create a partition
------------------

Partitions are basically configuration containers. Users assigned to a
partition can only view their partition configuration and configuration items in the **Common** partition. Depending on their role a user may modify and create configuration items within their partition and use (but not modify) configuration items in
the common partition.

To create a new partition, go to **System > Users > Partition List** and
select create

Create a new partition named **my\_partition**. There is really not a
whole lot to it.

Create and place a user in a partition with a specific role
-----------------------------------------------------------

Create a new local user by going to **System > Users > User List** and
selecting create.

Create a new user **testuser** with **testpass** as the password and set
their **Role** to **Manager**, assign them to the **test\_partition**
partition and give them **tmsh Terminal Access**

Open a new private browser to the BIG-IP, or log out and log back in
under your current browser as the new user **testuser/testpass**.

*Q1. In the upper right of the BIG-IP WebUI what partition are you in?*

Look at the virtual servers. You can see these because they were all
built in the Common partition, but you cannot modify them. If you go
into a virtual server you will see the selections greyed out.

As you can see, you can view but not change things in common. But you
can use things in the **Common** partition to build your own configuration.

Build a new HTTP virtual server named **test\_vs**, with a destination
IP of **10.1.10.120** and used the **www\_pool** in the **/Common**
partition as the default pool.

In this case we are taking advantage of the **Common** partition nodes and
pools to build are virtual server.

Log out and log in as **admin** (or go to your other browser window that is
logged in as admin)

Go to the **Virtual Server List**.

*Q2. Do you see the* **test\_vs** *just created?*

Go to the upper right-hand corner and select **test\_partition**. You
can now see the **test\_vs** virtual server. Since you are an admin you
can also modify the virtual server as necessary

SSH to your BIG-IP and log in with the new user name and password.  You should be
taken directly into the **tmsh**.

Note the prompt, your partition name is there.

Now let's make a change to the test\_vs::

   mod ltm virtual test\_vs description "Partition Testing"

In the BIG-IP WebUI go to your **test_vs** virtual server.

*Q3. Do you see your change? Is your change permanent?*

.. NOTE::

    **The following lab portion is probably not on the 301, but as I have you playing with
    partitions this is something if feel you should know.**

SSH to the BIG-IP and log in as **root**. **cat** or **more** bigip.conf
and look for you **test\_vs** virtual::

   cat bigip.conf
   more bigip.conf

*Q4. Did you find it in /config/bigip.conf?*

Each partition gets its own "folder" where its configuration is stored
under the **partitions** directory in the **/config** directory. At the
BIG-IP CLI prompt::

   cd /config/partitions/test_partition
   ls
   cat bigip.conf

*Q5. Did you find your virtual server? Is the tmsh change you made in
there?*

As **testuser** at the tmsh prompt type: **save sys config**

Look at your **bigip.conf** in the **test_partition**.

**Q6. Do you see the change now?**

Attempt to exit tmsh to get to the Linux CLI.

*Q7. Where you able to?*

Remote Authentication against LDAP
----------------------------------

.. NOTE::

   Changes to the lab environment no longer allow this lab to work, but it does give
   you the general concept of how remote authentication is set up.

Go to **System > Users > Authentication** and select **Change** under **User
Directory**.

Now select **LDAP** from the **User Directory** drop-down and enter the
following

+-------------------------+------------------------+
| Host                    | 10.1.20.252            |
+-------------------------++-----------------------+
| Remote Directory Tree   | dc=f5demo,dc=com       |
+-------------------------+------------------------+
| Bind DN:                | cn=Directory Manager   |
+-------------------------+------------------------+
| Bind Password/Confirm   | default                |
+-------------------------+------------------------+
| Role                    | Administrator          |
+-------------------------+------------------------+

Open a new private browser window to **bigip01** at **https://10.1.1.4** and
logon using the LDAP account **adminuser/password**.

*Q1. Were you successful?*

Try logging with the local account **testuser/testpass**.

*Q2. Were you successful?*
