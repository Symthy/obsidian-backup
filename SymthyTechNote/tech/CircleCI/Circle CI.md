
```
jobs:
  build:
    docker:
      - image: circleci/golang:1.16
    working_directory: ~/repo
    steps:
      - checkout
      - restore_cache:
          keys:
            - <module_name>-{{ checksum "go.sum" }}
      - run:
          name: build
          command: make
      - save_cache:
          key: <module_name>-{{ checksum "go.sum" }}
          paths:
            - go/pkg/mod
      - presist_to_workspace:
          root: .
          paths:
            - bin/<binary>
```
