1.06 - Determine how to architect and deploy multi-tier applications using LTM
===============================================================================
Multi-tier application configurations are scenarios where the path traverses the BIG-IP multiple times or goes through more than one BIG-IP.  The early versions of this were a Firewall Sandwich.  The idea of a firewall sandwich is traffic coming in from a client hits an external BIG-IP and traffic is SSL-Decrypted.  Traffic is then passed through a collection of firewalls for inspection, and then onto anothe BIG-IP for re-encryption and distribution to the application servers.  This is documented here: https://www.f5.com/services/resources/white-papers/load-balancing-101-firewall-sandwiches

A situation you may see more often is SNI Routing.  SNI stands for Server Name Indication.  In TLS the SNI value is sent clear text on an initial connection request, the BIG-IP uses a virtual server to distribute traffic to multiple virtual servers based on the SNI value.  An example of this is documented here: https://community.f5.com/t5/technical-articles/sni-routing-with-big-ip/ta-p/282018.

Understand connection based architecture and when/how to apply
--------------------------------------------------------------
We will now create a multi-tier SNI routing configuration.  

Start with creating 2 virtual servers with 1 pool each as follows:

.. code-block:: tcl

    create ltm pool secure1_pool members add { 10.1.20.11:443 10.1.20.13:443} monitor https
    create ltm pool secure2_pool members add { 10.1.20.12:443 10.1.20.14:443} monitor https
    create ltm virtual secure1_vs destination 10.1.5.16:443 ip-protocol tcp persist replace-all-with { cookie } pool secure1_pool profiles add { clientssl serverssl tcp http } source-address-translation { type automap } translate-address enabled translate-port enabled
    create ltm virtual secure2_vs destination 10.1.5.15:443 ip-protocol tcp persist replace-all-with { cookie } pool secure2_pool profiles add { clientssl serverssl tcp http } source-address-translation { type automap } translate-address enabled translate-port enabled

Now we will create a traffic policy by going to **Local Traffic, Policies, Policy List**.  Click on **Create**.

    .. image:: /_static/301a/p21.png
        :scale: 80%

Now click on create in the Rules section and configure the first rule as below:

    .. image:: /_static/301a/p22.png
        :scale: 80%

Now click on create again to create a 2nd rule as follows:

    .. image:: /_static/301a/p23.png
        :scale: 80%

Now create a new virtual server that will be the entry point for the clients:

 .. code-block:: tcl

     create ltm virtual sni_vs destination 10.1.10.111:443 ip-protocol tcp persist replace-all-with { ssl } policies replace-all-with { sni_routing } profiles add { tcp }

Now go to the browser on the desktop and go to https://secure1.f5demo.com.

What Pool member did you get?  Why?

Now go to https://secure2.f5demo.com

What Pool member did you get?  Why?


SNAT/persistence/SSL settings in multi-tiered environment
---------------------------------------------------------

On the BIG-IP configuration page go to **Local Traffic, Virtual Servers** and select sni_vs virtual server and then click on the Resources Tab.  Change default persistence from SSL to None and click on update.  What happens?

Now go to the secure1_vs Resources tab, and select Default Persistence Profile of None.  Start a new instance of Chrome and go to https://secure1.f5demo.com.  Refresh multiple times.  Does it ever change pool members?  Why?

Now go to the secure1_vs and remove the ClientSSL and ServerSSL Profiles.  Hit the page again.  Why is it failing to load?

Now put the ClientSSL and ServerSSL profiles back, and change Source Address Translation to None. Does the connection work now?  What if you add source address translation to the sni_vs Virtual Server?  Now go to https://secure2.f5demo.com which has source address translation enabled, and sni_vs has source address translation enabled.  Does the site still work?  Why?


Idenity which device handles specific configuration objects in multi-tiered deployment
--------------------------------------------------------------------------------------

