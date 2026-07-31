---
title: "CTF Writeup: 2023 Cyber Defense Challenge - CTI & RE"
layout: post
---
Capture the Flag competitions can seem intimidating, especially if you're just getting started in cybersecurity. This walkthrough shows how I approached my first CTF, solved several CTI and Reverse Engineering challenges, and learned new skills along the way.


This past month, I participated in my first ever CTF: the 2023 Target Cyber Defense Challenge, offered for WiCyS members. It was extremely valuable in that it gave me hands-on experience and a taste as to what it’s like to be on a cyber defense team up against the threat actor “Shiny Scorpion”. It also constantly challenged me to think differently and to learn about areas of cyber that I wasn’t familiar with from a technical standpoint.

While I was unable to complete the entire CTF, I’m excited to share that I ended up placing 68th out of 476 competitors, and completed 10 challenges.

The challenges were spread across the categories: Cryptography/ Steganography, Reverse Engineering, USB Forensics, and Cyber Threat Intelligence.

The following is a write-up of the Cyber Threat Intelligence and Reverse Engineering challenges that I solved!

---
# Cyber Threat Intelligence

## WHOIS responsible for this IP address? (100 pts)

Problem:

The incident response team has identified an IP address that several infected hosts have been communicating with: 165.227.251.183
As part of the investigation, you’ve been tasked with identifying the company that owns this IP address.

Note: The flag is not case/whitespace sensitive.

Solution: FLAG: DigitalOcean, LLC

To begin, I opened up my terminal and ran “whois 165.227.251.183”. This returned a lot of information, as seen in the screenshot below. Since the ask was to identify the company that owns the IP address, I searched the results and found the company listed after “Organization:”.

```
Last login: Fri Jun 16 00:05:48 on ttys000
TheLatinaTech@TheLatinaTech-MBP ~ % whois 165.227.251.183
% IANA WHOIS server
% for more information on IANA, visit <http://www.iana.org>
% This query returned 1 object

refer:        whois.arin.net

inetnum:      165.0.0.0 - 165.255.255.255
organisation: Administered by ARIN
status:       LEGACY

whois:        whois.arin.net

changed:      1993-05
source:       IANA

# whois.arin.net

NetRange:       165.227.0.0 - 165.227.255.255
CIDR:           165.227.0.0/16
NetName:        DIGITALOCEAN-165-227-0-0
NetHandle:      NET-165-227-0-0-1
Parent:         NET165 (NET-165-0-0-0-0)
NetType:        Direct Allocation
OriginAS:       AS14061
Organization:   DigitalOcean, LLC (DO-13)
RegDate:        2016-10-06
Updated:        2020-04-03
Comment:        Routing and Peering Policy can be found at <https://www.as14061.net>
Comment:
Comment:        Please submit abuse reports at <https://www.digitalocean.com/company/contact/#abuse>
Ref:            <https://rdap.arin.net/registry/ip/165.227.0.0>

OrgName:        DigitalOcean, LLC
OrgId:          DO-13
Address:        101 Ave of the Americas
Address:        FL2
City:           New York
StateProv:      NY
PostalCode:     10013
Country:        US
RegDate:        2012-05-14
Updated:        2022-05-19
Ref:            <https://rdap.arin.net/registry/entity/DO-13>

OrgNOCHandle: NOC32014-ARIN
OrgNOCName:   Network Operations Center
OrgNOCPhone:  +1-347-875-6044
OrgNOCEmail:  noc@digitalocean.com
OrgNOCRef:    <https://rdap.arin.net/registry/entity/NOC32014-ARIN>

OrgTechHandle: NOC32014-ARIN
OrgTechName:   Network Operations Center
OrgTechPhone:  +1-347-875-6044
OrgTechEmail:  noc@digitalocean.com
OrgTechRef:    <https://rdap.arin.net/registry/entity/NOC32014-ARIN>

OrgAbuseHandle: ABUSE5232-ARIN
OrgAbuseName:   Abuse, DigitalOcean
OrgAbusePhone:  +1-347-875-6044
OrgAbuseEmail:  abuse@digitalocean.com
OrgAbuseRef:    <https://rdap.arin.net/registry/entity/ABUSE5232-ARIN>
```

---
## Don’t sweat the MITRE technique (100 pts)

Problem:

The incident response team has identified a suspicious command being executed on several infected hosts: nltest /domain_trusts /all_trusts
To help determine what the adversary is up to, you’ve been asked to identify the MITRE ATT&CK technique ID associated with this activity.
Note: The flag is not case sensitive

Solution: FLAG: T1482

This flag was found pretty quickly (thanks to our bestie Google). I searched MITRE ATT&CK framework and once on the site dug around a bit to find a technique that matched the given information from the problem. Then found the ID on the page for Domain Trust Discovery.

---

# Reverse Engineering

Due to limited time constraints and my technical knowledge of C, I was only able to solve one RE challenge:

## r04c4 (100 points)

Problem:

It seems I skipped RE101. I may need to brush up on my C programming before analyzing the code.
flag format: flag{the_is_an_example_flag}
(The following is the contents of the challenge’s attached file).

```
#include <stdio.h>
#include <string.h>

void d(unsigned char *x,unsigned char *y,unsigned char *z) {
    unsigned char s[0400];
    int i,j;

    for (i=0;i<0400;i++) {
        s[i]=i;
    }

    for (i=0,j=0;i<0400;i++) {
        j=(j+x[i%05]+s[i])%0400;
        unsigned char t=s[i];
        s[i]=s[j];
        s[j]=t;
    }

    i=j=0;
    for (int x=0;x<026;x++) {
        i=(i+1)%0400;
        j=(j+s[i])%0400;
        unsigned char t=s[i];
        s[i]=s[j];
        s[j]=t;
        unsigned char rnd=s[(s[i]+s[j])%0400];
        z[x]=y[x]^rnd;
    }
}

int main() {
    unsigned char k[]="\162\x30\164\x63\64";
    unsigned char c[]={0x54,0206,0xdb,0242,0xd7,0151,0x38,0114,0x59,0235,0xd9,0340,0xeb,0100,0x84,0365,0xbd,0237,0x39,0143,0xa3,0243};

    unsigned char p[026];
    memset(p,0,sizeof(p));

    d();

    for (int i=0;i<026;i++) {
        printf("%c",p[i]);
    }
    printf("\n");

    return 0;
}
```

Solution: FLAG:{1_l0v3_rc4}

As someone who doesn’t have a programming background (yet!), I plugged the attached C file into ChatGPT to get an understanding of what I was reading. Then I used https://tio.run/#c-gcc to run the attached C code and the output was:
```  MzkuM3fkK2jjqwAspzZ0sD ```

I added this output to Cyberchef and recognized the format of the string as Base64. I applied the “From Base64” filter, and was given the correct flag.