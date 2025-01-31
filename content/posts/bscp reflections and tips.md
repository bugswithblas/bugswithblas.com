---
title: "How I Became a Burp Suite Certified Practitioner in 2025: Reflections and Tips"
description: "Discover my journey to becoming a Burp Suite Certified Practitioner in 2025, including lessons learned, challenges faced, and tips for success in cybersecurity."
showTableOfContents: true
tags:
  - "Burp Suite"
  - BSCP
  - Certification
  - Cybersecurity
  - Pentesting
type: post
draft: false
date: 2025-01-22T10:00:00+01:00
---

## Introduction

Did you know that the demand for certified cybersecurity professionals is expected to grow by 35% by 2025? Here’s how I navigated the challenges of becoming a Burp Suite Certified Practitioner and what I learned along the way.

### A Bit About Me

To provide some context, let me share my background. I have nearly seven years of experience as an IT administrator, where I managed servers, networks, internal websites, and IT infrastructure. This role has given me a solid understanding of how applications and websites function, from the hardware level through servers and firewalls, all the way to the end user. These fundamentals have allowed me to grasp how web applications operate "in the background."

Throughout my education and career, I've also dabbled in coding, working on websites, Android apps, and scripts for internal use. While I wouldn't label myself a programmer, I consider myself a capable code reader. I believe these experiences have provided me with a strong foundation to venture into penetration testing.

### Why This Certification?

At a certain point in my career, I felt the need for change. The "programming train" wasn't appealing to me, and I found it challenging to build a portfolio that would lead to a well-paying job. Inspired by one of my favorite shows, *Mr. Robot*, I decided to take a leap and explore something new. After conducting a brief market reconnaissance, I set my sights on becoming a penetration tester.

In 2023, I began preparing for the OSCP certification but faced setbacks and ultimately didn't pass. However, I decided to take a step back and focus on mastering Burp Suite. But enough about my journey—let's dive into the Burp Suite Exam.

### Outline of the Article

In this article, I want to share my experience of passing the Burp Suite Exam. I will provide tips, tricks, and resources that I found helpful along the way, as well as my feelings about the entire process. Whether you're considering this certification or just curious about the world of penetration testing, I hope my insights will be valuable to you.


## Preparation

Before diving into the Burp Suite Exam, there are a few essential things you should know. For a comprehensive list of requirements, check out the official [PortSwigger certification page](https://portswigger.net/web-security/certification/how-it-works#requirements).

One crucial detail I initially overlooked was the need for a license for the Pro version of Burp Suite. While most of the requirements are relatively low, you will need to budget $449.00 for the Pro version if you don't already have access to it, plus an additional $99 for the exam. If your current job provides access to Burp Suite, you can use that license. According to PortSwigger:

> "If you have a Burp Suite Professional license, but it is registered under an email domain of the company you work for rather than your personal email address, you will still be absolutely fine, from a technical perspective, to use that license for taking the exam. So long as you have access to a valid, active Burp Suite Professional license at the time of your certification, you will be able to use it to take the exam."

If you have any concerns about using a company-registered license, it's best to discuss this with your employer, as they can provide guidance.

### Materials

Here are the resources I personally utilized during my preparation:

- **PortSwigger Topics and Labs**: I covered all topics and labs except for the expert level. You can find them [here](https://portswigger.net/web-security/all-topics).
- **YouTube Community Solutions**: I found valuable insights from channels like:
  - [Intigriti](https://www.youtube.com/@intigriti)
  - [Jarno Timmermans](https://www.youtube.com/@netletic)
- **PortSwigger Research Articles**: These were particularly helpful for understanding concepts like request smuggling. Check them out [here](https://portswigger.net/research/articles).
- **Botesjuan Repository**: This is a fantastic resource, especially for learning how to exploit vulnerabilities like XSS to steal cookies rather than just triggering alerts in the browser. You can find it [here](https://github.com/botesjuan/Burp-Suite-Certified-Practitioner-Exam-Study).
- **Botesjuan YouTube Playlist**: This [playlist](https://youtube.com/playlist?list=PLsDxQTEdg_YkVMP6PybE7I-hAdhR7adem&si=1UtLwY27vcw5FGT-) is a great companion resource, with shoutouts to [z3nsh3ll](https://www.youtube.com/c/z3nsh3ll) and [John Hammond](https://www.youtube.com/c/JohnHammond010).
- **My Own Site**: I created a site that mirrors the Botesjuan repository but offers better navigation and includes a section with all payloads. You can visit it [here](https://oscp-7.gitbook.io/bscp-notes).

### Study Methods

#### Initial Preparation  
My study approach was straightforward: I completed all topics and labs except for the expert ones. After that, I revisited and retook topics where I felt less confident, utilizing all the materials mentioned above—sometimes reading through them multiple times. I also tackled several mystery labs and completed a practice exam, which I highly recommend to familiarize yourself with the exam format.

#### PortSwigger Methodology  
Following this, I revisited more mystery labs. You can follow the proposed methodology outlined on the PortSwigger website:  

> "Work through the topics within the academy, completing every apprentice and practitioner-level lab as you go. As you reach the end of each topic, use the mystery labs feature to practice solving the labs with no contextual clues. When you've completed all the practitioner-level labs, practice solving mystery labs from all available topics to develop your recon and discovery skills. Then complete a practice exam to familiarize yourself with the exam format. Make sure to read the exam hints and tips, as they contain invaluable information that you'll need to be successful in the exam."

#### Overcoming Challenges  
From my perspective, if a topic seems challenging, revisit it after taking the practice exam and work on it with mystery labs again. This approach helps solidify your understanding and builds confidence in tackling difficult areas.

### Time Commitment

I began my preparation in May and passed the exam in January. Throughout this period, I was working full-time and had various personal commitments, including , brithdays, travels and holidays. Therefore, my timeline may not be representative of what others might experience. In hindsight, I believe that 5-6 months is a reasonable timeframe for preparation. However, if you are organized and have more time to dedicate, you could potentially complete it in 3-4 months. Keep in mind that everyone's experience will vary.


## Exam Experience

The exam consists of two applications that closely resemble those found in the labs. Each application involves three main steps:

1. Gain access to a standard user account (the username is likely "carlos").
2. Elevate privileges to the administrator account.
3. Retrieve the contents of the file located at `/home/carlos/secret`.

For more details, you can read about the exam structure [here](https://portswigger.net/web-security/certification/how-it-works#what-the-exam-involves).

The exam duration is four hours.


### My Tips

I passed the exam on my second attempt and discovered various approaches that can be effective. Here are my recommendations:

1. **Start with Active Scanning**: Begin by actively scanning both applications simultaneously. This process may take some time, but it’s worth it. As you explore the websites, generate requests and, if you identify any interesting areas, run a scan for specific insertion points or requests.

2. **Utilize Two Screens**: If possible, set up a dual-screen configuration. Use one larger screen for Burp Suite and the second for your study materials, protocol documentation, and the exam applications. This setup can significantly enhance your workflow.

3. **Be Aware of Obfuscation Techniques**: Remember that the exam may not present obfuscation techniques in the same straightforward manner as the topics covered in the labs. While most labs utilize basic obfuscation, the exam may require you to employ different techniques. Stay alert and be prepared to adapt your strategies accordingly.

4. **SQL Injection Automation**: During my first attempt, I struggled with **SQL injection**. For the second try I used the **CO extension**, which allows running **sqlmap** directly in the system terminal. This was a game-changer because it automated the injection process, freeing up my time to focus on other parts of the exam.  

5. **Extensions and Tools** Stick to the tools and techniques covered in the **PortSwigger Academy topics and labs**. Overcomplicating your approach with unfamiliar tools can waste valuable time. But use some time using it, or use some of newer ones like I used CO for SQL Injection.

## Reflections

Passing the Burp Suite Exam filled me with a profound sense of accomplishment and relief. The journey was challenging, but it ultimately deepened my passion for penetration testing and web security. 

For those preparing for the exam, I want to emphasize that if you find your motivation waning after months or even years due to your current job, remember that achieving such milestones can reignite your drive. If you have doubts, I assure you that the feeling of passing is incredibly rewarding. I can relate to this personally; I created a blog from scratch and wrote this post in just four days. Two months ago, I would have thought that would take me forever.

## Conclusion

In summary, my journey to passing the [Burp Suite Exam](https://portswigger.net/web-security/e/c/690adbbe21505dc3) has been both rewarding and enlightening. The key takeaways from this experience include the importance of thorough preparation, the value of utilizing diverse resources, and the necessity of practicing with real-world scenarios.

Looking ahead, I am eager to deepen my knowledge in the security field. My immediate plan is to gain more experience, hopefully in a new role as a penetration tester, while further developing my skills through CTF platforms and bug bounty programs. I also aspire to attempt the OSCP again and, one day, speak at DEF CON. This professional dream motivates me and provides a boost to my efforts.

Becoming a Burp Suite Certified Practitioner was one of the most challenging yet rewarding experiences of my career. If I can do it, so can you—start your journey today! If you’re considering becoming a Burp Suite Certified Practitioner, I’d love to hear about your journey. For more tips on cybersecurity, penetration testing, CTFs and bug hunting, follow my blog."

 