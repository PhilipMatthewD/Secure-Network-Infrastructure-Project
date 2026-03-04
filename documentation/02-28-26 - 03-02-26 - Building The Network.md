### 02-28-26 Building The Network

* This phase focused on building the actual network infrastructure using Hyper-V, pfSense, and Ubuntu Server. Phase 1 consists of setting up Hyper-V, deploying pfSense as the firewall, and configuring Ubuntu as the internal server.

* I began by creating two virtual switches inside Hyper-V:

  * An **External switch** named *Wan-Switch*
  * An **Internal switch** named *Lan-Switch*

  These switches allow pfSense to simulate WAN and LAN segmentation within the lab environment.

* I created a new virtual machine named **pfSense Firewall**. Most settings remained default, but I adjusted the memory from 4096MB to 2048MB and set the disk size to 20GB. I selected the pfSense ISO from Netgate and completed the VM creation.

* To properly configure pfSense, I added the **Lan-Switch** as a second network adapter and disabled Secure Boot so the system could boot correctly. During installation:

  * `hn0` was assigned as WAN
  * `hn1` was assigned as LAN

* I configured the LAN interface with the IP address **10.0.0.1/24** to segment the lab from my home network.
  The DHCP IPv4 range was manually set to **10.0.0.10 – 10.0.0.100** to avoid conflicts between internal systems.

* On the host machine’s Network Connections panel, I manually configured the LAN adapter with:

  * IP: 10.0.0.2
  * Subnet Mask: 255.255.255.0
  * Default Gateway: 10.0.0.1
  * DNS: 10.0.0.1
  * Alternate DNS: 8.8.8.8

  After this configuration, navigating to **[http://10.0.0.1](http://10.0.0.1)** successfully loaded the pfSense dashboard, where I immediately changed the default credentials and verified that the LAN remained static at 10.0.0.1/24.

* I confirmed outbound connectivity by pinging 8.8.8.8, which returned 0% packet loss. This validated that pfSense was routing traffic correctly and that NAT was functioning as expected.

* Next, I deployed **Ubuntu-Internal-Server** as a new virtual machine connected to the Lan-Switch. During my previous lab attempt, I mistakenly tried integrating Windows into pfSense directly, so this time I ensured each system had its own properly configured VM.

* I disabled Secure Boot for Ubuntu, set the hostname to *ubuntu-internal-server*, and installed OpenSSH for future remote management. After logging in, I updated the system using:

  * `sudo apt update && sudo apt upgrade -y`

* I installed Apache using:

  * `sudo apt install apache2 -y`

  I verified the assigned IP address using `ip a`, then successfully accessed the Apache default page at **[http://10.0.0.10/](http://10.0.0.10/)**.

* To validate internal hosting functionality, I created a custom `index.html` file using Notepad++ that displayed an image named **Salmiakki.png** centered on the page. Although I could have created the file directly in Ubuntu, I chose SCP to practice secure file transfers.

* I transferred both `index.html` and `Salmiakki.png` from my host machine to the Ubuntu VM using SCP, then moved them into `/var/www/html/` and restarted Apache.

* After restarting Apache, the webpage rendered correctly with the image displayed. This confirmed:

  * Internal segmentation is functioning
  * DHCP configuration is correct
  * Apache is serving content properly
  * SSH file transfer is operational

If you want next, we can standardize this format so every phase looks consistent and professional ac
