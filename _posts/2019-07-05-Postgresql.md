---
layout: post
title: How to join three tables in Postgresql
subtitle: Full outer join of RAIS 2014, 2015 and 2016 tables
gh-repo: cauancardoso/cauancardoso.github.io
gh-badge: [star, fork, follow]
tags: [SQL, Postgresql]
comments: true
---

The **Annual Social Information Report (RAIS)** is a socioeconomic report of the formal work in Brazil. It includes monthly information of admissions, dismissals, medical leaves, salaries and demographic information of formal workers. 

The RAIS 2014 has 60 variables and more than 74 million registers; the RAIS 2015 has 76 variables and more than 72 million registers; the RAIS 2016 has 76 variables and more than 67 million registers. Each register represents a job linkage in that year.

Here I will show very briefly how I've managed to join the RAIS 2014, 2015 and 2016 datasets into a single dataset using [PostgreSQL](https://www.postgresql.org/) as a way to create monthly employment entry and exit rates in the 2014-2016 period.

First things first, I used [pgAdmin III](https://www.pgadmin.org/download/) version 1.22.2 to this project.

**pgAdmin III version:** 1.22.2  
{: .box-note}

I did a FULL OUTER JOIN of the RAIS 2014-2016 tables, because I wanted to keep all the three tables registers. However, a JOIN cannot be performed in SQL if a variable from one table has the same column name of a variable from another. The 60 variables from RAIS 2014 table are also present in the RAIS 2015 and 2016 tables, hence the column names are the same. **How to deal with that?**

Information about name and sex of the worker, that (hopefully) will not change over the years, can be taken from just one table. On the other hand, information about employment will change over the years and is the core of the analysis here. In that case, it will suffice change the name of the variables. I chose something that relate them to the year that information is from. So, I did something like this:

For simplification purposes, I'm calling the RAIS 2015 and 2016 variables **v1**, **v2**, **v3**, ... and **v76**
{: .box-note}

```javascript
ALTER TABLE rais_2015
RENAME COLUMN v3 to v3_2015;
ALTER TABLE rais_2015
RENAME COLUMN v4 to v4_2015;
ALTER TABLE rais_2015
RENAME COLUMN v5 to v5_2015;
```
And so on up to ```v76``` in ```rais_2015``` table...
```javascript
ALTER TABLE rais_2016
RENAME COLUMN v3 to v3_2016;
ALTER TABLE rais_2016
RENAME COLUMN v4 to v4_2016;
ALTER TABLE rais_2016
RENAME COLUMN v5 to v5_2016;
```
And so on up to ```v76``` in ```rais_2016``` table.

I used the ```v1``` and ```v2``` variables (which are called **pis** and **cpf**) to match the workers from one table to another. Because these variables exist in all three tables with the same name (and I didn't rename them for reasons I'll explain later), these columns can't be selected in more than one table. So, I selected all the variables of the 2014 table (60 variables) and all the variables **except cpf and pis** from the 2015 and 2016 tables (74 variables each). Also, [I use a subquery](https://www.techonthenet.com/postgresql/subqueries.php) to FULL OUTER JOIN first the RAIS 2014 and 2015 and later 2016. My query looked something like this: 

```javascript
SELECT rais_2014_2015.*,  
       rais_2016.v3_2016,  
       rais_2016.v4_2016,  
       [...],  
       rais_2016.v76_2016  
INTO rais_2014_2016  
FROM (SELECT rais_2014.*,  
	     rais_2015.v3_2015,  
	     rais_2015.v4_2015,  
	     [...],  
	     rais_2015.v76_2015,  
	FULL OUTER JOIN rais_2015  
		USING (v1, v2)  
	) rais_2014_2015  
FULL OUTER JOIN rais_2016  
	USING (v1, v2)  
;
```

The result I get is all the information from RAIS 2014, RAIS 2015 and RAIS 2016 in just one table. But it's not done just yet: when a match between RAIS 2014 table and RAIS 2015 table or RAIS 2016 table, ```v1``` and ```v2``` are *NULL* (because they information come from RAIS 2014 table only). To fix it, I did another update using the original RAIS 2015 and RAIS 2016 tables that looked something like this:

```javascript
UPDATE rais_2014_2016  
SET v1 = rais_2015.v1, v2 = rais_2015.v2  
FROM rais_2015  
WHERE rais_2014_2016.v3_2015 = rais_2015.v3 AND  
      rais_2014_2016.v4_2015 = rais_2015.v4 AND  
      [...] AND  
      rais_2014_2016.v76_2015 = rais_2015.v76  
;  
UPDATE rais_2014_2016  
SET v1 = rais_2016.v1, v2 = rais_2016.v2  
FROM rais_2016  
WHERE rais_2014_2016.v3_2016 = rais_2016.v3 AND  
      rais_2014_2016.v4_2016 = rais_2016.v4 AND  
      [...] AND  
      rais_2014_2016.v76_2016 = rais_2016.v76 AND  
;  
```
Another option to do all this in a single query (with no need to update) is to use a CASE WHEN expression like this: 

```javascript
SELECT CASE WHEN rais_2014_2015.v1 IS NULL THEN rais_2016.v1 ELSE rais_2014_2015.v1 END AS v1,
       CASE WHEN rais_2014_2015.v2 IS NULL THEN rais_2016.v2 ELSE rais_2014_2015.v2 END AS v2,
       rais_2014_2015.v3,  
       rais_2014_2015.v4,  
       [...],  
       rais_2014_2015.v76,  
       rais_2016.v3_2016,  
       rais_2016.v4_2016,  
       [...],  
       rais_2016.v76_2016  
INTO rais_2014_2016  
FROM (SELECT CASE WHEN rais_2014.v1 IS NULL THEN rais_2015.v1 ELSE rais_2014.v1 END AS v1,
	     CASE WHEN rais_2014.v2 IS NULL THEN rais_2015.v2 ELSE rais_2014.v2 END AS v2,
	     rais_2014.v3_2015,  
	     rais_2014.v4_2015,  
	     [...],  
	     rais_2014.v76_2015, 
	     rais_2015.v3_2015,  
	     rais_2015.v4_2015,  
	     [...],  
	     rais_2015.v76_2015,  
	FULL OUTER JOIN rais_2015  
		USING (v1, v2)  
	) rais_2014_2015  
FULL OUTER JOIN rais_2016  
	USING (v1, v2)  
;
```
And it's done!

Now I have a single table for 3 consecutive years and only two columns as id variables (```v1``` and ```v2```) that I can use to merge with others datasets that also have these id variables, e.g. **Cadastro Único**, **Caged**, **Seguro Desemprego** and many others Brazilian administrative registers.

If you want to learn more about the different ways you can JOIN tables in SQL, [see this very instructed document created by Steve Stedman](http://stevestedman.com/wp-content/uploads/TSqlJoinTypePoster1.pdf).

