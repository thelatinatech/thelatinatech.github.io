---
title: "TryHackMe Writeup: tshark"
layout: post
---
Learn how to analyze packet captures from the command line by solving the TShark TryHackMe room step by step.


---
# Overview
The TShark room on TryHackMe is a great introduction to analyzing network traffic from the command line. If you're familiar with Wireshark, think of TShark as its command-line counterpart—it lets you inspect, filter, and analyze packet captures without ever opening a GUI.

---
# Task 1: Getting Started

Before beginning, verify that TShark is installed.
`apt list tshark`
If it isn't installed, install it with:
`sudo apt install tshark`
You can also view the available command-line options by running:
`tshark -h`

# Task 2: Reading PCAP Files
Download the provided dns.cap file and navigate to its directory.

Before answering any questions, I like to inspect the capture to get a feel for what's inside.
`tshark -r dns.cap`

## Helpful Commands
Throughout this room, you'll mainly use a small set of TShark options:

|  Command  |                Purpose               |
|:---------:|:------------------------------------:|
| -r        | Read packets from a capture file     |
| -Y        | Apply a display filter               |
| -T fields | Display only specific fields         |
| -e        | Specify which field to output        |
| wc -l     | Count the number of matching packets |
These few options are enough to solve most of the room.

---

## Question 1
**How many packets are in the capture?**
Since TShark prints one packet per line, we can simply count the output.
`tshark -r dns.cap | wc -l`
### Why wc -l?
The wc command counts lines. Because each packet is displayed on its own line, counting lines gives us the packet count.

## Question 2
**How many A records are in the capture?**
This time we only care about DNS A records, so we filter the capture before counting.
`tshark -r dns.cap -Y "dns.qry.type == 1" | wc -l`
Instead of searching through every packet manually, the display filter limits the output to only matching packets.

## Question 3
**Which A record appears the most?**
Now we don't need the entire packet—we only need the queried domain names.
`tshark -r dns.cap -Y "dns.qry.type == 1" -T fields -e dns.qry.name`
The output lists each queried domain, making it easy to determine which appears most frequently.

# Task 3: DNS Exfiltration
Download **dnsexfil.pcap** and begin by answering the packet count question the same way as before.
`tshark -r dnsexfil.pcap | wc -l`
Because we've already learned this pattern, the rest of the task becomes much easier.

## Finding Only DNS Queries
One question asks for the number of queries, not responses.
The room even provides a helpful hint:
`dns.flags.response == 0`
Applying that filter gives us only DNS requests.
`tshark -r dnsexfil.pcap -Y "dns.flags.response == 0" | wc -l`

## Finding the Transaction ID
The next task asks for the suspicious DNS transaction ID.

At first, this confused me because I wasn't familiar with the available DNS field names.

Instead of trying to guess, I displayed only the DNS query packets and inspected the output until I found the dns.id field.

Sometimes the quickest solution isn't another command, it's slowing down and examining the data.

## Extracting the Hidden String
The final challenge is where things got interesting.
Running
`tshark -r dnsexfil.pcap -Y "dns.flags.response == 0" -T fields -e dns.qry.name`
returns a long list of DNS queries.

Each query contains part of the hidden message as a subdomain.

I copied the results into VS Code and used Find & Replace to remove the repeated domain name, leaving only the encoded fragments.

From there, I wrote a short Python script to concatenate everything into a single string.
``` python
with open ('dnsexfil_string.txt','r') as file:
  letters = [line.strip() for line in file]

# Combine the letters into a string
combined_string = ''.join(letters)

# Output the combined string
print(combined_string) #Output: abcd

```
One mistake that cost me several minutes was accidentally filtering with:
`dns.flags.response == 1`
instead of
`dns.flags.response == 0`

If your decoded string doesn't make sense, double-check your display filter first.

## Decoding the Flag
The room hints that the resulting string is encoded using **Base32**.
After combining the fragments, I pasted the string into [CyberChef](gchq.github.io/CyberChef/) and applied From Base32, revealing the final flag.

---

# Beginner Takeaways
If you're new to packet analysis, here are the biggest lessons this room reinforced for me:
- **Break large problems into smaller questions.** Every command answered one question, which naturally led to the next.
- **Display filters are your best friend.** Instead of scrolling through thousands of packets, filter the data down to only what matters.
- **Learn a few commands well.** Most of this room can be solved using just -r, -Y, -T fields, -e, and wc -l.
- **Inspect your data before assuming.** Looking closely at packet fields often reveals the next clue.
- **Don't be afraid to use multiple tools.** TShark, VS Code, Python, and CyberChef each solved a different part of the challenge.

---

# Helpful Resources
If you'd like to continue learning TShark beyond this room, these are excellent references:
[Wireshark — TShark Documentation](https://www.wireshark.org/docs/man-pages/tshark.html)
[HackerTarget — TShark Tutorial & Filter Examples](https://hackertarget.com/tshark-tutorial-and-filter-examples/)
[LinuxHint — A Guide to TShark](https://linuxhint.com/wireshark-command-line-interface-tshark/)

---

## Final Thoughts
I enjoyed this room because it reinforced that packet analysis isn't about memorizing commands—it's about asking the right questions and using the available tools to narrow down the answers. 

If you're just getting started with TShark, I'd recommend trying to solve each question on your own before looking at the walkthrough. You'll learn much more by following the clues yourself.

If you found this guide helpful, you might also enjoy my other beginner-friendly walkthroughs covering SQL, Python, cybersecurity, and automation. I'm documenting what I learn as I continue growing in tech, and I hope these notes help make your learning journey a little easier.
