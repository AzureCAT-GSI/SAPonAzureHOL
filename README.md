# Overview 

This is a hands-on-lab to create a windows server, install SAPGui and run some useful transactions against a live SAP system.

## Create a resource group in west us 2

Use the azure portal to create a resource group to hold your windows server.

## Create a windows server 2016 VM 

Create a windows server 2016 virtual machine in your new resource group, allow RDP port access, in west us 2 region.  You can use the defaults for the vnet creation.

## RDP into the machine you created

Use the Connect button to download the RDP file for your machine, and connect into it.  Use the credentials you specified when you created the machine:

![connect to machine](media/2019-02-12_11-33-46.png)

## Turn off IE enhanced security 

When you are successfully logged into your VM, open the server manager and turn off the IE enhanced security:

![turn off IE security](media/2019-02-12_11-39-31.png)

Choose "off" for admins and users, and click "OK"

![settings](media/2019-02-12_11-40-48.png)

## Install putty SSH client

Open the browser and download putty from http://www.putty.org

![download putty](media/2019-02-12_11-42-50.png)

Run to install putty, and leave all defaults.

## Install sapgui

SAPGUI is the thick client application used to access SAP.  First, download it from our storage account by pasting the following URL in the browser, and saving the downloaded file as 50148746_6.zip:  https://sapbitswestus2.blob.core.windows.net/saphol/50148746_6.ZIP?st=2019-02-12T19%3A51%3A15Z&se=2019-02-28T19%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=PdgOm9JMsw65KOT29tFzxH0BYAqQ%2BqtGm9N43JCNoVs%3D

Use the file explorer to extract the zip file in the Download direectory.

To install SAPGUI, run 50148746_6\BD_NW_7.0_Presentation_7.50_Comp._2_\PRES1\GUI\WINDOWS\Win32\SapGuiSetup.exe

This will start the installer and give you this screen where you will click "Next":

![sapgui1](media/2019-02-12_12-09-37.png)

In the left hand list, click the + next to "SAP GUI for Windows 7.50".  Check that and "SAP GUI Suite" only, and click "Next":

![sapgui2](media/2019-02-12_12-11-22.png)

For the target directory, leave the default and choose "Next":

![sapgui3](media/2019-02-12_12-12-32.png)

The software install will take a few minutes:

![sapgui4](media/2019-02-12_12-14-27.png)

## Putty to our linux jumpbox with proper port forwarding

Since the machine you just created is not on the same vnet as our SAP system, we will use an SSH tunnel to access our Linux jumpbox.

Start Putty.  In the putty configuration window, enter 51.141.162.171.  This is the address of our linux jumpbox.  In the category pane on the left, scroll down to Connection/SSH and click Tunnels:

![sapgui4](media/2019-02-12_18-05-47.png)

in the right pane, type **3204** in the source port box, **10.0.1.30:3204** in the destination box:

![sapgui4](media/2019-02-12_18-07-59.png)

then click the **Add button**, which should add the entry in the "forwarded ports" section:

![sapgui4](media/2019-02-12_18-09-06.png)

On the left, scroll up and click **Session**.  on the right, give your session a name in the **Saved Sessions** box, and click **Save**:

![sapgui4](media/2019-02-12_18-10-17.png)

To connect, click the **Open** button:

![sapgui4](media/2019-02-12_18-11-34.png)

Then logon with the credentials supplied.  When you are logged on, this will also tunnel port 3204 to the SAP application server we will use.

## Start sapgui

Double click on the SAP Logon icon on the desktop:

![sapgui4](media/2019-02-12_18-19-04.png)

When the SAP logon window opens up, right click on the **connections** tab on the left, and choose **Add New Entry**

![sapgui4](media/2019-02-12_18-20-39.png)

In the **Create New System Entry** dialog box, choose **User Specified System** and click Next:

![sapgui4](media/2019-02-12_18-22-31.png)

In the dialog box, enter a description, and in the Application Server field type **localhost**.  This will use the SSH tunnel that you set up earlier.  For the Instance number type **04**, and the System ID should be **S4A**.  Click **Finish**:

![sapgui4](media/2019-02-12_18-24-21.png)

You should now see the connection you just created under "Connections".  Double click on it.

![sapgui4](media/2019-02-12_18-27-26.png)

You should then see the SAP logon screen.  Enter the credentials supplied and hit the "enter" key to login.

![sapgui4](media/2019-02-12_18-28-37.png)

If you see the **SAP Easy Access** screen, this will indicate a successful login.

![sapgui4](media/2019-02-12_18-30-04.png)

To run a specific transaction, you can enter "/n" and the transaction code in the dropdown list box in the upper left hand corner, for example:

![transaction id](media/2019-02-12_18-32-17.png)

## SAP Transactions

### Workload Monitor

Let's enter the "st03" transaction by typing "/nst03" and hitting enter.  This should take us to the **Workload Monitor** transaction. 

![sapgui4](media/2019-02-12_18-31-23.png)

### Tune Summary

Now try out the **ST02** transaction:

![st02 transaction](media/2019-02-12_18-36-12.png)

### DBACockpit

The **dbacockpit** transaction is quite useful:

![sapgui4](media/2019-02-12_18-38-28.png)

### Analysis & Service Tools

The **ST13** transaction is very useful for identifying issues in the SAP deployment.  When you run the transaction you get the following screen:

![sapgui4](media/2019-02-12_18-39-23.png)

Click the squares button just to the left of the execute button.  This will bring up a list of available choices.  Double click on **PERF_TOOL**: 

![sapgui4](media/2019-02-12_18-40-00.png)

SAPGUI should fill in **PERF_TOOL** in the tool name field.  Click the **Execute** button:

![sapgui4](media/2019-02-12_18-40-29.png)

in the **/SSA/CAT** screen that comes up, choose **ABAPMETER** for the **Select Performance Tool** field:

![sapgui4](media/2019-02-12_18-41-11.png)

Click **Execute** in the bottom right to run the abapmeter tool.

![sapgui4](media/2019-02-12_18-41-41.png)

This will run various performance tests on the SAP system, and give output such as:

![sapgui4](media/2019-02-12_18-42-20.png)

### User Sessions

Try out the **AL08** transaction, which shows the users logged in to the system

![sapgui4](media/2019-02-12_18-47-42.png)

### System Snapshot

The **ST06** transaction gives a view of performance statistics of the system:

![sapgui4](media/2019-02-12_18-59-10.png)

## User Maintenance

The **SU01** transaction allows administrators to maintain the users of the system:

![sapgui4](media/2019-02-12_19-02-58.png)

## Database Maintenance

The **DB02** transaction is for database administration via the SAP GUI:

![sapgui4](media/2019-02-15_11-33-45.png)



