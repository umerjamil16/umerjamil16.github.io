---

title: "Containerizing Kaldi - the speech recognition toolkit"
date: 2022-11-30T11:00:00+05:00
tags: ["Kaldi","DevOps", "ASR", "Speech Recognition", "Docker", "Operations"]
author: "Umer Jamil"

---

![kaldi image](https://miro.medium.com/max/945/0*qbI15In_UIOZXQ-A)

Kaldi is a very popular Automatic Speech Recognition (ASR) toolkit. In my experience, setting up Kaldi on a system can take huge amount of time. Therefore, it is best to use containerization approach.

Following is the Dockerfile that I wrote. We will be using a cuda enabled Ubuntu 18.04 image from Nvidia NGC:
