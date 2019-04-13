# Reference

https://hack.aragon.org/docs/guides-custom-deploy

# General considerations

For any deploy always remember to have in a separate terminal the IPFS daemon running, use aragon ipfs to start it.

If you are testing locally:

In a separate terminal run: aragon devchain
If you are deploying to rinkeby:

Add --environment aragon:rinkeby to each of these commands
If you are deploying to mainnet:

Add --environment aragon:mainnet to each of these commands

The following is using the rinkeby environement.

# Install the Aragon CLI

```bash
npm install -g @aragon/cli
```

# Creating a blank organization

## Create the DAO

```bash
dao new --environment aragon:rinkeby
 ✔ Fetching template bare-kit.aragonpm.eth@latest
 ✔ Create new DAO from template
 ✔ Checking DAO
 ✔ Created DAO: 0x39044aA1C2A8F5D060502cF0340eFa8d0fdDbf07
```

*YOUR DAO ADDRESS:* 0x39044aA1C2A8F5D060502cF0340eFa8d0fdDbf07

```bash
export ARAGON_DAO_ADDRESS=0x39044aA1C2A8F5D060502cF0340eFa8d0fdDbf07
``

## Creating a Membership Token

```bash
dao token new "Member" "MBR" 0 --environment aragon:rinkeby
 ✔ Deploy the MiniMeTokenFactory contract
 ✔ Deploy the MiniMeToken contract
 ✔ Successfully deployed the token at 0xc592A9C6Fa048e0459C0433133bc6d40FcD68068
 ℹ Token transaction hash: 0x4ff5b0f5c90df2282af1eee266ccf0530c17305700a69d20d79ada7ab8d59363
 ✔ Successfully deployed the token factory at 0x5c6A795C42a041Ebfd7Fb2AEB560b8D56351f479
 ℹ Token factory transaction hash: 0x1b727af2dacf1bf83612d93ded107f648b2b4affd639c8c8fc7df4cd6e0ba2ac
```

*YOUR TOKEN ADDRESS:* 0xc592A9C6Fa048e0459C0433133bc6d40FcD68068

```bash
export ARAGON_TOKEN_ADDRESS=0xc592A9C6Fa048e0459C0433133bc6d40FcD68068
```

## Deploy the Token-Manager Instance

```bash
dao install <dao-address> token-manager --app-init none
```

```bash
dao install $ARAGON_DAO_ADDRESS token-manager --app-init none --environment aragon:rinkeby
 ✔ Fetching token-manager.aragonpm.eth@latest
 ↓ Checking installed version [skipped]
   → Installing the first instance of token-manager.aragonpm.eth in DAO
 ✔ Deploying app instance
 ℹ Successfully executed: "Create a new upgradeable instance of 0x6b20a3010614eeebf2138ccec99f028a61c811b3b1a3343b6ff635985c75c91f app linked to the Kernel, setting its code to 0x3Ec5e6239A90332A13155F5d7382E0DB70C36A8a ."
 ⚠ After the app instance is created, you will need to assign permissions to it for it appear as an app in the DAO
 ⚠ App could not be initialized, check the --app-init flag. Functions protected behind the ACL will not work until the app is initialized
 ```

## Getting the token-manager address

```bash
dao apps <dao-address> --all
```

```bash
dao apps $ARAGON_DAO_ADDRESS --all --environment aragon:rinkeby
 ✔ Inspecting DAO
 ✔ Fetching permissionless apps
 ✔ Successfully fetched DAO apps for 0x39044aA1C2A8F5D060502cF0340eFa8d0fdDbf07
┌────────┬────────────────────────────────────────────┬───────────────────┐
│ App    │ Proxy address                              │ Content           │
├────────┼────────────────────────────────────────────┼───────────────────┤
│ kernel │ 0x39044aA1C2A8F5D060502cF0340eFa8d0fdDbf07 │ (No UI available) │
├────────┼────────────────────────────────────────────┼───────────────────┤
│ acl    │ 0x3b73f998f9c0b697231b0daf9920d9a2bc55a96e │ (No UI available) │
├────────┼────────────────────────────────────────────┼───────────────────┤
│ evmreg │ 0x70c55315e2e7217d5dc2974c8a0ef05c6d3bb6ba │ (No UI available) │
└────────┴────────────────────────────────────────────┴───────────────────┘
┌────────────────────┬────────────────────────────────────────────┐
│ Permissionless app │ Proxy address                              │
├────────────────────┼────────────────────────────────────────────┤
│ token-manager      │ 0x829923fb777Cf60518b4178604124B6620d3aEC4 │
└────────────────────┴────────────────────────────────────────────┘
```

*YOUR TOKEN MANAGER ADDRESS:* 0x829923fb777Cf60518b4178604124B6620d3aEC4

```bash
export ARAGON_TOKEN_MANAGER=0x829923fb777Cf60518b4178604124B6620d3aEC4
```

## Setting the token-manager instances as the token-controller on our token

```bash
dao token change-controller <token-address> <token-manager-address>
```

```bash
dao token change-controller $ARAGON_TOKEN_ADDRESS $ARAGON_TOKEN_MANAGER --environment aragon:rinkeby
 ✔ Changing the MiniMe token controller
 ✔ Successfully changed the controller of 0xc592A9C6Fa048e0459C0433133bc6d40FcD68068 to 0x829923fb777Cf60518b4178604124B6620d3aEC4
 ℹ Transaction hash: 0xb58bbd17ea28bfaa138a4dc14a8cd700f08a928442d6f7f2107a5c8ca2b78617
 ```

## Create a permission for the token-manager

```bash
dao acl create <dao-address> <token-manager-address> MINT_ROLE <your-address> <your-address>
```

```bash
dao acl create $ARAGON_DAO_ADDRESS $ARAGON_TOKEN_MANAGER MINT_ROLE 0xB5787893aF14f7F68ca54503bB2e23cf5b485951 0xe3D623E4b0902d3d0A578941527D7780849734d0 --environment aragon:rinkeby
 ✔ Generating transaction
 ✔ Sending transaction
 ✔ Successfully executed: "Create a new permission granting 0xB5787893aF14f7F68ca54503bB2e23cf5b485951 the ability to perform actions of role 0x154c00819833dac601ee5ddded6fda79d9d8b506b911b3dbd54cdb95fe6c3686 on 0x829923fb777Cf60518b4178604124B6620d3aEC4 (setting 0xe3D623E4b0902d3d0A578941527D7780849734d0 as the permission manager)"
 ```

## Initialize the token manager

```bash
dao exec <dao-address> <token-manager-address> initialize [token-address] false 1
```

```bash
dao exec $ARAGON_DAO_ADDRESS $ARAGON_TOKEN_MANAGER initialize $ARAGON_TOKEN_ADDRESS false 1 --environment aragon:rinkeby
 ✔ Generating transaction
 ✔ Sending transaction
 ✔ Successfully executed: "undefined"
```

## Go to the DAO

Find your DAO UI here: https://<network>.aragon.org/#/<DAO Address>

https://rinkeby.aragon.org/#/0x9934E6c917675888D4E4747725A9d3c9B3f63AcD/

# Adding a Voting instance

```bash
dao install <dao-address> voting --app-init-args [token-address] 600000000000000000 250000000000000000 604800
```

```bash
dao install $ARAGON_DAO_ADDRESS voting --app-init-args $ARAGON_TOKEN_ADDRESS 600000000000000000 250000000000000000 604800 --environment aragon:rinkeby
 ✔ Fetching voting.aragonpm.eth@latest
 ↓ Checking installed version [skipped]
   → Installing the first instance of voting.aragonpm.eth in DAO
 ✔ Deploying app instance
 ℹ Successfully executed: "Create a new upgradeable instance of 0x9fa3927f639745e587912d4b0fea7ef9013bf93fb907d29faeab57417ba6e1d4 app linked to the Kernel, setting its code to 0x8C06aEBF29F20A2e09b32F5d44cEa49Db3EC2eE0 ."
 ⚠ After the app instance is created, you will need to assign permissions to it for it appear as an app in the DAO
```

*YOUR VOTING APP ADDRESS:* 0x8C06aEBF29F20A2e09b32F5d44cEa49Db3EC2eE0

```bash
export ARAGON_VOTING_APP_ADDRESS=0x8C06aEBF29F20A2e09b32F5d44cEa49Db3EC2eE0
```

```bash
dao acl create <dao-address> <voting-app-address> CREATE_VOTES_ROLE <token-manager-address> <voting-app-address>
```

```bash
dao acl create $ARAGON_DAO_ADDRESS $ARAGON_VOTING_APP_ADDRESS CREATE_VOTES_ROLE $ARAGON_TOKEN_MANAGER $ARAGON_VOTING_APP_ADDRESS --environment aragon:rinkeby
 ✔ Generating transaction
 ✔ Sending transaction
 ✔ Successfully executed: "Create a new permission granting 'Token Manager' the ability to perform actions of role 0xe7dcd7275292e064d090fbc5f3bd7995be23b502c1fed5cd94cfddbbdcd32bbc on 0x8C06aEBF29F20A2e09b32F5d44cEa49Db3EC2eE0 (setting 0x8C06aEBF29F20A2e09b32F5d44cEa49Db3EC2eE0 as the permission manager)"
```

# Adding a Vault and Finance instance

```bash
dao install <dao-address> vault
```

```bash
dao install $ARAGON_DAO_ADDRESS vault --environment aragon:rinkeby
 ✔ Fetching vault.aragonpm.eth@latest
 ↓ Checking installed version [skipped]
   → Installing the first instance of vault.aragonpm.eth in DAO
 ✔ Deploying app instance
 ℹ Successfully executed: "Create a new upgradeable instance of 0x7e852e0fcfce6551c13800f1e7476f982525c2b5277ba14b24339c68416336d1 app linked to the Kernel, setting its code to 0x31637C26Ab193cf12402F3FC5265ae916a1cFb89 ."
 ⚠ After the app instance is created, you will need to assign permissions to it for it appear as an app in the DAO
```

*YOUR VAULT-ADDRESS:* 0x31637C26Ab193cf12402F3FC5265ae916a1cFb89

```bash
export ARAGON_VAULT_ADDRESS=0x31637C26Ab193cf12402F3FC5265ae916a1cFb89
```

```bash
dao install <dao-address> finance --app-init-args [vault-address] 2592000
```

```bash
dao install $ARAGON_DAO_ADDRESS finance --app-init-args $ARAGON_VAULT_ADDRESS 2592000 --environment aragon:rinkeby
 ✔ Fetching finance.aragonpm.eth@latest
 ↓ Checking installed version [skipped]
   → Installing the first instance of finance.aragonpm.eth in DAO
 ✔ Deploying app instance
 ℹ Successfully executed: "Create a new upgradeable instance of 0xbf8491150dafc5dcaee5b861414dca922de09ccffa344964ae167212e8c673ae app linked to the Kernel, setting its code to 0x084Db627c7c7714Dc7BeFA8521cb93696DE84fF3 ."
 ⚠ After the app instance is created, you will need to assign permissions to it for it appear as an app in the DAO
```

*YOUR FINANCE ADDRESS:* 0x084Db627c7c7714Dc7BeFA8521cb93696DE84fF3

```bash
export ARAGON_FINANCE_ADDRESS=0x084Db627c7c7714Dc7BeFA8521cb93696DE84fF3
```

```bash
dao acl create <dao-address> <vault-address> TRANSFER_ROLE <finance-address> <voting-address>
```

```bash
dao acl create $ARAGON_DAO_ADDRESS $ARAGON_VAULT_ADDRESS TRANSFER_ROLE $ARAGON_FINANCE_ADDRESS $ARAGON_VOTING_APP_ADDRESS --environment aragon:rinkeby
 ✔ Generating transaction
 ✔ Sending transaction
 ✔ Successfully executed: "Create a new permission granting 0x084Db627c7c7714Dc7BeFA8521cb93696DE84fF3 the ability to perform actions of role 0x8502233096d909befbda0999bb8ea2f3a6be3c138b9fbf003752a4c8bce86f6c on 0x31637C26Ab193cf12402F3FC5265ae916a1cFb89 (setting 0x8C06aEBF29F20A2e09b32F5d44cEa49Db3EC2eE0 as the permission manager)"
```

```bash
dao acl create <dao-address> <finance-address> CREATE_PAYMENTS_ROLE <voting-address> <voting-address>
```

```bash
dao acl create $ARAGON_DAO_ADDRESS $ARAGON_FINANCE_ADDRESS CREATE_PAYMENTS_ROLE $ARAGON_VOTING_APP_ADDRESS $ARAGON_VOTING_APP_ADDRESS --environment aragon:rinkeby
 ✔ Generating transaction
 ✔ Sending transaction
 ✔ Successfully executed: "Create a new permission granting 0x8C06aEBF29F20A2e09b32F5d44cEa49Db3EC2eE0 the ability to perform actions of role 0x5de467a460382d13defdc02aacddc9c7d6605d6d4e0b8bd2f70732cae8ea17bc on 0x084Db627c7c7714Dc7BeFA8521cb93696DE84fF3 (setting 0x8C06aEBF29F20A2e09b32F5d44cEa49Db3EC2eE0 as the permission manager)"
```

```bash
dao acl create <dao-address> <finance-address> EXECUTE_PAYMENTS_ROLE <voting-address> <voting-address>
```

```bash
dao acl create $ARAGON_DAO_ADDRESS $ARAGON_FINANCE_ADDRESS EXECUTE_PAYMENTS_ROLE $ARAGON_VOTING_APP_ADDRESS $ARAGON_VOTING_APP_ADDRESS --environment aragon:rinkeby
 ✔ Generating transaction
 ✔ Sending transaction
 ✔ Successfully executed: "Create a new permission granting 0x8C06aEBF29F20A2e09b32F5d44cEa49Db3EC2eE0 the ability to perform actions of role 0x563165d3eae48bcb0a092543ca070d989169c98357e9a1b324ec5da44bab75fd on 0x084Db627c7c7714Dc7BeFA8521cb93696DE84fF3 (setting 0x8C06aEBF29F20A2e09b32F5d44cEa49Db3EC2eE0 as the permission manager)"
```

```bash
dao acl create <dao-address> <finance-address> MANAGE_PAYMENTS_ROLE <voting-address> <voting-address>
```

```bash
dao acl create $ARAGON_DAO_ADDRESS $ARAGON_FINANCE_ADDRESS MANAGE_PAYMENTS_ROLE $ARAGON_VOTING_APP_ADDRESS $ARAGON_VOTING_APP_ADDRESS --environment aragon:rinkeby
 ✔ Generating transaction
 ✔ Sending transaction
 ✔ Successfully executed: "Create a new permission granting 0x8C06aEBF29F20A2e09b32F5d44cEa49Db3EC2eE0 the ability to perform actions of role 0x30597dd103acfaef0649675953d9cb22faadab7e9d9ed57acc1c429d04b80777 on 0x084Db627c7c7714Dc7BeFA8521cb93696DE84fF3 (setting 0x8C06aEBF29F20A2e09b32F5d44cEa49Db3EC2eE0 as the permission manager)"
```

# Review & Finalize Permissions

```bash
dao acl <dao-address>
```

```bash
dao acl 0x9934E6c917675888D4E4747725A9d3c9B3f63AcD --environment aragon:rinkeby
 ✔ Inspecting DAO Permissions
 ✔ Successfully fetched DAO apps for 0x9934E6c917675888D4E4747725A9d3c9B3f63AcD
┌────────────────────────┬─────────────────────────┬────────────────────────┬───────────────────────┐
│ App                    │ Action                  │ Allowed entities       │ Manager               │
├────────────────────────┼─────────────────────────┼────────────────────────┼───────────────────────┤
│ acl (0x03ab)           │ CREATE_PERMISSIONS_ROLE │ ✅ 0xb4124cEB345163... │   0xb4124cEB345163... │
├────────────────────────┼─────────────────────────┼────────────────────────┼───────────────────────┤
│ 0x9934e6c9176758...    │ APP_MANAGER_ROLE        │ ✅ 0xb4124cEB345163... │   0xb4124cEB345163... │
├────────────────────────┼─────────────────────────┼────────────────────────┼───────────────────────┤
│ token-manager (0xf14b) │ MINT_ROLE               │ ✅ 0xB5787893aF14f7... │   0xe3D623E4b0902d... │
├────────────────────────┼─────────────────────────┼────────────────────────┼───────────────────────┤
│ 0x31637c26ab193c...    │ TRANSFER_ROLE           │ ✅ 0x084Db627c7c771... │   0x8C06aEBF29F20A... │
├────────────────────────┼─────────────────────────┼────────────────────────┼───────────────────────┤
│ 0x084db627c7c771...    │ CREATE_PAYMENTS_ROLE    │ ✅ 0x8C06aEBF29F20A... │   0x8C06aEBF29F20A... │
├────────────────────────┼─────────────────────────┼────────────────────────┼───────────────────────┤
│ 0x084db627c7c771...    │ EXECUTE_PAYMENTS_ROLE   │ ✅ 0x8C06aEBF29F20A... │   0x8C06aEBF29F20A... │
├────────────────────────┼─────────────────────────┼────────────────────────┼───────────────────────┤
│ 0x084db627c7c771...    │ MANAGE_PAYMENTS_ROLE    │ ✅ 0x8C06aEBF29F20A... │   0x8C06aEBF29F20A... │
└────────────────────────┴─────────────────────────┴────────────────────────┴───────────────────────┘
```

# Getting Apps of the DAO

```bash
aragon dao apps <dao-address>
```

```bash
aragon dao apps 0x9934E6c917675888D4E4747725A9d3c9B3f63AcD --environment aragon:rinkeby
 ✔ Inspecting DAO
 ✔ Successfully fetched DAO apps for 0x9934E6c917675888D4E4747725A9d3c9B3f63AcD
┌──────────────────────┬────────────────────────────────────────────┬─────────────────────────────────────────────────────┐
│ App                  │ Proxy address                              │ Content                                             │
├──────────────────────┼────────────────────────────────────────────┼─────────────────────────────────────────────────────┤
│ kernel               │ 0x9934E6c917675888D4E4747725A9d3c9B3f63AcD │ (No UI available)                                   │
├──────────────────────┼────────────────────────────────────────────┼─────────────────────────────────────────────────────┤
│ acl                  │ 0x03ab5a63543bef42601b103ee92f26097066c029 │ (No UI available)                                   │
├──────────────────────┼────────────────────────────────────────────┼─────────────────────────────────────────────────────┤
│ evmreg               │ 0x80325a68af95ec399739b8013af33202db8053c7 │ (No UI available)                                   │
├──────────────────────┼────────────────────────────────────────────┼─────────────────────────────────────────────────────┤
│ token-manager@v1.1.6 │ 0xf14beede08058f3e4d4f8ae8038cfdc02c492112 │ ipfs:QmNeEr4VTDFrMDVUR69xBA2Puy74CGwWtRzpvyAg2fuPpL │
└──────────────────────┴────────────────────────────────────────────┴─────────────────────────────────────────────────────┘
```
