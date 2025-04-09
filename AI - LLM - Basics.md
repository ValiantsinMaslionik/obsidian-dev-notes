#ai/llm

---

## What is LLM

![[zIMG - AI - LLM - what is LLM.png]]
Llama-2-70b by Meta

- parameters - contains weights of the underlying neuro network (70 billion parameter model)
- run.c - contains code to run neuro network

Each weight is represented by 16bit float number, so whole model size is 140GB.

These two files are enough to run NN, no 30rd party dependencies or internet connection is required.
Just need to compile c file, run it, point to *parameters*, and you can start talking with the NN.

## Where the magic is

The magic is hidden behind the parameters file. It was generated during training NN against real data.

![[zIMG - AI - LLM - training.png]]

## What NN actually does

![[zIMG - AI - LLM - what NN does.png]]

In this case training is very similar to lossy compression test data from the Internet.

![[zIMG - AI - LLM - how doest NN work.png]]