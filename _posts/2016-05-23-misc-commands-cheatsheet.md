---
layout: post
title: "Miscellaneous Commands"
date: 2016-05-19
---

## Miscellaneous Commands

### Creating a maven parent project and sub projects using command line 

`<div>
mvn archetype:generate -DgroupId=com.sample.package -DartifactId=sample-project
</div>`
Ensure the ** `<div><module></div>` ** tag exists in the parent pom
Once the parent project is created, `cd` into the parent project and repeat the above command. 

