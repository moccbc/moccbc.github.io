---
author: yuta
title: Designing Data Intensive Applications - Day 1
date: 2025-11-12 -0800
categories: [SystemDesign, Ddia]
tags: [dayone, systemdesign]
---

Read until page 32

## Overview
I think DDIA's first chapter is very boring as a whole. I couldn't seem to grasp the overall direction that the first chapter is trying to go in, which makes me lose sight of the why behind reading this chapter. Then because I lose track of why I'm reading this chapter, the information just doesn't seem to stick. I have tried to read this book many times and I just keep getting thwarted by the first chapter because I can just never get through it. I ended up just reading the last summary section and I personally feel that that gave me a better understading of what the purpose of the chapter was.

I understand that it is there to give an overview of what a "Data Intensive Application" is supposed to do, but I do feel that each of these smaller sections would have been better discussed towards the end. This way, you get an idea of the idea of what a "Data Intensive Application" is from the ground up, then it would end with a discussion of what it is trying to achieve.

Chapter one was basically giving an overview of what a "Data Intensive Application" is supposed to do. In essense, these kinds of applications are trying to achieve reliability, scalability, and maintainability. Reliability measures if the system is well protected against faults, scalability measures if the system can handle high load, and maintainability measures if the system can be changed easily by the maintainers.

The discussions around faults in the reliability section was pretty interesting. Three different kinds of faults / errors were discussed: hardware, software, and human. The hardware fault discussion reminds me of Google's BigTable because I believe it was designed around using cheaper hardware (that I assume cause more faults). The reason I found this interesting was because as a software engineer, I usually think about software issues, so expanding my view to hardware issues was an eye opener. I write software for a living after all, and I interact the most with it. However, hardware is abstracted away and the most I think about it on a day to day basis is when I need to think about the compute that my software will run on. The discussion around human errors was also interesting because this is something I think about often when I write software. This was something that I felt that I was validated on when I read it in this book.

The other parts of scalability and maintainability were a little boring because they really seem to be out of context. Why would I think describing load is interesting when I don't even know what kind of load is big? The book then just describes an interesting glimpse of what Twitter does before it tells you that it'll go into a deeper (which I read as more interesing) in even later chapters. Part of me thought that if it was just going to defer me to later chapters, then we don't even need this at all.

Anyways, to conclude, the entirety of chapter one can just be replaced by the summary of it.

Chapter two on the other hand, does get interesting. It is where we get into actual technology and how it can be used. Namely, it discusses different data modelling techniques. I only read up to page 32 today, where I started to understand the discussion about the relational model vs the document model.

The first point that I found interesting was how it described data modelling. The book says that it affects not only how the software is written, but also how we think about the problem that we are solving. This was interesting to me because this begs the question; can we understand how other people think about software by observing how they model the data? I don't have an answer to this yet, but this is a question that I may come back to from time to time. The way that I understood it, data modelling is about creating abstraction layers so that people can use the things that create that data model. For example, return values of APIs should hide how complicated it is to actually generate that data.

The next point that I found interesting was how the book described what a "relational model" was. It is essentially a model to organize data into models, where each model has a list of unordered tuples. These in SQL would be a table (model) with rows (a list of unordered tuples). Creating relations between these models will naturally lead to a graph like structure, which now makes sense as to why graph based models are now a thing.

## Notes
### Chapter 1 
- About what we want to achieve with "Data Intensive Applications". We are trying to achieve 3 things: reliability, scalability, and maintainability. Then it talks about how to think of these three topics, and then ends off with how we can achieve these three things.
- Thought chapter 1 wasn't really technically interesting so I skipped it.

### Chapter 2
#### Introduction
- Data modelling is important because it provides an interface for connecting two layers of abstractions. It does so by providing an abstraction to hide away the complexity of the layer below. 
   - For example, when you create an API response, the caller of the API does not need to understand how the information is calculated. They just need to understand what the value of the return fields of the API are and what they represent. In this way, the API's return value's data model is a layer of abstraction for that the caller can understand, that hides away the complexities of the code that calculates the API's return value's data.
   - In a similar way, you can also think of a programming language's types as a method of data modelling. It is a layer of abstraction that models what the programmer can use and the complexity of the transformation to that into bytecode is hidden by the complier or interpreter.
- Even moreso though, data modelling is important because it has a profound effect on how the software will be written, but also on how we as developers will think about the problem that we are solving.
  - If we take the example of software languages as data modelling, then this makes sense. Different programming languages lead to different ways of thinking.
  - This may be why there are always long discussions around data modelling. People have different ways of thinking, and they express it through data modelling. There might be something interesting here about getting glimpses into how someone thinks based on how they model data.
- This chapter will discuss on the different general purpose data modelling options. For example, it will look at the relational model, the document model, and a few graph based models. Then it will look at a few query languages and their use cases.

## Questions
- What is "Data Modelling"? What does it actually mean?
- Can you also just not represent the relational model with a graph of a list of lists?
  - The relational model is where you organize data into models (called tables in SQL) where each model is an unordered list of tuples (called rows in SQL).
- Is Dynamo DB a non-relational database?
  - Initial answer would be yes. It seems like it is [not a relational database](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/SQLtoNoSQL.WhyDynamoDB.html).

