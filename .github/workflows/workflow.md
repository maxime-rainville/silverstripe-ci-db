name: build-test
on:
  push:
    branches:
      - master
    paths-ignore:
      - '**.md'
  pull_request:
    paths-ignore:
      - '**.md'

jobs:
  build:
  
    name: Run
    runs-on: ${{ matrix.operating-system }}
    strategy:
      matrix:
        operating-system: [ubuntu-latest]
    steps:
    - name: Checkout
      uses: actions/checkout@v2

    - name: Run actions
      uses: ./actions

    - name: Test result
      run: |
        mysqladmin ping \
          --host=${{ env.SS_DATABASE_SERVER }} \
          --user=${{ env.SS_DATABASE_USERNAME }} \
          --password=${{ env.SS_DATABASE_PASSWORD }}; \
