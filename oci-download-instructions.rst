Deploying BIG-IP VE in Oracle Cloud Infrastructure
==================================================

OCI currently requires a `Bring Your Own Image` and `Bring Your Own License` for deployments.

To deploy BIG-IP VE manually in to OCI:

- :ref:`Step 1: Download a BIG-IP VE image <Oracle_download_step_1>`
- :ref:`Step 2: Create an Oracle object storage bucket <Oracle_download_step_2>`
- :ref:`Step 3: Deploy a BIG-IP VE instance <Oracle_download_step_3>`
- :ref:`Step 4: Configure HA <Oracle_download_step_4>`



.. _Oracle_download_step_1:

Step 1: Download a BIG-IP VE image
----------------------------------

The first step is to get a BIG-IP VE image (a .qcow2.zip file) onto a local drive. You will upload this image to Oracle storage.

1. In a browser, open the F5 Downloads page at https://downloads.f5.com and register or log in.

2. On the Downloads Overview page, select :guilabel:`Find a Download`.

3. Under Product Line, select :guilabel:`BIG-IP <version>_Virtual Edition`.

4. Under Name, select :guilabel:`13.1.0.x_Virtual-Edition`.

5. Click one of the ``.qcow2.zip`` image files.

6. If the End User Software License is displayed, read it and then click :guilabel:`I Accept`.

7. Choose the download location closest to you.

8. When the file finishes downloading, unzip the ``.qcow2.zip`` file to a local drive. If you are using Windows, you can use 7-Zip to do this. For Linux or Mac you can use unzip.

9. After it's unzipped, if necessary extract the ``.qcow2`` from the ``.tar`` file with ``tar xvfz <filename>.tar``. You can also use 7-Zip to do this.



.. _Oracle_download_step_2:

Step 2: Create an OCI Object Storage Bucket and create Pre-Authenticated Request
--------------------------------------------------------------------------------

Now create an OCI Object Storage Bucket. In the next step you will upload the ``.qcow2`` file to it.

1. Open the Oracle OCI console for the Region in which you want to deploy for example: ``PHX``. |console|.

2. In the top pane, click :guilabel:`Storage`.

3. In the left pane, click :guilabel:`Object Storage`.

4. Click :guilabel:`Create Bucket`.

5. In the :guilabel:`BUCKET NAME` box, type a name.

6. Click :guilabel:`Create Bucket`.

7. In the center pane, find the file for the image you uploaded.

8. Hover over the :guilabel:`...`.

9. From the pop-up menu select :guilabel:`Create Pre-Authenticated Request`.

10. In the :guilabel:`NAME` box, type a name.

11. In the :guilabel:`EXPIRATION DATE/TIME` box, select a date for expiration. All other presets can be left.

12. The OCI Console will present you with a :guilabel:`PRE-AUTHENTICATED REQUEST URL`. Copy this for use in Step 3.



.. _Oracle_download_step_3:

Step 3: Create a Custom Image
-----------------------------

To deploy the BIG-IP VE you must create a custom image.

2. In the top pane, click :guilabel:`Compute`.

3. In the left pane, type :guilabel:`Custom Images`.

4. Click :guilabel:`Import Image`.

5. In the :guilabel:`NAME` box, type a name.

6. In the :guilabel:`OPERATING SYSTEM` box, select Linux.

7. In the :guilabel:`OBJECT STORAGE URL` box, paste in the URL for the Pre-Authenticated Request you created in Step 2.

8. With the :guilabel:`IMAGE TYPE` radio button, select ``qcow2``.

9. With the :guilabel:`LAUNCH MODE` radio button, select ``EMULATED MODE``.

10. Click :guilabel:`Import Image`.

11. This will begin the import process. This may take some time. Once the import is complete and the Custom Image is ready to use, the icon next to the image name will show ``green``.




.. _Oracle_download_step_4:

Step 4: Deploy a BIG-IP VE instance
-----------------------------------

Now your OCI environment is ready to deploy a BIG-IP VE instance.

To do this, you will need to create the instance from the `Custom Image` we created in Step 3.

1. Go to :guilabel:`Compute -> Instances`.

2. Click the :guilabel:`Create Instance` button.

3. In the :guilabel:`NAME` box, type a name.

4. In the :guilabel:`AVAILABILITY DOMAIN` box, select an Availability Domain in which you want to deploy the BIG-IP.

5. In the :guilabel:`BOOT VOLUME` radio button, select `Custom Image`.

6. In the :guilabel:`IMAGE` box, select the name of the BIG-IP Custom Image you created in step 3.

7. In the :guilabel:`BOOT VOLUME SIZE(IN GB)` section it will display the size of the volume for the image you uploaded. This will change from BIG-IP TMOS version to version.

8. If you want to create a larger initial Boot Volume, check the :guilabel:`CUSTOM BOOT VOLUME SIZE` box and specify the desired volume size.

9. For the :guilabel:`SHAPE TYPE` radio button select `VIRTUAL MACHINE`.

10. For the :guilabel:`SHAPE` box select an appropriate Shape based on your requirements. Shapes restrict the number vCPUs, VNICs, and memory allocated.

Reference for BIG-IP VE Requirements:
https://support.f5.com/csp/article/K14810

For an overview of the Instance Shapes within OCI:
https://docs.us-phoenix-1.oraclecloud.com/Content/Compute/Concepts/computeoverview.htm

10. In the :guilabel:`Networking` section select the `VIRTUAL CLOUD NETWORK` and `SUBNET` you want to attach the BIG-IP VE management interface to.

11. If you want to directly access the BIG-IP from the Internet, you can check the :guilabel:`ASSIGN PUBLIC IP ADDRESS`.

12. You will need to modify any Security Lists to allow TCP ports `22` and `443` inbound (Single-NIC mode requires `8443` for webui) and specify which IP addresses are allowed to communicate with the management interface.


.. _Oracle_download_step_4:

Step 4: Configuring an HA cluster
-----------------------------------

Once you have deployed two BIG-IP's you can create a High Availability (HA) Device Service Cluster and configure failover objects to move to the Active BIG-IP device within that cluster.

First follow the recommendations to configure BIG-IP Device Service Clustering
https://support.f5.com/kb/en-us/products/big-ip_ltm/manuals/product/bigip-system-device-service-clustering-administration-13-1-0.html

Once that is complete, you can create your failover objects for the OCI environment. Failover objects in F5 BIG-IP terms are typically, Floating Self-IP, SNATs Addresses and Virtual Servers.

In the OCI environment, Public and Private IP addresses are mapped to Floating Self-IP Addresses and Virtual Servers. To create a new Virtual Server for example, start by creating a new Private IP address under one of the Secondary VNICs assigned to the BIG-IP Instance in OCI.

Ensure the following protocols/ports are permitted between nodes. Note : No matter which Port lockdown setting used these ports are permitted. OCI security lists will need to be modified to allow the following:

    UDP/1026 (network failover)
    TCP/1028 (connection & persistence mirroring)
    TCP/4353 (CMI – peer communication)


1. Go to :guilabel:`Compute -> Instances -> Instance Details` for one of the BIG-IP Instances you deployed in Step 3.

2. In the left pane, click :guilabel:`Attached VNICs`.

3. In the center pane, click on the VNIC which corresponds with the network on which you want to create your failover IP Address. Copy the `ocid` from this VNIC and save it for use later in this section.

4. In the center pane, click the :guilabel:`Assign Private IP Address` box, select an Availability Domain in which you want to deploy the BIG-IP.

5. In the :guilabel:`PRIVATE IP ADDRESS` box, you can type in a preferred IP address or leave blank to have an available one assigned for you.

6. In the :guilabel:`PUBLIC IP ADDRESS` box, if you want to assign a `Public IP Address` to be mapped externally to the Internet, select the :guilabel:`Reserved Public IP Address` radio button.

7. In the :guilabel:`COMPARTMENT` box, select the `Compartment` which will likely be the same as that which your BIG-IP is deployed in.

8. In the :guilabel:`RESERVED PUBLIC IP` box, you can select a previously created `Reserved Public IP` or choose `Create a New Reserved Public IP`.

9. Once this `Private IP Address` is created, copy the `ocid` for use in the next steps.

You can now create the object (Floating Self-IP, SNAT, or Virtual Server) on the BIG-IP cluster and sync the configuration between.

Once the object is created on the BIG-IP, you can now customize the scripts which 'move' the failover objects to the `Active` device in an HA cluster.

9. Download the following files from https://github.com/snowblind-/BIG-IP-OCI-HA-Failover

.. code-block:: console

    active
    oci-curl
    vnicext2.json
    vnicint2.json

10. Copy them to the `/config/failover` directory on both BIG-IP devices on which you previously set up a cluster.

11. In our `Virtual Server` example, we are going to configure it to be attached to our `ext2` VNIC.

12. In the file `vnicext2`, replace the section with the example `ocid` with your own.

.. code-block:: json

    {
      "vnicId" : "ocid1.vnic.oc1.phx.abyhREPLACETHISWITHYOUROCIDShs5mzua7a"
    }

13. In the file `active`, replace the `iaas.us-phoenix-1.oraclecloud.com` with the appropriate OCI API endpoint for the `Region` you are deployed in.

    Replace the `ocid` with the `Private IP ocid` copied when you created the Private IP Address earlier. You can delete the second command as it is not necessary in this example.

14. In the file `oci-curl`, replace the `tenancyID`, `authUserId`, `keyFingerprint`, and `privateKeyPath` with your values.

15. Ensure the `oci-curl` and `active` files are set to the `execute` (+x) permissions.

16. Test the failover. On the `Active` BIG-IP in the cluster, run the following command.

.. code-block:: console

    tmsh run sys failover standby
