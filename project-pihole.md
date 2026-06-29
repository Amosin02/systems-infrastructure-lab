# Building Project-Pihole

I wanted to deploy a network-wide ad and tracker blocker using a Raspberry Pi. Despite the strict ISP barriers, I quickly learned that ***Pi-hole is remarkably easy to install and use—making it hands-down the absolute best project to start home labbing.***

To get around the router's limitations, I just had to get a little creative with static IP configurations and a DHCP handoffs. Here is exactly how I documented the setup process.

## Step 1: Remote Access via Raspberry Pi Connect

Before diving into network configurations, I needed a seamless way to manage my Raspberry Pi headlessly. I opted to use Raspberry Pi Connect, which allows secure, browser-based screen sharing and terminal access without messing around with local SSH configurations right off the bat.

Once the initial connection utility was running, I was securely logged into the Pi and ready to modify the core network layer.

<img width="1438" height="212" alt="image" src="https://github.com/user-attachments/assets/65015ae3-8c06-4065-9dcf-6063f8313aa6" />


## Step 2: Shifting from DHCP to a Static IP

A Pi-hole acts as the DNS "traffic controller" for your entire home network. For this to work reliably, the Raspberry Pi must have a permanent, unchanging IP address. If the router reassigns a new dynamic IP to the Pi via DHCP after a reboot, the entire network loses internet access.

Before running any commands, I noted down three crucial details from my network environment:
  1. The Active Network Interface Name (e.g., "wlan0" or "eth0")

  2. My Desired Static IP

  3. The Router's Gateway IP

I used the following sequence to manually bind the new static configuration (supplied the appropriate values)
#### 1. Assign the static IP address (using standard /24 CIDR subnet notation)
sudo nmcli connection modify "Your Connection Name" ipv4.addresses 192.168.100.200/24

#### 2. Assign the gateway (your router's local address)
sudo nmcli connection modify "Your Connection Name" ipv4.gateway 192.168.100.1

#### 3. Assign the DNS servers (using Google's public DNS and your router as a backup)
sudo nmcli connection modify "Your Connection Name" ipv4.dns "8.8.8.8,192.168.100.1"

#### 4. Switch the configuration profile from dynamic DHCP to manual static assignment
sudo nmcli connection modify "Your Connection Name" ipv4.method manual

#### 5. Restart the interface to apply changes
sudo nmcli connection up "Your Connection Name"

The raspberry Pi now uses the static IP 192.168.100.200

<img width="1438" height="1102" alt="image" src="https://github.com/user-attachments/assets/9ec18a77-9945-49dd-896a-752fd2a07d76" />


## Step 3: Outsmarting Converge Router Restrictions (The DHCP Handoff)

This is where the real problem solving happened. Normally, to deploy Pi-hole, you simply log into your router's admin panel and change the primary DNS IP to point to your Raspberry Pi. However, due to Converge’s strict firmware restrictions, modifying the DNS settings directly on their router is blocked.

#### The Solution? 
Turn off the router’s DHCP server entirely and let Pi-hole handle the entire network's IP distribution.
1. Preparing Pi-hole to take over

Inside the Pi-hole admin dashboard, I navigated to Settings > DHCP. I enabled the DHCP server functionality, then carefully configured the essential settings:

    Address Pool: Defined the range of IPs the Pi-hole is allowed to hand out to devices (keeping them within the 192.168.100.X subnet).

    Default Gateway: Pointed back to the router (192.168.100.1).

2. Disabling the ISP DHCP Server

With the Pi-hole primed and ready to act as the new network anchor, I logged into the Converge admin dashboard. I navigated to the LAN/DHCP settings and disabled the DHCP server.

#### The Result
By disabling DHCP on the router, the Pi-hole automatically took over assigning local IP addresses to every phone, laptop, and smart device in the house. Because the Pi-hole distributes itself as the default DNS server alongside those IP assignments, every piece of network traffic now flows through its ad-blocking filters automatically.

<img width="2498" height="1394" alt="image" src="https://github.com/user-attachments/assets/660a119e-89ca-4aa3-96f3-1d70335874a9" />
