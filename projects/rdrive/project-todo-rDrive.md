---
id: project-todo-rDrive
aliases: []
tags: []
---

- add cloudfront dist infront of api gateway with geolocation
- add backwards nav to image preview
- add video player to FE
- create password change to rdrive
- turn into mono repo
- make a dashboard for the resources i have for rDrive


## Writing the CICD pipeline
I will write a basic CICD pipeline that builds and deploys both the FE and the BE on commit to main, I will refactor later using
the nx affected command and pnpm work spaces
    - detect when one of the sub folders changes
    - set up aws credentials
    - FE
        - install the dependencies
        - build FE to dist folder
        - change into infrastructure folder and run cdk deploy (no approval)
    - BE
        - install depeendencies
        - build the lambdas?
        - change into infra folder and run cdk deploy (no approval)

### Basic Pipeline
- set up AWS
- install dependencies for FE
- install dependencies for BE
- deploy FE
- deploy BE
