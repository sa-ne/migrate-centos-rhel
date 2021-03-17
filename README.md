# Migrate from CentOS to Red Hat Enterprise Linux using convert2rhel and Satellite

  

## Here are Instructions on How To:

- Convert a CentOS system to RHEL 
- Register the system to Red Hat Satellite

## Back up your system and verify that you are able to restore it
- Don't skip this step - rollbacks after errors don't always work as expected
## If the CentOS server was deployed using Satellite:
```
# subscription-manager remove --all
# subscription-manager unregister
# subscription-manager clean
```
## Determine if subscription manager packages are installed:
```
# rpm -qa | grep -i subscription
```
## If installed, remove the packages listed in the output of the previous step:
```
# yum erase python3-subscription-manager-rhsm-1.27.16-1.el8.x86_64 \
subscription-manager-rhsm-certificates-1.27.16-1.el8.x86_64 \
subscription-manager-1.27.16-1.el8.x86_64 \
dnf-plugin-subscription-manager-1.27.16-1.el8.x86_64
```
## Install the convert2rhel utility (Note - el8.noarch.rpm is for CentOS 8 systems. Use el7.noarch.rpm for CentOS 7 systems):
```
# yum install -y https://github.com/oamg/convert2rhel/releases/download/v0.17/convert2rhel-0.17-1.el8.noarch.rpm
```
## Grab the following file from the Satellite server and place in /usr/share/convert2rhel/subscription-manager/ on the system to be converted (replacing sat01.example.org with the FQDN of yous Satellite server):
```
# curl --insecure --output /usr/share/convert2rhel/subscription-manager/katello-ca-consumer-latest.noarch.rpm \
https://sat01.example.org/pub/katello-ca-consumer-latest.noarch.rpm
```
## Clean yum cache:
```
# yum clean all
```
## Retrieve the activation key for the appropriate RHEL release from the Satellite server (substituting in your organization label):
```
# hammer activation-key list --organization-label "example_org"
```
## Run convert2rhel utility:
```
# convert2rhel -k AK-RHEL_8_SOE_Development -o example_org -a --debug
```
-   -k uses the activation key retrieved in the previous step
-   -o is your Satellite organization name
-   -a will auto attach a subscription
-   --debug will help with troubleshooting
## Reboot and verify that the system was converted to RHEL:
```
# cat /etc/redhat-release 
Red Hat Enterprise Linux release 8.3 (Ootpa)
```

> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTAzMzcyODAyXX0=
-->
