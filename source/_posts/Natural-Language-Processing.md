title: Natural Language Processing（1）
author: Meredith Ma
date: 2019-01-13 17:32:04
tags:
---
# Introduction

##### 1.  What is Natural Language Processing (NLP) ?
- <font color="red">Machine Translation</font>: e.g., Google Translation from Arabic
- <font color="red">Information Extraction</font>: Map a document collection to structured database
    - Complex searches 
    - Statistical queries
- <font color="red">Text Sumarization</font>
- <font color="red">Dialogue Systems</font>
- **Basic NLP Problems :**
    - Tagging : Strings to Tagged Sequences
    - Parsing : Input to parse tree
    
##### 2.  Why NLP is hard ?
	** Ambiguity at Many Levels **
    - at the acoustic level (speech recongnition)
    - at the syntactic level
    - at the semantic (meaning) level
    - at the discourse (multi-clause) level:
        - Alice says they've built a  computer that understands you like your mother. But she...<br>the "she" is obvious with Alice or mom.
        
##### 3.  What will this course be about ?
- Language modeling, smoothed estimation
- Tagging, hidden Markov models
- Statistical parsing
- Machine translation
- Log-linear models, discriminative methods
- Semi-supervised and unsupervised learning for NLP

# The Language Modeling Problems
- We have a training sample for example sentences in English
- We need to "learn" a probability distribution p i.e., p is a function that satisfies
$$\sum_{x\epsilon \nu^{\dotplus }}p(x) = 1, p(x) \geq 0 for all x \epsilon \nu^{\dotplus }$$

		p(the STOP) = 10e-12
		p(the fan STOP) = 10e-8
		p(the fan saw Beckham STOP) = 2 * 10e-8
		p(the fan saw saw STOP) = 10e-15
		...
		p(the fan saw Beckham play for Real Madrid STOP) = 2*10e-9
		...
   
# Trigram Language Model
##### 1.  Markov Processes
- Consider a sequence of random variables X1,X2,...,Xn.
	Each random variable can take any value in finite set V.
   For now we assume the length n is fixed(e.g.,n=100).
- Our gaol:model
<center>**P(X1=x1, X2=x2,..., Xn=xn)**</center>

##### 2. First-Order Markov Processes
	first of all, we have:
<center>**P(X1=x1, X2=x2,..., Xn=xn):<br>=P(X1=x1) PIE P(Xi=xi|X1=x1,...,Xi-1=xi-1)**</center>

	now the first-order Markov assumption:
    for any i $\epsilon$ {2...n},for any x1,...,xi
<center>**P(Xi=xi|X1=x1,...,Xi-1=xi-1) = P(Xi=xi|Xi-1=xi-1)**</center>

	then we have:    
<center>**P(X1=x1, X2=x2,..., Xn=xn):<br>=P(X1=x1)*P(X2=x2|X1=x1)*PIE P(Xi=xi|Xi-1=xi-1)**</center>

##### 3. Second-Order Markov Processes
	first of all, we have:
<center>**P(X1=x1, X2=x2,..., Xn=xn):<br>=P(X1=x1) PIE P(Xi=xi|X1=x1,...,Xi-1=xi-1)**</center>

	now the second-order Markov assumption:
    for any i $\epsilon$ {2...n},for any x1,...,xi
<center>**P(Xi=xi|X1=x1,...,Xi-1=xi-1) = P(Xi=xi|Xi-2=xi-2, Xi-1=xi-1)**</center>

	then we have:    
<center>**P(X1=x1, X2=x2,..., Xn=xn):<br>=P(X1=x1)*P(X2=x2|X1=x1)*PIE P(Xi=xi|Xi-2=xi-2, Xi-1=xi-1)**</center>

##### 4. Trigram Language Models
- A trigram language model consist of:
	1. A finite set V
    2. A parameter q(w|u,v) for each trigram u,v,w such that w epsilon V|{\*}.
- For any sentence x1,...,xn where xi epsilon V for i=1...(n-1), and xn=STOP, the probability of the sentence under the trigram language model is 
<center>**P(x1,x2,...,xn)=PIE q(xi|xi-2,xi-1)<br>where we define x0=x-1=\* **</center>

# Proplexity
![avatar](/images/nlp1.png)
![avatar](/images/nlp2.png)
![avatar](/images/nlp3.png)