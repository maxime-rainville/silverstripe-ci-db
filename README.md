# Silverstripe CMS Database GitHub action

This action is part of collection to make it easy to write CI for Silverstripe CMS project using GitHub actions.

It allows you to configure various database to work with Silverstripe CMS.

# Usage

```yml
name: CI

on:
  push:
  pull_request:

jobs:
  build:
    name: Silverstripe CMS CI
    runs-on: ubuntu-latest
    
    steps:
    - name: Checkout
      uses: actions/checkout@v2

    - name: Set up PHP
      uses: maxime-rainville/silverstripe-ci-setup@master

    - name: Run actions
      uses: ./myaction
      with:
        driver: ${{ matrix.db.driver }}
        version: ${{ matrix.db.version }}

    - name: Run test suite
      uses: maxime-rainville/silverstripe-ci-phpunit@master

```