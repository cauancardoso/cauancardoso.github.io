---
layout: post
title: How to join three tables in Postgresql
subtitle: Full outer join of RAIS 2014, 2015 and 2016 tables
gh-repo: cauancardoso/cauancardoso.github.io
gh-badge: [star, fork, follow]
tags: [SQL, Postgresql]
comments: true
---

The **Annual Social Information Report (RAIS)** is a socioeconomic report of the formal work in Brazil. It includes monthly information of admissions, dismissals, medical leaves, salaries and demographic information of the workers. 

The RAIS 2014 has 60 variables and more than 74 million registers; the RAIS 2015 has 76 variables and more than 72 million registers; the RAIS 2016 has 76 variables and more than 67 million registers. Each register represents a job linkage in that year.

Here I will show very briefly how I've managed to join the RAIS 2014, 2015 and 2016 dataset into a single dataset using [PostgreSQL](https://www.postgresql.org/) to construct monthly employment entry and exit rates in the 2014-2016 period.

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

I used the ```v1``` and ```v2``` variables (which are called **pis** and **cpf**) to match the workers from one table to another. Because these variables exist in all three tables with the same name (and I didn't rename them for reasons I'll explain later), these columns can be selected in just one of them. So, I selected all the variables of the 2014 table (60 variables) and all the variables **except cpf and pis** from the 2015 and 2016 tables (74 variables each). My query looked something like this: 

```javascript
SELECT rais_2014.*,
       rais_2015.v3_2015,  
       rais_2015.v4_2015,
       rais_2015.v5_2015,
       rais_2015.v6_2015,
       rais_2015.v7_2015,
       rais_2015.v8_2015,
       rais_2015.v9_2015,
       rais_2015.v10_2015,
       rais_2015.v11_2015,
       rais_2015.v12_2015,
       rais_2015.v13_2015,
       rais_2015.v14_2015,
       rais_2015.v15_2015,
       rais_2015.v16_2015,
       rais_2015.v17_2015,
       rais_2015.v18_2015,
       rais_2015.v19_2015,
       rais_2015.v20_2015,
       rais_2015.v21_2015,
       rais_2015.v22_2015,
       rais_2015.v23_2015,
       rais_2015.v24_2015,
       rais_2015.v25_2015,
       rais_2015.v26_2015,
       rais_2015.v27_2015,
       rais_2015.v28_2015,
       rais_2015.v29_2015,
       rais_2015.v30_2015,
       rais_2015.v31_2015,
       rais_2015.v32_2015,
       rais_2015.v33_2015,
       rais_2015.v34_2015,
       rais_2015.v35_2015,
       rais_2015.v36_2015,
       rais_2015.v37_2015,
       rais_2015.v38_2015,
       rais_2015.v39_2015,
       rais_2015.v40_2015,
       rais_2015.v41_2015,
       rais_2015.v42_2015,
       rais_2015.v43_2015,
       rais_2015.v44_2015,
       rais_2015.v45_2015,
       rais_2015.v46_2015,
       rais_2015.v47_2015,
       rais_2015.v48_2015,
       rais_2015.v49_2015,
       rais_2015.v50_2015,
       rais_2015.v51_2015,
       rais_2015.v52_2015,
       rais_2015.v53_2015,
       rais_2015.v54_2015,
       rais_2015.v55_2015,  
       rais_2015.v56_2015,
       rais_2015.v57_2015,
       rais_2015.v58_2015,
       rais_2015.v59_2015,
       rais_2015.v60_2015,
       rais_2015.v61_2015,
       rais_2015.v62_2015,
       rais_2015.v63_2015,
       rais_2015.v64_2015,
       rais_2015.v65_2015,
       rais_2015.v66_2015,
       rais_2015.v67_2015,
       rais_2015.v68_2015,
       rais_2015.v69_2015,
       rais_2015.v70_2015,
       rais_2015.v71_2015,
       rais_2015.v72_2015,
       rais_2015.v73_2015,
       rais_2015.v74_2015,
       rais_2015.v75_2015,
       rais_2015.v76_2015,  
       rais_2016.v3_2016,
       rais_2016.v4_2016,
       rais_2016.v5_2016,
       rais_2016.v6_2016,
       rais_2016.v7_2016,
       rais_2016.v8_2016,
       rais_2016.v9_2016,
       rais_2016.v10_2016,
       rais_2016.v11_2016,
       rais_2016.v12_2016,
       rais_2016.v13_2016,
       rais_2016.v14_2016,
       rais_2016.v15_2016,
       rais_2016.v16_2016,
       rais_2016.v17_2016,
       rais_2016.v18_2016,
       rais_2016.v19_2016,
       rais_2016.v20_2016,
       rais_2016.v21_2016,
       rais_2016.v22_2016,
       rais_2016.v23_2016,
       rais_2016.v24_2016,
       rais_2016.v25_2016,
       rais_2016.v26_2016,
       rais_2016.v27_2016,
       rais_2016.v28_2016,
       rais_2016.v29_2016,
       rais_2016.v30_2016,
       rais_2016.v31_2016,
       rais_2016.v32_2016,
       rais_2016.v33_2016,
       rais_2016.v34_2016,
       rais_2016.v35_2016,
       rais_2016.v36_2016,
       rais_2016.v37_2016,
       rais_2016.v38_2016,
       rais_2016.v39_2016,
       rais_2016.v40_2016,
       rais_2016.v41_2016,
       rais_2016.v42_2016,
       rais_2016.v43_2016,
       rais_2016.v44_2016,
       rais_2016.v45_2016,
       rais_2016.v46_2016,
       rais_2016.v47_2016,
       rais_2016.v48_2016,
       rais_2016.v49_2016,
       rais_2016.v50_2016,
       rais_2016.v51_2016,
       rais_2016.v52_2016,
       rais_2016.v53_2016,
       rais_2016.v54_2016,
       rais_2016.v55_2016,
       rais_2016.v56_2016,
       rais_2016.v57_2016,
       rais_2016.v58_2016,
       rais_2016.v59_2016,
       rais_2016.v60_2016,
       rais_2016.v61_2016,
       rais_2016.v62_2016,
       rais_2016.v63_2016,
       rais_2016.v64_2016,
       rais_2016.v65_2016,
       rais_2016.v66_2016,  
       rais_2016.v67_2016,  
       rais_2016.v68_2016,  
       rais_2016.v69_2016,  
       rais_2016.v70_2016,  
       rais_2016.v71_2016,  
       rais_2016.v72_2016,  
       rais_2016.v73_2016,  
       rais_2016.v74_2016,  
       rais_2016.v75_2016,  
       rais_2016.v76_2016  
INTO rais_2014_2016 
FROM rais_2014 
FULL OUTER JOIN rais_2015  
	USING (v1, v2)  
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
      rais_2014_2016.v4_2015 = rais_2015.v4 AND
      rais_2014_2016.v5_2015 = rais_2015.v5 AND
      rais_2014_2016.v6_2015 = rais_2015.v6 AND
      rais_2014_2016.v7_2015 = rais_2015.v7 AND
      rais_2014_2016.v8_2015 = rais_2015.v8 AND
      rais_2014_2016.v9_2015 = rais_2015.v9 AND
      rais_2014_2016.v10_2015 = rais_2015.v10 AND
      rais_2014_2016.v11_2015 = rais_2015.v11 AND
      rais_2014_2016.v12_2015 = rais_2015.v12 AND
      rais_2014_2016.v13_2015 = rais_2015.v13 AND
      rais_2014_2016.v14_2015 = rais_2015.v14 AND
      rais_2014_2016.v15_2015 = rais_2015.v15 AND
      rais_2014_2016.v16_2015 = rais_2015.v16 AND
      rais_2014_2016.v17_2015 = rais_2015.v17 AND
      rais_2014_2016.v18_2015 = rais_2015.v18 AND
      rais_2014_2016.v19_2015 = rais_2015.v19 AND
      rais_2014_2016.v20_2015 = rais_2015.v20 AND
      rais_2014_2016.v21_2015 = rais_2015.v21 AND
      rais_2014_2016.v22_2015 = rais_2015.v22 AND
      rais_2014_2016.v23_2015 = rais_2015.v23 AND
      rais_2014_2016.v24_2015 = rais_2015.v24 AND
      rais_2014_2016.v25_2015 = rais_2015.v25 AND
      rais_2014_2016.v26_2015 = rais_2015.v26 AND
      rais_2014_2016.v27_2015 = rais_2015.v27 AND
      rais_2014_2016.v28_2015 = rais_2015.v28 AND
      rais_2014_2016.v29_2015 = rais_2015.v29 AND
      rais_2014_2016.v30_2015 = rais_2015.v30 AND
      rais_2014_2016.v31_2015 = rais_2015.v31 AND
      rais_2014_2016.v32_2015 = rais_2015.v32 AND
      rais_2014_2016.v33_2015 = rais_2015.v33 AND
      rais_2014_2016.v34_2015 = rais_2015.v34 AND
      rais_2014_2016.v35_2015 = rais_2015.v35 AND
      rais_2014_2016.v36_2015 = rais_2015.v36 AND
      rais_2014_2016.v37_2015 = rais_2015.v37 AND
      rais_2014_2016.v38_2015 = rais_2015.v38 AND
      rais_2014_2016.v39_2015 = rais_2015.v39 AND
      rais_2014_2016.v40_2015 = rais_2015.v40 AND
      rais_2014_2016.v41_2015 = rais_2015.v41 AND
      rais_2014_2016.v42_2015 = rais_2015.v42 AND
      rais_2014_2016.v43_2015 = rais_2015.v43 AND
      rais_2014_2016.v44_2015 = rais_2015.v44 AND
      rais_2014_2016.v45_2015 = rais_2015.v45 AND
      rais_2014_2016.v46_2015 = rais_2015.v46 AND
      rais_2014_2016.v47_2015 = rais_2015.v47 AND
      rais_2014_2016.v48_2015 = rais_2015.v48 AND
      rais_2014_2016.v49_2015 = rais_2015.v49 AND
      rais_2014_2016.v50_2015 = rais_2015.v50 AND
      rais_2014_2016.v51_2015 = rais_2015.v51 AND
      rais_2014_2016.v52_2015 = rais_2015.v52 AND
      rais_2014_2016.v53_2015 = rais_2015.v53 AND
      rais_2014_2016.v54_2015 = rais_2015.v54 AND
      rais_2014_2016.v55_2015 = rais_2015.v55 AND
      rais_2014_2016.v56_2015 = rais_2015.v56 AND
      rais_2014_2016.v57_2015 = rais_2015.v57 AND
      rais_2014_2016.v58_2015 = rais_2015.v58 AND
      rais_2014_2016.v59_2015 = rais_2015.v59 AND
      rais_2014_2016.v60_2015 = rais_2015.v60 AND
      rais_2014_2016.v61_2015 = rais_2015.v61 AND
      rais_2014_2016.v62_2015 = rais_2015.v62 AND
      rais_2014_2016.v63_2015 = rais_2015.v63 AND
      rais_2014_2016.v64_2015 = rais_2015.v64 AND
      rais_2014_2016.v65_2015 = rais_2015.v65 AND
      rais_2014_2016.v66_2015 = rais_2015.v66 AND
      rais_2014_2016.v67_2015 = rais_2015.v67 AND
      rais_2014_2016.v68_2015 = rais_2015.v68 AND
      rais_2014_2016.v69_2015 = rais_2015.v69 AND
      rais_2014_2016.v70_2015 = rais_2015.v70 AND
      rais_2014_2016.v71_2015 = rais_2015.v71 AND
      rais_2014_2016.v72_2015 = rais_2015.v72 AND
      rais_2014_2016.v73_2015 = rais_2015.v73 AND
      rais_2014_2016.v74_2015 = rais_2015.v74 AND
      rais_2014_2016.v75_2015 = rais_2015.v75 AND
      rais_2014_2016.v76_2015 = rais_2015.v76
;
UPDATE rais_2014_2016
SET v1 = rais_2016.v1, v2 = rais_2016.v2
FROM rais_2016
WHERE rais_2014_2016.v3_2016 = rais_2016.v3 AND
      rais_2014_2016.v4_2016 = rais_2016.v4 AND
      rais_2014_2016.v5_2016 = rais_2016.v5 AND
      rais_2014_2016.v6_2016 = rais_2016.v6 AND
      rais_2014_2016.v7_2016 = rais_2016.v7 AND
      rais_2014_2016.v8_2016 = rais_2016.v8 AND
      rais_2014_2016.v9_2016 = rais_2016.v9 AND
      rais_2014_2016.v10_2016 = rais_2016.v10 AND
      rais_2014_2016.v11_2016 = rais_2016.v11 AND
      rais_2014_2016.v12_2016 = rais_2016.v12 AND
      rais_2014_2016.v13_2016 = rais_2016.v13 AND
      rais_2014_2016.v14_2016 = rais_2016.v14 AND
      rais_2014_2016.v15_2016 = rais_2016.v15 AND
      rais_2014_2016.v16_2016 = rais_2016.v16 AND
      rais_2014_2016.v17_2016 = rais_2016.v17 AND
      rais_2014_2016.v18_2016 = rais_2016.v18 AND
      rais_2014_2016.v19_2016 = rais_2016.v19 AND
      rais_2014_2016.v20_2016 = rais_2016.v20 AND
      rais_2014_2016.v21_2016 = rais_2016.v21 AND
      rais_2014_2016.v22_2016 = rais_2016.v22 AND
      rais_2014_2016.v23_2016 = rais_2016.v23 AND
      rais_2014_2016.v24_2016 = rais_2016.v24 AND
      rais_2014_2016.v25_2016 = rais_2016.v25 AND
      rais_2014_2016.v26_2016 = rais_2016.v26 AND
      rais_2014_2016.v27_2016 = rais_2016.v27 AND
      rais_2014_2016.v28_2016 = rais_2016.v28 AND
      rais_2014_2016.v29_2016 = rais_2016.v29 AND
      rais_2014_2016.v30_2016 = rais_2016.v30 AND
      rais_2014_2016.v31_2016 = rais_2016.v31 AND
      rais_2014_2016.v32_2016 = rais_2016.v32 AND
      rais_2014_2016.v33_2016 = rais_2016.v33 AND
      rais_2014_2016.v34_2016 = rais_2016.v34 AND
      rais_2014_2016.v35_2016 = rais_2016.v35 AND
      rais_2014_2016.v36_2016 = rais_2016.v36 AND
      rais_2014_2016.v37_2016 = rais_2016.v37 AND
      rais_2014_2016.v38_2016 = rais_2016.v38 AND
      rais_2014_2016.v39_2016 = rais_2016.v39 AND
      rais_2014_2016.v40_2016 = rais_2016.v40 AND
      rais_2014_2016.v41_2016 = rais_2016.v41 AND
      rais_2014_2016.v42_2016 = rais_2016.v42 AND
      rais_2014_2016.v43_2016 = rais_2016.v43 AND
      rais_2014_2016.v44_2016 = rais_2016.v44 AND
      rais_2014_2016.v45_2016 = rais_2016.v45 AND
      rais_2014_2016.v46_2016 = rais_2016.v46 AND
      rais_2014_2016.v47_2016 = rais_2016.v47 AND
      rais_2014_2016.v48_2016 = rais_2016.v48 AND
      rais_2014_2016.v49_2016 = rais_2016.v49 AND
      rais_2014_2016.v50_2016 = rais_2016.v50 AND
      rais_2014_2016.v51_2016 = rais_2016.v51 AND
      rais_2014_2016.v52_2016 = rais_2016.v52 AND
      rais_2014_2016.v53_2016 = rais_2016.v53 AND
      rais_2014_2016.v54_2016 = rais_2016.v54 AND
      rais_2014_2016.v55_2016 = rais_2016.v55 AND
      rais_2014_2016.v56_2016 = rais_2016.v56 AND
      rais_2014_2016.v57_2016 = rais_2016.v57 AND
      rais_2014_2016.v58_2016 = rais_2016.v58 AND
      rais_2014_2016.v59_2016 = rais_2016.v59 AND
      rais_2014_2016.v60_2016 = rais_2016.v60 AND
      rais_2014_2016.v61_2016 = rais_2016.v61 AND
      rais_2014_2016.v62_2016 = rais_2016.v62 AND
      rais_2014_2016.v63_2016 = rais_2016.v63 AND
      rais_2014_2016.v64_2016 = rais_2016.v64 AND
      rais_2014_2016.v65_2016 = rais_2016.v65 AND
      rais_2014_2016.v66_2016 = rais_2016.v66 AND
      rais_2014_2016.v67_2016 = rais_2016.v67 AND
      rais_2014_2016.v68_2016 = rais_2016.v68 AND
      rais_2014_2016.v69_2016 = rais_2016.v69 AND
      rais_2014_2016.v70_2016 = rais_2016.v70 AND
      rais_2014_2016.v71_2016 = rais_2016.v71 AND
      rais_2014_2016.v72_2016 = rais_2016.v72 AND
      rais_2014_2016.v73_2016 = rais_2016.v73 AND
      rais_2014_2016.v74_2016 = rais_2016.v74 AND
      rais_2014_2016.v75_2016 = rais_2016.v75 AND
      rais_2014_2016.v76_2016 = rais_2016.v76 AND
;
```
And it's done!

Now I have a single table for 3 consecutive years and two columns with id variables (```v1``` and ```v2```) that I can use to merge with others datasets that also have **pis** and **cpf** variables, e.g. **Cadastro Único**, **Caged**, **Seguro Desemprego** and many others.

If you want to learn more about the different ways you can JOIN tables in SQL, [see this very instructed document created by Steve Stedman](http://stevestedman.com/wp-content/uploads/TSqlJoinTypePoster1.pdf).

