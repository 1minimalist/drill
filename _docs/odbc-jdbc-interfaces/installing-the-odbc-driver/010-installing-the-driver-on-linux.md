---
title: "Installing the Driver on Linux"
parent: "Installing the ODBC Driver"
---
Install the MapR Drill ODBC Driver on the machine from which you connect to
the Drill service. You can install the 32- or 64-bit driver on Linux. Install
the version of the driver that matches the architecture of the client
application that you use to access Drill. The 64-bit editions of Linux support
32- and 64-bit applications.

Install the MapR Drill ODBC Driver on a system that meets the [system requirements]({{site.baseurl}}/docs/installing-the-driver-on-linux/#system-requirements), and then complete the following steps described in detail in this document:

  * [Step 1: Download the MapR Drill ODBC Driver]({{site.baseurl}}/docs/installing-the-driver-on-linux/#step-1:-download-the-mapr-drill-odbc-driver) 
  * [Step 2: Install the MapR Drill ODBC Driver]({{site.baseurl}}/docs/installing-the-driver-on-linux/#step-2:-install-the-mapr-drill-odbc-driver)
  * [Step 3: Check the MapR Drill ODBC Driver version]({{site.baseurl}}/docs/installing-the-driver-on-linux/#step-3:-check-the-mapr-drill-odbc-driver-version)

Verify that your system meets the system requirements before you start.

## System Requirements

  * One of the following distributions (32- and 64-bit editions are supported):
    * Red Hat® Enterprise Linux® (RHEL) 5.0/6.0
    * CentOS 5.0/6.0
    * SUSE Linux Enterprise Server (SLES) 11
  * 90 MB of available disk space.
  * An installed ODBC driver manager:
    * iODBC 3.52.7 or above  
      OR 
    * unixODBC 2.2.12 or above  
    On Linux, 3.52.7 is available as a tarball. After unpacking the tarball, see the README for instructions about building the driver manager.
  * The client must be able to resolve the actual host name of the Drill node or nodes from the IP address. Verify that a DNS entry was created on the client machine for the Drill node or nodes.   
If not, create an entry in `/etc/hosts` for each node in the following format:  

    `<drill-machine-IP> <drill-machine-hostname>`  
    Example: `127.0.0.1 localhost`

To install the driver, you need Administrator privileges on the computer.

## Step 1: Download the MapR Drill ODBC Driver
Download either the 32- or 64-bit driver from the following sites:

  * [MapR Drill ODBC Driver (32-bit)]

    http://package.mapr.com/tools/MapR-ODBC/MapR_Drill/MapRDrill_odbc_v1.0.0.1001/MapRDrillODBC-32bit-1.0.0.i686.rpm
  * [MapR Drill ODBC Driver (64-bit)]

    http://package.mapr.com/tools/MapR-ODBC/MapR_Drill/MapRDrill_odbc_v1.0.0.1001/MapRDrillODBC-1.0.0.x86_64.rpm

## Step 2: Install the MapR Drill ODBC Driver

To install the driver, complete the following steps:

  1. Login as the root user.
  2. Navigate to the folder that contains the driver RPM packages to install.
  3. Enter the following command where _RPMFileName_ is the file name of the RPM package containing the version of the driver that you want to install:  
     * RedHat/CentOS  
     `yum localinstall --nogpgcheck <RPMFileName>`
     * SUSE  
     `zypper install RPMFileName`

The MapR Drill ODBC Driver depends on the following resources:

  * `cyrus-sasl-2.1.22-7` or above
  * `cyrus-sasl-gssapi-2.1.22-7` or above
  * `cyrus-sasl-plain-2.1.22-7` or above

The MapR Drill ODBC Driver typically resolves dependencies automatically. If not, error messages during the installation indicate that the package manager in your Linux distribution cannot resolve the
dependencies automatically. In this case, manually install the packages.

The following table provides a list of the MapR Drill ODBC Driver file
locations and descriptions:

File| Description  
---|---  
`/opt/mapr/drillodbc/ErrorMessages `| Error messages files directory.  
`/opt/mapr/drillodbc/Setup`| Sample configuration files directory.  
`/opt/mapr/drillodbc/lib/32 `| 32-bit shared libraries directory.  
`/opt/mapr/drillodbc/lib/64`| 64-bit shared libraries directory.  
  
## Step 3: Check the MapR Drill ODBC Driver version

To check the version of the driver you installed, use the following case-sensitive command on the terminal command line:

`yum list | grep MapRDrillODBC`

or

`rpm -qa | grep MapRDrillodbc`


### Next Step

[Configuring ODBC on Linux]({{ site.baseurl }}/docs/configuring-odbc-on-linux/).

