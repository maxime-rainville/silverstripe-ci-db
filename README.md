# Silverstripe CMS Database GitHub action

This action is part of collection to make it easy to write CI for Silverstripe CMS project using GitHub actions.

It allows you to configure various database to work with Silverstripe CMS.

# Usage

```yml
name: PHPUnit for Silverstripe CMS

on:
  push:
    branches: [ '*' ]
  pull_request:
    branches: [ master ]

jobs:
  test:
    runs-on: ubuntu-latest
    
    steps:
    - name: Set up PHP
      uses: maxime-rainville/silverstripe-ci-setup@master

    - name: Set up database
      uses: maxime-rainville/silverstripe-ci-db@master

    - name: Run test suite
      uses: maxime-rainville/silverstripe-ci-phpunit@master
```