# 持续集成部署研究

## Travis-ci

```
sudo gem install travis
travis login
travis encrypt-file ~/.ssh/id_rsa --add
```

```
sudo: false

language: node_js

node_js:
  - "8"

branches:
  only:
    - master

git:
  depth: 3
  quiet: true
  submodules: false

cache:
  directories:
    - "node_modules"

before_install:
  - npm i -g npm@version-number

install:
  - echo 'do some install'

before_script:
  - npm install -g gulp-cli

script:
  - gulp

before_deploy:
  - echo 'do some before_deploy'

# before_deploy:
#       # Set up git user name and tag this commit
#       - git config --local user.name "YOUR GIT USER NAME"
#       - git config --local user.email "YOUR GIT USER EMAIL"
#       - git tag "$(date +'%Y%m%d%H%M%S')-$(git log --format=%h -1)"

deploy:
  # deploy develop to the staging environment
  - provider: script
    script: bash scripts/deploy.sh staging
    skip_cleanup: true
    on:
      branch: develop
  # deploy master to production
  - provider: script
    script: bash scripts/deploy.sh production
    skip_cleanup: true
    on:
      branch: master

after_deploy:
  - echo 'do some after_deploy'

after_success:
  - ssh root@47.106.128.182 "./deploy"

addons:
  ssh_known_hosts: 47.106.128.182
```
