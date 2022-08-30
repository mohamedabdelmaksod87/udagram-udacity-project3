# In our app we used Circleci which integrated and linked with our github repo for CI/CD process and by using circleci we were able to create a pipeline to handle our jobs in an orderded and controlled steps.

## Our Pipeline process consists of:

# The version field is intended to be used in order to issue warnings for deprecation or breaking changes.

version: 2.1

# orgs contain basc recipes and reproducible actions (install node, aws, eb)

orbs:

node: circleci/node@5.0.2
aws-cli: circleci/aws-cli@3.1.1
aws-elastic-beanstalk: circleci/aws-elastic-beanstalk@2.0.1

# below are the jobs executed in our pipeline in order

jobs:
build:

# the base image can run most needed actions with orbs

    docker:
      - image: "cimg/base:stable"
        environment:
          CHROME_BIN: /usr/bin/google-chrome

# here we install and prepare our enviroments needed to during the pipeline execution

    steps:
      - node/install
      - aws-elastic-beanstalk/setup
      - aws-cli/setup
      - checkout

# install dependencies in both frontend & backend

      - run:
          name: Front-End Install Dep
          command: |
            npm run frontend:install
      - run:
          name: Back-End Install Dep
          command: |
            npm run backend:install

# run Frontend test script to validate UI Code before build & deploy

      - run:
          name: Front-End Testing
          command: |
            npm run frontend:test

# Build both frontend & backend app versions which will be run on AWS different services

      - run:
          name: Front-End Build
          command: |
            npm run frontend:build
      - run:
          name: Back-End Build
          command: |
            npm run backend:build

# Deploy both frontend & backend app on AWS services

      - run:
          name: Front-End Deploy
          command: |
            npm run frontend:deploy
      - run:
          name: Back-End Deploy
          command: |
            npm run backend:deploy
