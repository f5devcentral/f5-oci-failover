# BIG-IP-OCI-HA-Failover
Scripts to use for HA failover

Should be put in /config/failover

You need to create a unique user for failover similar to a system account. I do not recommend using your existing credentials. This is also useful for auditing and tracking failover events vs. user configuration changes. The user will also need to have a public/private key pair. Directions for creating one for use with OCI can be found here:

https://docs.us-phoenix-1.oraclecloud.com/Content/API/Concepts/apisigningkey.htm#How


The user/group must have the minimum policy:
--------------------------------------------

allow group myHAgroup to use private-ips in compartment myCompartment

allow group myHAgroup to use public-ips in compartment myCompartment

allow group myHAgroup to use vnics in compartment myCompartment


------------------------------------------------------------------------------------------------
Another important note is clock skew. Within OCI requests must be within 5min.

Deploying the BIG-IP should always include an ntp configuration especially for HA, and the OCI network provides an ntp server at the 169.254.169.254 IP address. Additionally you could use pool.ntp.org. But the locally provided IP is recommended and will likely be closely in sync with the API infrastructure and local.

------------------------------------------------------------------------------------------------
