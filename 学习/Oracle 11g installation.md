#oracle

```
# cat /etc/os-release
NAME="Oracle Linux Server"
VERSION="7.9"
ID="ol"
ID_LIKE="fedora"
VARIANT="Server"
VARIANT_ID="server"
VERSION_ID="7.9"
PRETTY_NAME="Oracle Linux Server 7.9"
ANSI_COLOR="0;31"
CPE_NAME="cpe:/o:oracle:linux:7:9:server"
HOME_URL="https://linux.oracle.com/"
BUG_REPORT_URL="https://github.com/oracle/oracle-linux"

ORACLE_BUGZILLA_PRODUCT="Oracle Linux 7"
ORACLE_BUGZILLA_PRODUCT_VERSION=7.9
ORACLE_SUPPORT_PRODUCT="Oracle Linux"
ORACLE_SUPPORT_PRODUCT_VERSION=7.9
```

关闭 SELinux

```
systemctl disable --now firewalld
```

```bash
groupadd oinstall
groupadd dba
useradd -g oinstall -G dba oracle
passwd oracle
id oracle
```

```
Oracle Grid Infrastructure - Installation Notes
Patch 19404309
Note: It is presumed that the user has already reviewed the Oracle Grid Infrastructure Installation Guide and associated Release Notes; instructions and/or recommendations from those documents will not be repeated here.



After downloading the Oracle Grid Infrastructure software, and before attempting any installation, download Patch 19404309 from My Oracle Support, and apply the patch using the instructions in the patch README.



Patch 18370031
Download Patch 18370031 from My Oracle Support. Then, start an interactive Oracle Grid Infrastructure installation through the Oracle Universal Installer (OUI), but do not execute root.sh on any node until afterthe application of Patch 18370031. When the OUI prompts the user to execute the root.sh scripts*, Patch 18370031 should be applied by following the instructions in Section 2.3, Case 5 - Patching a Software Only GI Home Installation or Before the GI Home Is Configured - of the patch README. Note: The README should be reviewed in full, as it contains other requirements (e.g. upgrading OPatch, etc.).

* If executing a software-only installation, the patch should be applied after the installation concludes, but before any configuration is attempted.

Once Patch 18370031 has been applied, proceed with the remainder of the installation (or configuration).





Oracle Database/RAC - Installation Notes
Note: As the title suggests, this section applies both to installations of Oracle Database and Oracle Real Application Clusters (RAC).



Patch 19404309
Note: It is presumed that the user has already reviewed the Oracle Database, Oracle RAC Installation Guides and associated Release Notes; instructions and/or recommendations from those documents will not be repeated here.



After downloading the Oracle Database/RAC software, and before attempting any installation, download Patch 19404309 from My Oracle Support, and apply the patch using the instructions in the patch README.



Patch 19692824
During installation of Oracle Database or Oracle RAC on OL7, the following linking error may be encountered:

Error in invoking target 'agent nmhs' of makefile '<ORACLE_HOME>/sysman/lib/ins_emagent.mk'. See '<installation log>' for details.
If this error is encountered, the user should select Continue. Then, after the installation has completed, the user must download Patch 19692824 from My Oracle Support and apply it per the instructions included in the patch README.
```
