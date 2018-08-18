---
layout: post
author: yashumittal
title: Who contributed the most to open source in 2017? Let’s analyze GitHub’s data and find out.
date: 2017-10-27 00:52:00 +0530
categories: tips
tags: tips github
description: Let's take a peek sneek in github and check who has contributed the most to open source repository on github. Let’s analyze GitHub’s data and find out.
image: https://cdn.codecarrot.net/images/1_ywkHH3kMMVdGhXe6LDq7IA.png
---

![Top contributor on Github in 2017](https://cdn.codecarrot.net/images/1_ywkHH3kMMVdGhXe6LDq7IA.png)

For this analysis we’ll look at all the `PushEvents` published by GitHub during 2017. For each GitHub user we’ll have to make our best guess to determine to which organization they belong. We’ll only look at repositories that have received at least 20 stars this year.

Here are the results I got, which you can [tinker with in my the interactive Data Studio report](//datastudio.google.com/open/0ByGAKP3QmCjLU1JzUGtJdTlNOG8).

## Comparing the top cloud providers

Looking at GitHub during 2017:

* Microsoft appears to have ~1,300 employees actively pushing code to 825 top repositories on GitHub.
* Google displays ~900 employees active on GitHub, who are pushing code to ~1,100 top repositories.
* Amazon appears to have only 134 active employees on GitHub, pushing code to only 158 top projects.
* Not all projects are equal: While Googlers are contributing code to 25% more repositories than Microsoft, these repositories have collected way more stars (530,000 vs 260,000). Amazon repositories sum of 2017 stars? 27,000.

![Google, microsoft, amazon repo](https://cdn.codecarrot.net/images/1_EfhT-K6feRjyifX_K49AFg.png)

## RedHat, IBM, Pivotal, Intel, and Facebook

If Amazon seems so far behind Microsoft and Google — what are the companies in between? According to this ranking RedHat, Pivotal, and Intel are pushing great contributions to GitHub:

Note that the following table combines all of IBM regional domains — while the individual regions still show up in the subsequent tables.

![redhat, ibm](https://cdn.codecarrot.net/images/1_KnaOtVpdmPFabCtk-saYUw.png)

![pivotal, intel, fb](https://cdn.codecarrot.net/images/1_Dy08nNIdjxBQRqQ6zXTThg.png)

Facebook and IBM *(US)* have a similar number of GitHub users than Amazon, but the projects they contribute to have collected more stars *(especially Facebook)*:

![fb, ibm, amazon](https://cdn.codecarrot.net/images/1_ZJP36ojAFyo7BcZnJ-PT3Q.png)

Followed by Alibaba, Uber, and Wix:

![alibaba, uber, wix](https://cdn.codecarrot.net/images/1_yG3X8Sq35S8Z9mNLv9pliA.png)

GitHub itself, Apache, Tencent:

![GitHub itself, Apache, Tencent](https://cdn.codecarrot.net/images/1_Ij2hSTZiQndHdFRsFNwb-g.png)

Baidu, Apple, Mozilla:

![Baidu, Apple, Mozilla](https://cdn.codecarrot.net/images/1_ZRjQ0fNe39-qox3cy6OGUQ.png)

Oracle, Stanford, Mit, Shopify, MongoDb, Berkeley, VmWare, Netflix, Salesforce, Gsa.gov:

![Oracle, Stanford, Mit, Shopify, MongoDb, Berkeley, VmWare, Netflix, Salesforce, Gsa.gov](https://cdn.codecarrot.net/images/1_mi1gdgVUYRbTBoBuo14gtA.png)

LinkedIn, Broad Institute, Palantir, Yahoo, MapBox, Unity3d, Automattic, Sandia, Travis-ci, Spotify:

![LinkedIn, Broad Institute, Palantir, Yahoo, MapBox, Unity3d, Automattic, Sandia, Travis-ci, Spotify](https://cdn.codecarrot.net/images/1_yQzsoab7AFbQ2BTnPCGbXg.png)

Chromium, UMich, Zalando, Esri, IBM (UK), SAP, EPAM, Telerik, UK Cabinet Office, Stripe:

![Chromium, UMich, Zalando, Esri, IBM (UK), SAP, EPAM, Telerik, UK Cabinet Office, Stripe](https://cdn.codecarrot.net/images/1_TCbZaq4sgpjFQ9f4yFoWoQ.png)

Cern, Odoo, Kitware, Suse, Yandex, IBM (Canada), Adobe, AirBnB, Chef, The Guardian:

![Cern, Odoo, Kitware, Suse, Yandex, IBM (Canada), Adobe, AirBnB, Chef, The Guardian](https://cdn.codecarrot.net/images/1_zXxtygHJUi4tdNr1JRNlyg.png)

Arm, Macports, Docker, Nuxeo, NVidia, Yelp, Elastic, NYU, Wso2, Mesosphere, Inria:

![Arm, Macports, Docker, Nuxeo, NVidia, Yelp, Elastic, NYU, Wso2, Mesosphere, Inria](https://cdn.codecarrot.net/images/1_f6AK5xHrJIAhEn7t9569lQ.png)

Puppet, Stanford (CS), DatadogHQ, Epfl, NTT Data, Lawrence Livermore Lab:

![Puppet, Stanford (CS), DatadogHQ, Epfl, NTT Data, Lawrence Livermore Lab](https://cdn.codecarrot.net/images/1_RP5nyYdwn2d2pb05xnMxyA.png)

## My Methodology

### How I linked GitHub users to companies

Determining the organization to which each GitHub user belongs it’s not easy — but we can use the email domains that show up in each commit message contained on PushEvents:

* The same email can show up in more than one user, so I only considered GitHub users able to push code to GitHub projects with more than 20 stars during the period.
* I only counted GitHub users with more than 3 pushes during the period.
* Users pushing code to GitHub can display many different emails on their pushes — part of how Git works. To determine the organization for each user, I looked into the email their pushes shows up most frequently.
* Not everyone uses their organization email on GitHub. There are a lot of gmail.com, users.noreply.github.com, and other email hosting providers. Sometimes the reason for this is anonymity and protecting their corporate inboxes — but if I couldn’t see their email domain, I couldn’t count them. Sorry.
* Sometimes employees switch organizations. I assigned them to the one that got the more pushes according to these rules.

**My query**

```sql
#standardSQL
WITH
period AS (
  SELECT *
  FROM `githubarchive.month.2017*` a
),
repo_stars AS (
  SELECT repo.id, COUNT(DISTINCT actor.login) stars, APPROX_TOP_COUNT(repo.name, 1)[OFFSET(0)].value repo_name
  FROM period
  WHERE type='WatchEvent'
  GROUP BY 1
  HAVING stars>20
),
pushers_guess_emails_and_top_projects AS (
  SELECT *
    # , REGEXP_EXTRACT(email, r'@(.*)') domain
    , REGEXP_REPLACE(REGEXP_EXTRACT(email, r'@(.*)'), r'.*.ibm.com', 'ibm.com') domain
  FROM (
    SELECT actor.id
      , APPROX_TOP_COUNT(actor.login,1)[OFFSET(0)].value login
      , APPROX_TOP_COUNT(JSON_EXTRACT_SCALAR(payload, '$.commits[0].author.email'),1)[OFFSET(0)].value email
      , COUNT(*) c
      , ARRAY_AGG(DISTINCT TO_JSON_STRING(STRUCT(b.repo_name,stars))) repos
    FROM period a
    JOIN repo_stars b
    ON a.repo.id=b.id
    WHERE type='PushEvent'
    GROUP BY  1
    HAVING c>3
  )
)
SELECT * FROM (
  SELECT domain
    , githubers
    , (SELECT COUNT(DISTINCT repo) FROM UNNEST(repos) repo) repos_contributed_to
    , ARRAY(
        SELECT AS STRUCT JSON_EXTRACT_SCALAR(repo, '$.repo_name') repo_name
        , CAST(JSON_EXTRACT_SCALAR(repo, '$.stars') AS INT64) stars
        , COUNT(*) githubers_from_domain FROM UNNEST(repos) repo
        GROUP BY 1, 2
        HAVING githubers_from_domain>1
        ORDER BY stars DESC LIMIT 3
      ) top
    , (SELECT SUM(CAST(JSON_EXTRACT_SCALAR(repo, '$.stars') AS INT64)) FROM (SELECT DISTINCT repo FROM UNNEST(repos) repo)) sum_stars_projects_contributed_to
  FROM (
    SELECT domain, COUNT(*) githubers, ARRAY_CONCAT_AGG(ARRAY(SELECT * FROM UNNEST(repos) repo)) repos
    FROM pushers_guess_emails_and_top_projects
    #WHERE domain IN UNNEST(SPLIT('google.com|microsoft.com|amazon.com', '|'))
    WHERE domain NOT IN UNNEST(SPLIT('gmail.com|users.noreply.github.com|qq.com|hotmail.com|163.com|me.com|googlemail.com|outlook.com|yahoo.com|web.de|iki.fi|foxmail.com|yandex.ru', '|')) # email hosters
    GROUP BY 1
    HAVING githubers > 30
  )
  WHERE (SELECT MAX(githubers_from_domain) FROM (SELECT repo, COUNT(*) githubers_from_domain FROM UNNEST(repos) repo  GROUP BY repo))>4 # second filter email hosters
)
ORDER BY githubers DESC
```

### FAQ

**If an organization has 1,500 repositories, why do you only count 200? If a repository has 7,000 stars, why do you only show 1,500?**

I’m filtering for relevancy. I’m only counting stars given during 2017. For example, Apache has >1,500 repositories on GitHub, but only 205 have received more than 20 stars this year.

![How I linked GitHub users to companies](https://cdn.codecarrot.net/images/1_wf86s1GygY1u283nA6LoYQ.png)

### Is this the state of open source?

Note that analyzing GitHub doesn’t include top communities like Android, Chromium, GNU, Mozilla, nor the the Apache or Eclipse Foundation, and other projects that choose to run most of their activities outside of GitHub.

### You were unfair to my organization.

I can only count what I can see. Please challenge my assumptions and tell me how you would measure things in a better way. Working queries would be the best way.

For example, see how their ranking changes when I combine IBM’s region-based domains into their top one with one SQL transformation:

```sql
SELECT *, REGEXP_REPLACE(REGEXP_EXTRACT(email, r'@(.*)'), r'.*.ibm.com', 'ibm.com') domain
```

![IBM’s relative position moves significantly when you combine their regional email domains.](https://cdn.codecarrot.net/images/1_sKjuzOO2OYPcKGAzq9jDYw.png)

![IBM’s relative position moves significantly when you combine their regional email domains.](https://cdn.codecarrot.net/images/1_ywkHH3kMMVdGhXe6LDq7IA.png)

*IBM’s relative position moves significantly when you combine their regional email domains.*

This blog is originally published by [Felipe Hoffa](//medium.freecodecamp.org/@hoffa)
