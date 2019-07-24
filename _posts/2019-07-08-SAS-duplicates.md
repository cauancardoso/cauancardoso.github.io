---
layout: post
title: How to count and remove duplicates using SAS
subtitle: Counting and removing duplicates of Unemployment Insurance data
gh-repo: cauancardoso/cauancardoso.github.io
gh-badge: [star, fork, follow]
tags: [SAS]
comments: true
---

[There are plenty of ways to remove duplicates using SAS](https://support.sas.com/resources/papers/proceedings17/0188-2017.pdf). Here I show how I've done it when I was working with the Unemployment Insurance administrative registers of the Ministry of Economy in Brazil.

First things first, I used [SAS](https://www.sas.com/en_us/home.html) version 9.3 to this project.

**SAS version:** 9.3  
{: .box-note}

### Create a token variable
The first step is to create a token variable equals to 1.

```javascript
data your_data;
	set your_data;
	token = 1;
run;
```

### Count duplicates
The second step is to count the number of duplicate records in your data. The way I do this is using ```PROC MEANS``` to sum the token variable when a duplicate occurs.

```javascript
PROC MEANS data = your_data  noprint;
	class column1 column2 column3 ... columnN /missing; /* use all the columns you want to check for duplicate records */
	types column1*column2*column3*...*columnN; /* repeat the columns above here, but separated with an asterisk instead of a single space */
	output out= new_data (drop=_type_ _freq_)
	sum(token) = repetition;  /*  Sum the number of records with the same key  */ 
run;
```
Then, you could either create a table with only duplicate records or a table without duplicate records.

### Delete non-duplicates
If you want to analyse the duplicate records, you could work on a new table with duplicate records only. Here I will show one way to achieve this.

```javascript
DATA duplicate_records;
   set new_data;
   if repetition = 1 then delete;
;
```
### Delete duplicates
Maybe the fastest way to delete your duplicate records from your data is using ```PROC SQL```.

```javascript
PROC SQL; 
  delete from work.new_data /* your new_data will be generated in the work folder if not told otherwise */
  where repetition ge 2; 
quit;
```
