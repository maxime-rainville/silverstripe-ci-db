# Silverstripe CMS Database GitHub action

[![build-test](https://github.com/maxime-rainville/silverstripe-ci-db/actions/workflows/workflow.yml/badge.svg)](https://github.com/maxime-rainville/silverstripe-ci-db/actions/workflows/workflow.yml)

This action is part of collection to make it easy to write CI for Silverstripe CMS project using GitHub actions.

It allows you to configure various databases to work with Silverstripe CMS.

## Simple Usage

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

## Inputs

The action accepts the following inputs:
- `driver` which specify the database to install (default `mysql`)
- `version` which specify the version of the detabase to install (default `latest`)
- `installPackage` which specify whether to install the matchinig Silverstripe CMS module for this database (default: `true`)

Supported drivers are:
- `mysql`
- `mariadb`
- `postgresql`
- `mssql`
- `sqlite`

## Output

`maxime-rainville/silverstripe-ci-db` will automatically populate the connection settings in the the GitHub action environment. `maxime-rainville/silverstripe-ci-phpunit` automatically reads the connection settings.

But you can read them in your action by accessing the following `env` keys.
```yml
 - name: Run test suite
    env:
      SS_ENVIRONMENT_TYPE: dev
      SS_DATABASE_CLASS: ${{ env.SS_DATABASE_CLASS }}
      SS_DATABASE_SERVER: ${{ env.SS_DATABASE_SERVER }}
      SS_DATABASE_USERNAME: ${{ env.SS_DATABASE_USERNAME }}
      SS_DATABASE_PASSWORD: ${{ env.SS_DATABASE_PASSWORD }}
      SS_DATABASE_NAME: ${{ env.SS_DATABASE_NAME }}
    run: vendor/bin/phpunit
```

## Advanced usecase


### Test multiple Database with a strategy

The main purpose of this action is to make it easy to test multiple Databases for libraries that need to work accross different set up. Using a strategy matrix, you can easily run your CI against many Database.

```yml
name: PHPUnit

on:
  push:
    branches: [ '*' ]
  pull_request:
    branches: [ master ]

jobs:
  test:
    strategy:
      fail-fast: false
      matrix:
        database:
          - driver: mysql
            version: 5.7
          - driver: mysql
          - driver: mariadb
          - driver: postgresql
    
    runs-on: ubuntu-latest
    
    steps:
    - name: Set up PHP
      uses: maxime-rainville/silverstripe-ci-setup@master

    - name: Set up database
      uses: maxime-rainville/silverstripe-ci-db@master
      with:
        driver: ${{ matrix.database.driver }}
        version: ${{ matrix.database.version }}

    - name: Run test suite
      uses: maxime-rainville/silverstripe-ci-phpunit@master
```

### Using with none Silverstripe CMS project

While this is not the primary use case for this action, you can use it with none Silverstripe CMS project if you want. Here's an example workflow for Laravel project.

Don't forget to set the `installPackage` input to `false` otherwise, it might try to install Silverstripe CMS dependencies in you project.

```yml
name: PHPUnit

on:
  push:
    branches: [ '*' ]
  pull_request:
    branches: [ master ]

jobs:
  test:

    strategy:
      fail-fast: false
      matrix:
        database:
          - driver: mysql
            laravelConnection: mysql
            version: 5.7
          - driver: mysql
            laravelConnection: mysql
          - driver: mariadb
            laravelConnection: mysql
          - driver: postgresql
            laravelConnection: pgsql
    
    runs-on: ubuntu-latest
    
    steps:

    - name: Checkout
      uses: actions/checkout@v2
    
    - name: Setup PHP
      uses: shivammathur/setup-php@v2
      with:
        php-version: 7.4
        
    - name: Install dependencies
      run: composer install --no-progress

    - name: Set up database
      uses: maxime-rainville/silverstripe-ci-db@master
      with:
        driver: ${{ matrix.database.driver }}
        version: ${{ matrix.database.version }}
        installPackage: false

    - name: Run test suite
      run: composer install --no-progress
      env:
        DB_CONNECTION: ${{ matrix.database.laravelConnection }}
        DB_HOST: ${{ env.SS_DATABASE_SERVER }}
        DB_PORT: ${{ env.SS_DATABASE_PORT }}
        DB_DATABASE: ${{ env.SS_DATABASE_NAME }}
        DB_USERNAME: ${{ env.SS_DATABASE_USERNAME }}
        DB_PASSWORD: ${{ env.SS_DATABASE_PASSWORD }}
```
