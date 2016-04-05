---
title: How not to TDD
id: 26
categories:
  - Uncategorized
date: 2008-08-15 12:22:00
tags:
---

I recently sat in on a teleconference with some developers of a testing and automation framework who were trying to get us to use their product. One of the big components is a tool that you write test specifications with. There are two outputs: A long, formatted testing specification PDF and an XML file that specifies your tests. You then pass the XML file through a translator that outputs NUnit test stubs for your to fill in.   

I was curious... I had to ask &quot;Why would I want to do this?&quot; Their answer was two fold:   
1\. Because the PDF is used for quality tracking   
2\. Because some developers like to write their tests before they write their software. It's called &quot;Test Driven Development&quot;   

I was rather shocked by this answer so I just shut up and listened to the rest of the pitch. I really wanted to respond, but it wasn't professional in this particular venue. What I would have said may have gone something like this:   

No No No No No!!!!! That is NOT Test Driven Development. That does not help you track quality.   

Test Driven Development is something that I have been practicing rather religiously for about a year now. I have reaped the many benefits of TDD and I can say with confidence that what they were describing was NOT TDD.   

TDD is a tool that developers use to design software. You write a SINGLE test, you make the test pass, and then you write another test, refactoring your code as you go such that the software lives for the tests. As you refactor your software, you are also refactoring your tests. As your software evolves, so do the tests. The tests, by themselves, is your quality tracking document. Your IDE is your design/specification tool. I really don't need to complicate my life with these other tools.   

If you have ever developed using TDD, imagine writing all of your tests up-front in a tool external from your IDE. Then, you pass the output of that tool through another tool to generate your tests. THEN, you open your IDE and fill in the test stubss. When it is time to write new tests, open the external tool again, pass the output through another tool, and fill in the new tests.   

One of the greatest feelings I get while coding is when I am in the TDD groove -- where I am going from Red to Green to Red to Green to Red to Green... etc etc. My software is evolving in a way that is very natural for my brain. The thought alone of breaking that groove with extra tools for the purpose of a PDF document that NOBODY will ever read stresses me out.   

I will pass on this particular workflow, thank you.   