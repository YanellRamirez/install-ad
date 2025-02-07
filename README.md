<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>Installing Active Directory in Azure</h1>
This lab demonstrates the steps I took to install Active Directory using Azure. I will be using this as the foundation for future labs. In this lab, I will be working with two VMs on Azure that are on the same VNet. However, this particular lab will focus on just one of the VMs, which will be configured as the domain controller with Active Directory. The second VM will act as a client and will be joined to the domain in a future lab.

This setup provides a solid foundation for learning how to implement and manage Active Directory in an Azure environment. <br />

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 Pro (21H2)

<h2>Installation Steps</h2>

<p>
<img src="https://i.imgur.com/fBRzEZ3.png" height="80%" width="80%" alt="Installation Steps"/>
</p>
<p>
Before using the VMs, it’s important to set the domain controller VM’s IP address to static. By default, the VMs may not be able to communicate with each other if both are assigned dynamic IPs, even though they are on the same VNet. If this change is not made, the client VM will not be able to join the domain that will be created later.

Here’s how to set the static IP for the domain controller VM:

- Go to the Azure portal and navigate to your domain controller VM.
- In the VM's settings, click on the Networking tab.
- Under the Networking section, click on the Network Interface associated with the domain controller VM.
- In the Network Interface settings, open the IP configurations tab.
- Toggle the Assignment switch from Dynamic to Static.
- Save your changes.

By setting the domain controller VM’s IP to static, we ensure it has a fixed reference address, which is crucial for proper communication and configuration, especially when the client VM joins the domain later.
</p>
<br />

<p>
<img src="https://i.imgur.com/hE1XNtg.png" height="80%" width="80%" alt="Installation Steps"/>
<img src="https://i.imgur.com/hlDtnbY.png" height="80%" width="80%" alt="Installation Steps"/>
<img src="https://i.imgur.com/IbIJWiC.png" height="80%" width="80%" alt="Installation Steps"/>
</p>
<p>
After setting the static IP on the domain controller VM, the next step is to check the connectivity from the client VM to the domain controller.

- Log in to the client VM.
- Open CMD or PowerShell and use the following command to ping the domain controller’s private IP address: ping -t <domain_controller_private_ip>
(You should see that the connection is timing out, indicating that there’s no response.)

To resolve this, follow these steps on the domain controller VM:

- On the domain controller VM, press Windows Key + R, type wf.msc, and hit Enter. This will open the Windows Defender Firewall settings.
- In the Windows Defender Firewall window, click on Inbound Rules on the left side.
- Scroll down to find the rule called Core Networking Diagnostics - ICMP Echo Request. These rules handle ICMP traffic, which is needed for the ping to work.
- Right-click on the rule and select Enable Rule. Make sure both the Domain, Private, and Public profiles are enabled as needed.

Once the ICMP Echo Request rule is enabled, return to the client VM and try the ping command again.

You should now see that the ping is successful, indicating that the client VM can communicate with the domain controller VM. This confirms that the connectivity issue has been resolved.
</p>
<br />

<p>
<img src="https://i.imgur.com/ktSoNmA.png" height="80%" width="80%" alt="Installation Steps"/>
</p>
<p>
Now that we have ensured connectivity between the client VM and the domain controller, it’s time to install Active Directory on the domain controller VM.

Follow these steps to install and configure Active Directory:

1.) Open Server Manager on the domain controller VM.
2.) In Server Manager, click on Add Roles and Features.
3.) Click Next until you reach the Select features screen.
4.) On the Select server roles tab, scroll down and check Active Directory Domain Services.
(A prompt will appear asking you to add additional features. Click Add Features.)
5.) Click Next until you reach the Install button and then click Install.

After the installation completes, you will need to promote the server to a domain controller:

6.) Once the installation is complete, you'll notice a warning icon in the top right corner under the flag in Server Manager.
7.) Click the flag and then click Promote this server to a domain controller.

Next, you'll configure the domain controller:

8.) Choose Add a new forest (since this is the first domain controller) and enter your domain name. For this lab, I will use ernestotest.com.
9.) Specify a Directory Services Restore Mode (DSRM) password. This password is used for directory services recovery.
10.) Click Next on the following screens to accept the default settings for the domain functional level, NetBIOS name, and other configuration options.
11.) Once you've reviewed the settings, click Install.

The server will then begin the promotion process. After the installation and promotion are complete, the server will automatically restart.

When the domain controller VM reboots, Active Directory will be installed, and your domain controller will be fully functional with the domain you specified.
</p>
<br />

<h2>An Important Note </h2>

Once the domain controller VM has been promoted and rebooted, you will need to log back into the domain controller VM using the domain context. Here’s how:

- Open Remote Desktop Connection on your local machine.
- When prompted for your login credentials, you will need to enter the domain path followed by the username. The format for this is: <domain_name>\<username>

For example, if your domain is ernestotest.com and your username is labuser, you will log in as: ernestotest.com\labuser

- Enter the corresponding password for the labuser account.
- Click OK to log in.

Now that Active Directory is installed and the domain controller is functional, you can begin implementing configurations and manage the domain in future labs. Also, the client VM you created earlier will be able to join the domain you've set up in this lab, allowing you to manage both machines as part of the domain in future steps.












