
# CircleCI - AWS and NPM

```JavaScript
anchors:
  configure-aws-access: &configure-aws-access
    run:
      name: Configure AWS access
      command: |
        mkdir -p ~/.aws
        file=~/.aws/credentials
        echo "[default]" > $file
        echo "aws_access_key_id = $AWS_ACCESS_KEY_ID" >> $file
        echo "aws_secret_access_key = $AWS_SECRET_ACCESS_KEY" >> $file
  configure-aws-lambda-secrets: &configure-aws-lambda-secrets
    run:
      name: Configure AWS Lambda secrets
      command: |
        file=~/project/infrastructure/lambda/auth-lambda/config-secrets.json
        echo "{" > $file
        echo "  \"clientId\": \"$GH_CLIENT_ID\"," >> $file
        echo "  \"clientOrg\": \"$GH_CLIENT_ORG\"," >> $file
        echo "  \"clientSecret\": \"$GH_CLIENT_SECRET\"" >> $file
        echo "}" >> $file
  # run for building
  install-storybook-dependencies: &install-storybook-dependencies
    run:
      name: Install Storybook Dependencies
      command: |
        npm install .

  # run for deploying
  install-infrastructure-dependencies: &install-infrastructure-dependencies
    run:
      name: Install Infrastructure Dependencies
      command: |
        cd ./infrastructure
        npm install .

  configure-build-environment: &configure-build-environment
    run:
      name: Configure build environment
      command: |
        mkdir ~/workspace

  configure-run-build: &configure-run-build
    run:
      name: Run Build
      command: |
        npm run build-storybook
        npm run build
        cp -r storybook-static ~/workspace
        cp -r dist ~/workspace

  cache-dependencies: &cache-dependencies
    save_cache:
      key: dependency-cache-{{ checksum "package.json" }}
      paths:
        - ./node_modules
        - ./infrastructure/node_modules

  load-dependency-cache: &load-dependency-cache
    restore_cache:
      key: dependency-cache-{{ checksum "package.json" }}

  authenticate-with-registry: &authenticate-with-registry
    run:
      name: authenticate with registry
      command: |
        echo "@xerris:registry=https://npm.pkg.github.com/" > ~/.npmrc
        echo "//npm.pkg.github.com/:_authToken=$NPM_AUTH_TOKEN" >> ~/.npmrc

  bump-package-version: &bump-package-version
    run:
      name: increment package version number
      command: |
        git config --global user.name "$UNAME"
        git config --global user.email $EMAIL
        npm version patch -m "[skip ci] update to v%s"
        git push origin main

  publish-package: &publish-package
    run:
      name: publish library as npm package
      command: |
        cp -r ~/workspace/dist ~/project/dist
        npm publish

  run-tests: &run-tests
    run:
      name: run tests on component library
      command: |
        npm run test

version: 2.1
orbs:
  aws-s3: circleci/aws-s3@1.0.11

executors:
  default:
    docker:
      - image: cimg/node:16.18.1
jobs:
  test:
    executor: default
    steps:
      - checkout
      - *load-dependency-cache
      - *install-storybook-dependencies
      - *cache-dependencies
      - *run-tests

  build:
    executor: default
    steps:
      - checkout
      - *load-dependency-cache
      - *install-storybook-dependencies
      - *cache-dependencies
      - *configure-build-environment
      - *configure-run-build
      - persist_to_workspace:
          root: ~/
          paths: workspace

  deploy:
    executor: default
    steps:
      - checkout
      - *load-dependency-cache
      - *install-infrastructure-dependencies
      - *configure-aws-access
      - *configure-aws-lambda-secrets
      - attach_workspace:
          at: ~/
      - run:
          name: Deploy
          command: |
            cp -r ~/workspace/storybook-static ./
            cd ./infrastructure
            ENV=$ENV npm run deploy
          no_output_timeout: 30m

  publish:
    executor: default
    steps:
      - checkout
      - *authenticate-with-registry
      - *load-dependency-cache
      - *bump-package-version
      - attach_workspace:
          at: ~/
      - *publish-package

workflows:
  ci-dev:
    jobs:
      - test:
          context: xerris-dev

      - build:
          context: xerris-dev
          requires:
            - test

      - deploy:
          context: xerris-prod
          filters:
            branches:
              only:
                - main
          requires:
            - build

      - publish:
          context: xerris-prod
          requires:
            - build
          filters:
            branches:
              only:
                - main
```
