---
title: Burp Suite Certified Practitioner in 2025 -  Reflections and Lessons Learned
description: 
showTableOfContents: true
tags:
  - Burp
  - Suite
  - Certification
  - Cybersecurity
  - Pentesting
type: post
draft: false
date: 2025-01-22T10:00:00+01:00
---

## A Bit About Me

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
  - [Intigriti](https://www.youtube.com/c/Intigriti)
  - [Jarno Timmermans](https://www.youtube.com/c/JarnoTimmermans)
- **PortSwigger Research Articles**: These were particularly helpful for understanding concepts like request smuggling. Check them out [here](https://portswigger.net/research/articles).
- **Botesjuan Repository**: This is a fantastic resource, especially for learning how to exploit vulnerabilities like XSS to steal cookies rather than just triggering alerts in the browser. You can find it [here](https://github.com/botesjuan/Burp-Suite-Certified-Practitioner-Exam-Study).
- **Botesjuan YouTube Playlist**: This playlist is a great companion resource, with shoutouts to [z3nsh3ll](https://www.youtube.com/c/z3nsh3ll) and [John Hammond](https://www.youtube.com/c/JohnHammond010).
- **My Own Site**: I created a site that mirrors the Botesjuan repository but offers better navigation and includes a section with all payloads. You can visit it [here](https://oscp-7.gitbook.io/bscp-notes).

### Study Methods

My study approach was straightforward: I completed all topics and labs except for the expert ones. After that, I revisited and retook topics where I felt less confident, utilizing all the materials mentioned above—sometimes reading through them multiple times. I also tackled several mystery labs and completed a practice exam, which I highly recommend to familiarize yourself with the exam format.

Following this, I revisited more mystery labs. You can follow the proposed methodology outlined on the PortSwigger website: 

> "Work through the topics within the academy, completing every apprentice and practitioner-level lab as you go. As you reach the end of each topic, use the mystery labs feature to practice solving the labs with no contextual clues. When you've completed all the practitioner-level labs, practice solving mystery labs from all available topics to develop your recon and discovery skills. Then complete a practice exam to familiarize yourself with the exam format. Make sure to read the exam hints and tips, as they contain invaluable information that you'll need to be successful in the exam."

From my perspective, if a topic seems challenging, revisit it after taking the practice exam and work on it with mystery labs again.

### Time Commitment

I began my preparation in May and passed the exam in January. Throughout this period, I was working full-time and had various personal commitments, including , brithdays, travels and holidays. Therefore, my timeline may not be representative of what others might experience. In hindsight, I believe that 5-6 months is a reasonable timeframe for preparation. However, if you are organized and have more time to dedicate, you could potentially complete it in 3-4 months. Keep in mind that everyone's experience will vary.


## Exam Experience

So the exam are two APP, websites very similar to those in labs. 

        - Describe the exam format (e.g., number of questions, time limit, question types).
        - Discuss the types of challenges you encountered (e.g., technical difficulties, time constraints, unexpected question types).
    - **Exam Tips:**
        - Share any specific tips or strategies that helped you succeed on the exam (e.g., time management, question elimination, reading questions carefully).
        - Discuss any common pitfalls to avoid.
- **Reflections and Lessons Learned**
    - Share your thoughts and feelings after passing the exam.
    - Discuss the value of the certification and its impact on your career.
    - Offer advice to others who are preparing for the exam.
- **Conclusion**
    - Summarize your key takeaways.
    - Reiterate the importance of dedication, perseverance, and effective study habits.
    - Offer a final encouraging message to aspiring practitioners.

**3. Writing Tips**

- **Be Personal and Engaging:** Share your own experiences, anecdotes, and emotions.
- **Use Clear and Concise Language:** Avoid jargon whenever possible.
- **Provide Specific Examples:** Illustrate your points with concrete examples from your study and exam experience.
- **Focus on Practical Advice:** Provide actionable tips and strategies that other candidates can implement in their own preparation.
- **Proofread Carefully:** Ensure your article is free of any grammatical or spelling errors.

**4. Optional Additions**

- **Include screenshots or visuals:** To illustrate concepts or your study process.
- **Create a Q&A section:** To answer common questions about the exam.
- **Link to relevant resources:** For readers who want to learn more.