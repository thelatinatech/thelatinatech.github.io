---
title: "CTF Writeup: 2023 Cyber Defense Challenge - USB Forensics"
layout: post
---
From identifying USB devices to recovering hidden files inside packet captures, these USB forensics challenges were some of my favorite problems from my first cybersecurity CTF. Here's how I approached each one and what I learned along the way.


This past month, I participated in my first ever CTF: the 2023 Target Cyber Defense Challenge, offered for WiCyS members. It was extremely valuable in that it gave me hands-on experience and a taste as to what it’s like to be on a cyber defense team up against the threat actor “Shiny Scorpion”. It also constantly challenged me to think differently and to learn about areas of cyber that I wasn’t familiar with from a technical standpoint.

While I was unable to complete the entire CTF, I’m excited to share that I ended up placing 68th out of 476 competitors, and completed 10 challenges.

The challenges were spread across the categories: Cryptography/ Steganography, Reverse Engineering, USB Forensics, and Cyber Threat Intelligence.

The following is a write-up of the USB Forensics challenges that I solved!

---
# Find the Blue Yeti (100 pts)
Problem:
We believe that while one of the ransomware operators was out in public, they dropped this USB device. While plugging it in to make an image of it, we noticed that it had multiple auto run features. So, we booted up Wireshark and made some PCAPs of the device traffic.

To get you used to the layout of searching a PCAP for device information, we will start with you finding the Blue Yeti.

Flag Format: (VendorId)(ProductId)

I went to the first GET DESCRIPTOR Response DEVICE packet and plugged in the idVendor, and idProduct. At first, I hadn’t realized there were different vendors and products.

![yeti1](/assets/images/usb-forensics/yeti1.png)

Did a search at the top of the Wireshark ribbon to narrow my search to anything containing the string “Blue”. Narrowed to 2 results, one of which was Blue microphones and ended up being the flag.

![yeti2](/assets/images/usb-forensics/yeti2.png)

Solution: FLAG: (0xb58e)(0x0005)

---
# Know Your Filters! (100 pts)
Problem:

To help further you further in the next steps, you need to figure out what the filter type is when trying to view a Mass Storage Device in WireShark.

Solution: Flag: usbms

This was a pretty straightforward challenge in that I searched for Wireshark filter types to view Mass Storage Devices, which directed me to this site on [usbms](https://www.wireshark.org/docs/dfref/u/usbms.html).

---
# Moving Files (200 pts)

Problem:

There is a file that transfers when the device is connected to the analysis Virtual Machine. Filter down on the data to figure out what the file is.
Flag Format : {Word1_Word2_…Wordn}

I began by reviewing the attached PCAP file using Wireshark. Everything on the file was listed as USB or USBMS protocols. From the Know Your Filters! challenge, it suggested to use USBMS as a filter. Then I looked at packets that were larger, since there was data being transferred.

Because the data was being sent out from the host, I applied the filter: usb.src == “host”). I had gotten a hint that the only packets I would need to look at have a packet length of 42001. Therefore, I added the filter: frame.len ≥ 42001.

Then I began the manual search for files that contained “JFIF”, which is an image file format. As I came across “JFIF” in certain packet bytes details, I saved them locally as documents and used my Mac’s search bar to open the files.

I ended up with 4 documents named 1053, 885, 1011, and 927. When I opened Packet 885’s document, it revealed the following image with the flag:

![yeti3](/assets/images/usb-forensics/yeti3.png)

Solution: FLAG: N0t_An0ther_N3twork_Pcap
