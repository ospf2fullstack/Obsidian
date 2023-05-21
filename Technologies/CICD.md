---
title: CICD
description: CICD Basics
publish: true
permalink: CICD-Basics
tag: CICD
image: "Assets/innerarity.png"
---
# CICD
## CI
Continuous Integration
Take the code, package and hand it off to  [[CICD#CD]].
This stop often includes tests and security checks. 


## CD
Continuous Delivery/Deployment
Deploy the packaged code to services/bare metal/vm, etc...
There is a difference between Continuous Delivery and Continuous Deployment. 

*Delivery* = Manual intervention to deploy to production - log into [[Jenkins]] and press 'the button' to push the newest version.

*Deployment* = Automatic CI process that kicks off CD once CI builds and packages so it can deploy it without the interaction. 


# CICD Platforms
[[Jenkins]]
[[Github]]
[[Gitlab]]