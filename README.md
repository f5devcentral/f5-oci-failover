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

This tool is not supported by F5 support. If you find an issue, we would love to hear about it. Please let us know by filling an issue in this repository. Tell us as much as you can about what you found, how you found it, your environment, etc.. We also welcome you to file feature requests as issues.

f5-oci-failover is released to the community under the Apache v2 license. It is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
