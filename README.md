# tfectl
[![Tests](https://github.com/AGLEnergyPublic/tfectl/actions/workflows/go.yml/badge.svg)](https://github.com/AGLEnergyPublic/tfectl/actions/workflows/go.yml)
[![GitHub license](https://img.shields.io/github/license/AGLEnergyPublic/tfectl.svg)](https://github.com/AGLEnergyPublic/tfectl/blob/main/LICENSE)
[![GoDoc](https://godoc.org/github.com/AGLEnergyPublic/tfectl?status.svg)](https://godoc.org/github.com/AGLEnergyPublic/tfectl)
[![Go Report Card](https://goreportcard.com/badge/github.com/AGLEnergyPublic/tfectl)](https://goreportcard.com/report/github.com/AGLEnergyPublic/tfectl)
[![GitHub issues](https://img.shields.io/github/issues/AGLEnergyPublic/tfectl.svg)](https://github.com/AGLEnergyPublic/tfectl/issues)

* CLI Utility to query/manage TFE inspired by [tfe-cli](https://github.com/rgreinho/tfe-cli)

## Setup
* Copy the binary (either Windows or Linux) to a path on your machine. Add the `.exe` extension if using it on Windows
  ```ps
  PS> .\tfectl.exe
  Query TFE from the command line.

  Usage:
    tfectl [command]

  Available Commands:
    admin       Manage TFE admin operations
    completion  Generate the autocompletion script for the specified shell
    help        Help about any command
    policy      Query TFE policies
    run         Manage TFE runs
    team        Manage TFE teams
    variable    Manage TFE workspace variables
    workspace   Manage TFE workspaces


  Flags:
    -h, --help                  help for tfectl
    -l, --log string            log level (debug, info, warn, error, fatal, panic)
    -o, --organization string   terraform organization or set TFE_ORG
    -q, --query string          JMESPath compatible query to parse JSON output
    -t, --token string          terraform token or set TFE_TOKEN
    -v, --version               version for tfectl

  Use "tfectl [command] --help" for more information about a command.
  ```

## Initialization
* `TFE_ADDRESS`: TFE URL defaults to `https://app.terraform.io/`
* `TFE_ORG`: TFE Organization
* `TFE_TOKEN`: token with read access to Organization specified in `TFE_ORG`
* Additionally `TFE_ORG` and `TFE_TOKEN` variables can be passed via CLI

## Usage
* To see available options
```bash
# /sbin/tfectl --help
Query TFE from the command line.

Usage:
  tfectl [command]

Available Commands:
  admin       Manage TFE admin operations
  completion  Generate the autocompletion script for the specified shell
  help        Help about any command
  policy      Query TFE policies
  run         Manage TFE runs
  team        Manage TFE teams
  variable    Manage TFE workspace variables
  workspace   Manage TFE workspaces


Flags:
  -h, --help                  help for tfectl
  -l, --log string            log level (debug, info, warn, error, fatal, panic)
  -o, --organization string   terraform organization or set TFE_ORG
  -q, --query string          JMESPath compatible query to parse JSON output
  -t, --token string          terraform token or set TFE_TOKEN
  -v, --version               version for tfectl

Use "tfectl [command] --help" for more information about a command.
```

### Workspace
* #### List
  * Run with no arguments returns workspaceName and workspaceID for all workspaces in org
  * Run with `--filter`
 
  ```bash
    $ tfectl workspace list --filter workspace-1
    [
      {
        "name": "workspace-1",
        "id": "ws-RZP914jsX1Hmc9Yo"
        "locked": false,
        "execution_mode": "remote",
        "terraform_version": "1.3.0"
      }
    ]
  ```

  * Run with `--detail`
 
  ```bash 
    $ tfectl workspace list --filter workspace-1 --detail
    [
      {
        "name": "workspace-1",
        "id": "ws-RZP914jsX1Hmc9Yo",
        "locked": false,
        "terraform_version": "1.3.0",
        "created_days_ago": "819.167082",
        "updated_days_ago": "2.279692",
        "last_remote_run_days_ago": "2.281231",
        "last_state_update_days_ago": "30.174812"
      }
    ]
  ```
* #### Lock/Unlock
  * Run with a comma-separated string of workspaceIDs or a workspaceName filter (mutually exclusive)

  ```bash
    $ tfectl workspace lock --ids ws-SxWNNcYPkLD48ZC7
    [
      {
        "id": "ws-SxWNNcYPkLD48ZC7",
        "locked": true,
        "name": "test-workspace-1"
      }
    ] 
  ```

  * Operation can be run against a workspace that is already locked
  ```bash
    $ tfectl workspace lock --filter dev-workspace
    [
      {
        "id": "ws-5xUNCXVKrryoPcEp",
        "locked": true,
        "name": "dev-workspace"
      }
    ]
  ```

  * Optionally the `lock` operation takes a `--reason` argument
* #### Lock All/ Unlock All
  * Locks/Unlocks all workspaces in the specified org

  ```bash
    $ tfectl workspace lockall
    [
      {
        "id": "ws-SxWNNcYPkLD48ZC7",
        "locked": true,
        "name": "test-workspace-1"
      },
      {
        "id": "ws-LXkPCWnJKJ1FSgjs",
        "locked": true,
        "name": "uat-workspace"
      },
      {
        "id": "ws-E9o8VitHDAvCp3wj",
        "locked": true,
        "name": "uat-2-workspace"
      },
      {
        "id": "ws-5xUNCXVKrryoPcEp",
        "locked": true,
        "name": "dev-workspace"
      }
    ]
  ```
### Runs
* `run` sub-command lets you manage runs against one or more workspaces
* #### Bulk Queue
  * Bulk queue plans against one or many workspaces
 
  ```bash
    $ tfectl run queue --filter workspace-sandbox
    [
      {
        "id": "run-pX9Lrq5KCrsgCYFH",
        "workspace_id": "ws-DpeRu7KpazXEWKoJ",
        "workspace_name": "workspace-sandbox",
        "status": "pending"
      }
    ]
  ```

* #### Apply runs
  * Apply pending plans - takes a comma-separated-string of runIDs

  ```bash
    $ tfectl run apply --ids run-UowKQd1cF7bgNfCp
    [
      {
        "id": "run-UowKQd1cF7bgNfCp",
        "workspace_id": "ws-N2qoyJxF1TkfeRYy",
        "workspace_name": "test-workspace-2",
        "status": "applying"
      }
    ]
  ```

* #### Query runs
  * Query/Get run-details from runIDs

  ```bash
    $ tfectl run get --ids run-UowKQd1cF7bgNfCp
    [
      {
        "id": "run-UowKQd1cF7bgNfCp",
        "workspace_id": "ws-N2qoyJxF1TkfeRYy",
        "workspace_name": "test-workspace-2",
        "status": "applied"
      }
    ]
  ```
### Variables
* CRUD operations on workspace variables
* #### Query/List workspace variables
  ```bash
    $ tfectl variable list --workspace-filter workspace-sandbox
    [
      {
        "workspace_id": "ws-DpeRu7KpazXEWKoJ",
        "workspace_name": "workspace-sandbox",
        "variables": [
          {
            "id": "var-RH7Q9pyD8gtgabtz",
            "key": "WORKSPACE_VAR_1",
            "value": "",
            "description": "",
            "category": "env",
            "hcl": false,
            "sensitive": false
          },
          {
            "id": "var-wQutb5uQeSb4SwRn",
            "key": "workspace_tf_var",
            "value": "",
            "description": "",
            "category": "terraform",
            "hcl": false,
            "sensitive": true
          },
          {
            "id": "var-cSB5E11TRewuyfd9",
            "key": "WORKSPACE_VAR_2",
            "value": "",
            "description": "",
            "category": "env",
            "hcl": false,
            "sensitive": false
          },
          {
            "id": "var-SP4Lcue83mCKVvHW",
            "key": "WORKSPACE_SECRET_VAR",
            "value": "",
            "description": "",
            "category": "env",
            "hcl": false,
            "sensitive": true
          }
        ]
      }
    ]
  ```

* #### Create new workspace variable
  ```bash
    $ tfectl variable create --workspace-id ws-DpeRu7KpazXEWKoJ --description "test" --key "testCLI" --value "testCLI value" --sensitive true --type terraform --hcl
    {
      "id": "var-uCgZrzkPhis6qXTS",
      "key": "testCLI",
      "value": "",
      "description": "test",
      "category": "terraform",
      "hcl": true,
      "sensitive": true
    }
  ```
* #### Update existing workspace variable
  ```bash
    $ tfectl variable update --variable-id var-uCgZrzkPhis6qXTS --workspace-id ws-DpeRu7KpazXEWKoJ --value "test CLI Value 2" --key "testCLI" --hcl --sensitive true
    {
      "id": "var-uCgZrzkPhis6qXTS",
      "key": "testCLI",
      "value": "",
      "description": "Variable Updated by tfectl",
      "category": "terraform",
      "hcl": true,
      "sensitive": true
    }
  ```
* #### Delete existing workspace variable
  ```bash
    $ tfectl variable delete --variable-id var-uCgZrzkPhis6qXTS --workspace-id ws-DpeRu7KpazXEWKoJ
    # Returns current variables (similar to variable list)
    [
      {
        "workspace_id": "ws-DpeRu7KpazXEWKoJ",
        "workspace_name": "workspace-sandbox",
        "variables": [
          {
            "id": "var-RH7Q9pyD8gtgabtz",
            "key": "WORKSPACE_VAR_1",
            "value": "",
            "description": "",
            "category": "env",
            "hcl": false,
            "sensitive": false
          },
          {
            "id": "var-wQutb5uQeSb4SwRn",
            "key": "workspace_tf_var",
            "value": "",
            "description": "",
            "category": "terraform",
            "hcl": false,
            "sensitive": true
          },
          {
            "id": "var-cSB5E11TRewuyfd9",
            "key": "WORKSPACE_VAR_2",
            "value": "",
            "description": "",
            "category": "env",
            "hcl": false,
            "sensitive": false
          },
          {
            "id": "var-SP4Lcue83mCKVvHW",
            "key": "WORKSPACE_SECRET_VAR",
            "value": "",
            "description": "",
            "category": "env",
            "hcl": false,
            "sensitive": true
          }
        ]
      }
    ]
  ```

* #### Create variables from file
  ```bash
    $ tfectl variable create from-file --file variables.json --workspace-id ws-DpeRu7KpazXEWKoJ
    [
      {
        "id": "var-oDNV14eJf9ijjcc2",
        "key": "test1",
        "value": "value1",
        "description": "Test Variable 1",
        "category": "env",
        "hcl": false,
        "sensitive": false
      },
      {
        "id": "var-e1vFqg3ooToLi5xR",
        "key": "test2",
        "value": "",
        "description": "Test Variable 2 - sensitive",
        "category": "env",
        "hcl": false,
        "sensitive": true
      }
    ]
  ```

### Admin
* Perform Admin operations supported by the TFE Admin API.
* NOTE: Admin settings are only available in Terraform Enterprise.

* #### Runs
  * #### List - Lists Runs filtered on run status - querying the `admin/runs` endpoint
  ```bash
    $ tfectl admin run list --filter "plan_queued" --query '.[] | .id'
    [
        "run-4LuSKSss9KH2NAPN",
        "run-HCL7LVz67hVHEgsx",
        "run-ozEfahr1YrDQNokG",
        "run-hqWdU7BMuQPpqFrE",
        "run-BstJ5RJKFGmYnCni",
        "run-7WCMcDf8GZxYGqjN",
        "run-ZtzW7Xb5k6cfmgNK",
        "run-WC4q9Ec3vernx7Sc",
        "run-q9Lak8i1rzS5mXFU",
        "run-gbJyJAT89tzC2ziz",
        "run-MLMzcUuoSZL8Tz8C",
        "run-nbSKBf9CLRjPbj1q",
        "run-faqeyLU2VMBcHPJQ",
        "run-hB6RqJtY1SuGWsHF",
        "run-6HaUc4T31yZsENmC",
        "run-vPvYHNrjBCD6Y3ke",
        "run-ENdFcVpEp2AMLxNr",
        "run-kxgmgdReVzrVopVG"
    ]
  ```
  * #### Force-Cancel - Force cancels runIDs
  ```bash
    $ tfectl admin run force-cancel --ids run-UFaNv3rz5XnzPhCh
    [
        {
            "id": "run-UFaNv3rz5XnzPhCh",
            "workspace_id": "ws-ojAyfT3ar4oXt3eA",
            "workspace_name": "workspace-infrastructure-production",
            "status": "cancelling"
        }
    ]
  ```

### Policy
* Query policies in TFE/TFC

* #### List
  ```bash
    $ tfectl policy list --filter "production-tagging"
    [
      {
        "id": "pol-5Qgo4h2mp2z68u3N",
        "name": "production-tagging",
        "kind": "sentinel",
        "enforce": "hard-mandatory",
        "policy_set_count": 1
      }
    ]
  ```

### Build
* Using GNU Make
```bash
  make build
```

* Linux 
```bash
  $ make linux
```

* Windows
```bash
  $ make windows
```

## Contributing
* see `CONTRIBUTING.md`
