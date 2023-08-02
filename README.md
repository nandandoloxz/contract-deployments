# contract-deployments

This repo contains execution code and artifacts related to Base contract deployments.
For actual contract implementations, see [base-org/contracts](https://github.com/base-org/contracts).

## Setup

First install forge if you don't have it already:
* Run `make install-foundry` to install [`Foundry`](https://github.com/foundry-rs/foundry/commit/3b1129b5bc43ba22a9bcf4e4323c5a9df0023140).

To execute a new task run one of the following commands (depending on the type of change you're making):
* For incident response commands: `make setup-incident network=<network> incident=<incident-name>`
* For full new deployment (of L1 contracts related to Base): `make setup-deploy network=<network>`
* For contract calls, upgrades, or one-off contract deployments: `make setup-task network=<network> task=<task-name>`

Next, `cd` into the directory that was created for you and follow the steps listed below for the relevant template.

## Using the incident response template

This is set up as a separate template to increase efficiency when responding to incidents.

To use template during an incident:
1. Fill in TODOs in the relevant script.
1. Fill in dependency commit values in the `.env` file.
1. Delete the other scripts that are not being used so that you don't run into build issues.
1. Check in code once addresses have been filled in.
1. Have each signer pull the branch, and run relevant signing command from the Makefile.

To add new incident response scripts:
1. Any incident response related scripts should be included in this template (should be generic, not specific to network), with specific TODOs wherever addresses or other details need to be filled in.
1. Add the relevant make commands that would need to be run for the script to the template Makefile
1. Add relevant mainnet addresses in comments to increase efficiency responding to an incident.

## Using the deploy template

* Specify the commit of [Optimism code](https://github.com/ethereum-optimism/optimism) and [Base contracts code](https://github.com/base-org/contracts)  you intend to use in the `.env` file
* Run `make deps`
* Fill in the `inputs/deploy-config.json` and `inputs/misc-config.json` files with the input variables for the deployment.
* See the example `make deploy` command. Modifications may need to be made if you're using a key for deployment that you do not have the private key for (e.g. a hardware wallet)
* Run `make deploy` command
* Check in files to github. The files to ignore should already have been specified in the `.gitignore`, so you should be able to check in everything.


## Using the generic template

This template can be used to do contract calls, upgrades or one-off deployments.

* Specify the commit of [Optimism code](https://github.com/ethereum-optimism/optimism) and [Base contracts code](https://github.com/base-org/contracts)  you intend to use in the `.env` file
* Run `make deps`
* Put scripts in the `scripts` directory (see examples that are part of the template, for example there is a file `TransferOwner.s.sol`, which can be used to setup the ownership transfer task). See note below if running a task that requires a multisig to sign.
* Call scripts from the Makefile (see examples in the template Makefile that's copied over).

Note: If using a multisig as a signer you can leverage the `MultisigBuilder` for standard multisigs or the `NestedMultisigBuilder` scripts in the [base contracts repo](https://github.com/base-org/contracts/tree/main/script/universal) for multisigs that contain other multisigs as signers. For these scripts, you need to implement the virtual functions that are provided, which will allow you to configure the task you'd like to run, the address of the multisig you're running it from and any post execution checks. See the files themselves for more details. It is recommended to add any configured addresses as constants at the top of the file and provide links to etherscan, etc as needed for reviewers to more accurately review the PR.
### Directory structure

* **[inputs/](/inputs)**  any input json files
* **[records/](/records)** foundry will autogenerate files here from running commands
* **[script/](/script)** place to store any one-off foundry scripts
* **[src/](/src)** place to store any one-off smart contracts (long lived contracts should go in [base-org/contracts](https://github.com/base-org/contracts))
* **.env** place to store env variables specific to this task

### Process

* Specify the commit of [Optimism code](https://github.com/ethereum-optimism/optimism) and [Base contracts code](https://github.com/base-org/contracts)  you intend to use in the `.env` file
* `make deps`
* See [forge script](https://book.getfoundry.sh/reference/forge/forge-script) documentation for how to use solidity scripts
* It's recommended to specify any addresses that are passed into scripts in the `.env` file, to make it easier for reviewers.
