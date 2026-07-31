---
title: "CTF Writeup: 2023 Cyber Defense Challenge - Cryptography & Steganography"
layout: post
---
Curious what a cybersecurity Capture the Flag competition is really like? Follow along as I break down several beginner-friendly cryptography and steganography challenges, explaining how I approached each one and what I learned along the way.


This past month, I participated in my first ever CTF: the 2023 Target Cyber Defense Challenge, offered for [WiCyS](https://www.wicys.org/) members. It was extremely valuable in that it gave me hands-on experience and a taste as to what it’s like to be on a cyber defense team up against the threat actor “Shiny Scorpion”. It also constantly challenged me to think differently and to learn about areas of cyber that I wasn’t familiar with from a technical standpoint.

While I was unable to complete the entire CTF, I’m excited to share that I ended up placing 68th out of 476 competitors, and completed 10 challenges.

The challenges were spread across the categories: Cryptography/ Steganography, Reverse Engineering, USB Forensics, and Cyber Threat Intelligence.

The following is a write-up of the Cryptography/ Steganography challenges that I solved!

---

# Before We Begin

**💡 What is Cryptography?**
Cryptography is the practice of securing information by transforming it into a format that can only be understood with the correct key or method. In CTFs, you will often encounter encrypted messages that require identifying the cipher before you can decode them.

**💡 What is Steganography?**
Steganography hides and scrambles information inside another file, such as an image, so the message isn't obvious to someone viewing it. It's common for secret messages to be embedded inside an image, audio file, or document.

---

# Beware the Ides of March (100 pts)

Problem:

You have intercepted the following message from the threat actor! Decrypt the message to reveal the secret phrase:
```GUR GNETRG UNF ORRA NPDHVERQ```

Solution:
To decrypt the message, I turned to dCode site, which has a variety of tools to help with cryptography and decrypting ciphers. I started with the Caesar cipher, as it’s a well-known cipher. After plugging the phrase in, the flag was shown:
```FLAG: THE TARGET HAS BEEN ACQUIRED```

---

# Now You See Me Now You Don’t (200 pts)

Problem:

You have intercepted an email sent between Bob and Alice, who we believe to be members of Shiny Scorpion. See a transcription of the message below:

```Hi Bob,

Our good friend Vigenère is looking to follow up on the financial reports you're working on. 
Can you give me a timeline on when you think they would be ready to share out?

Best, Alice
---

There doesn’t look to be anything malicious in the email, but the embedded photo in the email hit the automated threat detection. Can you find the true message of the email?
```

I ran **zsteg**, a tool that shows hidden data in images, which resulted in the string:
```KaierljsipvgbediecsvhrrscaEvvqyq ```

The clue mentioned Vigenère in the email. That suggested the hidden message was encrypted with a Vigenère cipher, so that became my next step.

I used that as input for the [CyberChef](https://gchq.github.io/CyberChef/) Vigenère decoding tool. While a key wasn’t mentioned previously, and after a lot of trial and error, I used the knowledge that both Alice and Bob are Shiny Scorpion members and applied that as the key.

FLAG: Start the ransomware attack on Monday

---

# Follow the Dotted Line (200 pts)

Problem:

The following message was found in a packet capture file originating from a device that is suspected to be associated with the Shiny Scorpion malware group. Can you find the hidden message?

```
.--.- --... --.-- -...- -.-.- ----- -..-- -.---

Labor Day sale!

You won't want to miss this! We're putting on our biggest Labor Day sale yet. Mark your calendars to bring home the bacon with these unbelievable savings!

.---. -.--- -..-- -...- -.-.- ----- ---.- --.--
```

Solution:

I spent a lot of time trying to solve this, thinking the dots and dashes were Morse code (spoiler: it’s not Morse code). Instead, I turned my sights towards binary, which consists of 0/1. So for each “.” I turned that into a 0, and each “-” into a 1.

After taking the original text and manually converting to binary, I was left with this:
```
01101 11000 11011 10001 10101 11111 10011 10111 01110 10111 10011 10001 10101 11111 11101 11011
```

With this binary set, I turned to CyberChef again and plugged this into the input. Looking back at the email and taking a closer look at the wording, the word bacon stood out as it was highlighted in red. This led to me applying a Bacon Cipher decoding/ encoding tool. Once that was plugged in properly, it was decoded to reveal the FLAG: THEPLANISINPLACE.

---

# A Particular Exchange (300 pts)

Problem:

The below email has been intercepted, and Threat Intelligence believes that the proof of the infiltration of Shiny Scorpion into the organization can be extracted somewhere in this email. Can you find the shared information?

```
Hi Bob,

As you may have heard from Director Diffie-Hellman, there is going to be a party to support inter-team communication. Can you arrange to have nine orders of papaya salad, seven orders of the grape pastries, six dozen apples, and eight loaves of banana bread?

The shared information is of the upmost importance.

Thanks,
Alice
```

Solution:

After reading the email, I looked up the director’s name to find that it’s related to a key exchange of the same name. Diffie-Hellman was used to safely develop and exchange keys over insecure channels. With D-H key exchange, both parties end up with the same result, without needing to send the entirety of the common secret across a communication channel.

I found a [Diffie-Hellman Key Exchange](https://www.dcode.fr/diffie-hellman-key-exchange) calculator and plugged in the numbers mentioned in the email’s party food orders:
- 9 papaya salad
- 7 grape pastries
- 6 dozen apples
- 8 loaves of banana bread

This resulted in the calculator returning the value and the FLAG: 1.