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
 ✔ Created DAO: 0x9934E6c917675888D4E4747725A9d3c9B3f63AcD
```

*YOUR DAO ADDRESS:* 0x9934E6c917675888D4E4747725A9d3c9B3f63AcD

## Creating a Membership Token

```bash
dao token new "Member" "MBR" 0
 ✔ Deploy the MiniMeTokenFactory contract
 ✔ Deploy the MiniMeToken contract
 ✔ Successfully deployed the token at 0xEE1cD10767c26F92634DaA26cC639A4bbb192E10
 ℹ Token transaction hash: 0x0f34dc350eb0386d66c51539790d40bee980fa94c4eaafa0ae670e7486d899fa
 ✔ Successfully deployed the token factory at 0x9310dC480CbF907D6A3c41eF2e33B09E61605531
 ℹ Token factory transaction hash: 0x806eeace2af41f70f445e37ae01818103d327c659af2d0ac39389e533e9de6c6
```

*YOUR TOKEN ADDRESS:* 0xEE1cD10767c26F92634DaA26cC639A4bbb192E10

## Deploy the Token-Manager Instance

```bash
dao install <dao-address> token-manager --app-init none
```

```bash
dao install 0x9934E6c917675888D4E4747725A9d3c9B3f63AcD token-manager --app-init none --environment aragon:rinkeby
 ✔ Fetching token-manager.aragonpm.eth@latest
 ↓ Checking installed version [skipped]
   → Installing the first instance of token-manager.aragonpm.eth in DAO
 ✔ Deploying app instance
 ℹ Successfully executed: "Create a new upgradeable instance of 0x6b20a3010614eeebf2138ccec99f028a61c811b3b1a3343b6ff635985c75c91f app linked to the Kernel, setting its code to 0x3Ec5e6239A90332A13155F5d7382E0DB70C36A8a ."
 ⚠ After the app instance is created, you will need to assign permissions to it for it appear as an app in the DAO
 ⚠ App could not be initialized, check the --app-init flag. Functions protected behind the ACL will not work until the app is initialized
 ```

*YOUR TOKEN MANAGER ADDRESS:* 0x3Ec5e6239A90332A13155F5d7382E0DB70C36A8a

## Getting the token-manager address

```bash
dao apps <dao-address> --all
```

```bash
dao apps 0x9934E6c917675888D4E4747725A9d3c9B3f63AcD --all --environment aragon:rinkeby
 ✔ Inspecting DAO
 ✔ Fetching permissionless apps
 ✔ Successfully fetched DAO apps for 0x9934E6c917675888D4E4747725A9d3c9B3f63AcD
┌────────┬────────────────────────────────────────────┬───────────────────┐
│ App    │ Proxy address                              │ Content           │
├────────┼────────────────────────────────────────────┼───────────────────┤
│ kernel │ 0x9934E6c917675888D4E4747725A9d3c9B3f63AcD │ (No UI available) │
├────────┼────────────────────────────────────────────┼───────────────────┤
│ acl    │ 0x03ab5a63543bef42601b103ee92f26097066c029 │ (No UI available) │
├────────┼────────────────────────────────────────────┼───────────────────┤
│ evmreg │ 0x80325a68af95ec399739b8013af33202db8053c7 │ (No UI available) │
└────────┴────────────────────────────────────────────┴───────────────────┘
┌────────────────────┬────────────────────────────────────────────┐
│ Permissionless app │ Proxy address                              │
├────────────────────┼────────────────────────────────────────────┤
│ token-manager      │ 0xf14BeEDe08058f3E4D4F8Ae8038CfdC02C492112 │
└────────────────────┴────────────────────────────────────────────┘
```

## Setting the token-manager instances as the token-controller on our token

```bash
dao token change-controller <token-address> <token-manager-address>
```

```bash
dao token change-controller 0xEE1cD10767c26F92634DaA26cC639A4bbb192E10 0xf14BeEDe08058f3E4D4F8Ae8038CfdC02C492112 --environment aragon:rinkeby
 ✔ Changing the MiniMe token controller
 ✔ Successfully changed the controller of 0xEE1cD10767c26F92634DaA26cC639A4bbb192E10 to 0xf14BeEDe08058f3E4D4F8Ae8038CfdC02C492112
 ℹ Transaction hash: 0x7cbd117d411e35eb92d849d19f01c76ebba857f8e27707c560649cff1dd18f6b
```

## Create a permission for the token-manager

```bash
dao acl create <dao-address> <token-manager-address> MINT_ROLE <your-address> <your-address>
```

```bash
dao acl create 0x9934E6c917675888D4E4747725A9d3c9B3f63AcD 0xf14BeEDe08058f3E4D4F8Ae8038CfdC02C492112 MINT_ROLE 0xB5787893aF14f7F68ca54503bB2e23cf5b485951 0xe3D623E4b0902d3d0A578941527D7780849734d0 --environment aragon:rinkeby
 ✔ Generating transaction
 ✔ Sending transaction
 ✔ Successfully executed: "Create a new permission granting 0xB5787893aF14f7F68ca54503bB2e23cf5b485951 the ability to perform actions of role 0x154c00819833dac601ee5ddded6fda79d9d8b506b911b3dbd54cdb95fe6c3686 on 0xf14BeEDe08058f3E4D4F8Ae8038CfdC02C492112 (setting 0xe3D623E4b0902d3d0A578941527D7780849734d0 as the permission manager)"
```

## Initialize the token manager

```bash
dao exec <dao-address> <token-manager-address> initialize [token-address] false 1
```

```bash
dao exec 0x9934E6c917675888D4E4747725A9d3c9B3f63AcD 0xf14BeEDe08058f3E4D4F8Ae8038CfdC02C492112 initialize 0xEE1cD10767c26F92634DaA26cC639A4bbb192E10 false 1 --environment aragon:rinkeby
 ✔ Generating transaction
 ✖ Sending transaction
   → gas required exceeds allowance (7000000) or always failing transaction
 ✖ gas required exceeds allowance (7000000) or always failing transaction
```

## Go to the DAO

Find your DAO UI here: https://<network>.aragon.org/#/<DAO Address>

https://rinkeby.aragon.org/#/0x9934E6c917675888D4E4747725A9d3c9B3f63AcD/

# Adding a Voting instance

```bash
dao install <dao-address> voting --app-init-args [token-address] 600000000000000000 250000000000000000 604800
```

```bash
dao install 0x9934E6c917675888D4E4747725A9d3c9B3f63AcD voting --app-init-args 0xEE1cD10767c26F92634DaA26cC639A4bbb192E10 600000000000000000 250000000000000000 604800 --environment aragon:rinkeby
 ✔ Fetching voting.aragonpm.eth@latest
 ↓ Checking installed version [skipped]
   → Installing the first instance of voting.aragonpm.eth in DAO
 ✔ Deploying app instance
 ℹ Successfully executed: "Create a new upgradeable instance of 0x9fa3927f639745e587912d4b0fea7ef9013bf93fb907d29faeab57417ba6e1d4 app linked to the Kernel, setting its code to 0x8C06aEBF29F20A2e09b32F5d44cEa49Db3EC2eE0 ."
 ⚠ After the app instance is created, you will need to assign permissions to it for it appear as an app in the DAO
```

*YOUR VOTING ADDRESS:* 0x8C06aEBF29F20A2e09b32F5d44cEa49Db3EC2eE0

```bash
dao acl create <dao-address> <voting-app-address> CREATE_VOTES_ROLE <token-manager-address> <voting-app-address>
```

TO_COMPLETE

# Adding a Vault and Finance instance

```bash
dao install <dao-address> vault
```

```bash
dao install 0x9934E6c917675888D4E4747725A9d3c9B3f63AcD vault --environment aragon:rinkeby
 ✔ Fetching vault.aragonpm.eth@latest
 ↓ Checking installed version [skipped]
   → Installing the first instance of vault.aragonpm.eth in DAO
 ✔ Deploying app instance
 ℹ Successfully executed: "Create a new upgradeable instance of 0x7e852e0fcfce6551c13800f1e7476f982525c2b5277ba14b24339c68416336d1 app linked to the Kernel, setting its code to 0x31637C26Ab193cf12402F3FC5265ae916a1cFb89 ."
 ⚠ After the app instance is created, you will need to assign permissions to it for it appear as an app in the DAO
```

*YOUR VAULT-ADDRESS:* 0x31637C26Ab193cf12402F3FC5265ae916a1cFb89

```bash
dao install <dao-address> finance --app-init-args [vault-address] 2592000
```

```bash
dao install 0x9934E6c917675888D4E4747725A9d3c9B3f63AcD finance --app-init-args 0x31637C26Ab193cf12402F3FC5265ae916a1cFb89 2592000 --environment aragon:rinkeby
 ✔ Fetching finance.aragonpm.eth@latest
 ↓ Checking installed version [skipped]
   → Installing the first instance of finance.aragonpm.eth in DAO
 ✔ Deploying app instance
 ℹ Successfully executed: "Create a new upgradeable instance of 0xbf8491150dafc5dcaee5b861414dca922de09ccffa344964ae167212e8c673ae app linked to the Kernel, setting its code to 0x084Db627c7c7714Dc7BeFA8521cb93696DE84fF3 ."
 ⚠ After the app instance is created, you will need to assign permissions to it for it appear as an app in the DAO
```

*YOUR FINANCE ADDRESS:* 0x084Db627c7c7714Dc7BeFA8521cb93696DE84fF3

```bash
dao acl create <dao-address> <vault-address> TRANSFER_ROLE <finance-address> <voting-address>
```

```bash
dao acl create 0x9934E6c917675888D4E4747725A9d3c9B3f63AcD 0x31637C26Ab193cf12402F3FC5265ae916a1cFb89 TRANSFER_ROLE 0x084Db627c7c7714Dc7BeFA8521cb93696DE84fF3 0x8C06aEBF29F20A2e09b32F5d44cEa49Db3EC2eE0 --environment aragon:rinkeby
 ✔ Generating transaction
 ✔ Sending transaction
 ✔ Successfully executed: "Create a new permission granting 0x084Db627c7c7714Dc7BeFA8521cb93696DE84fF3 the ability to perform actions of role 0x8502233096d909befbda0999bb8ea2f3a6be3c138b9fbf003752a4c8bce86f6c on 0x31637C26Ab193cf12402F3FC5265ae916a1cFb89 (setting 0x8C06aEBF29F20A2e09b32F5d44cEa49Db3EC2eE0 as the permission manager)"
```

```bash
dao acl create <dao-address> <finance-address> CREATE_PAYMENTS_ROLE <voting-address> <voting-address>
```

```bash
dao acl create 0x9934E6c917675888D4E4747725A9d3c9B3f63AcD 0x084Db627c7c7714Dc7BeFA8521cb93696DE84fF3 CREATE_PAYMENTS_ROLE 0x8C06aEBF29F20A2e09b32F5d44cEa49Db3EC2eE0 0x8C06aEBF29F20A2e09b32F5d44cEa49Db3EC2eE0 --environment aragon:rinkeby
 ✔ Generating transaction
 ✔ Sending transaction
 ✔ Successfully executed: "Create a new permission granting 0x8C06aEBF29F20A2e09b32F5d44cEa49Db3EC2eE0 the ability to perform actions of role 0x5de467a460382d13defdc02aacddc9c7d6605d6d4e0b8bd2f70732cae8ea17bc on 0x084Db627c7c7714Dc7BeFA8521cb93696DE84fF3 (setting 0x8C06aEBF29F20A2e09b32F5d44cEa49Db3EC2eE0 as the permission manager)"
```

```bash
dao acl create <dao-address> <finance-address> EXECUTE_PAYMENTS_ROLE <voting-address> <voting-address>
```

```bash
dao acl create 0x9934E6c917675888D4E4747725A9d3c9B3f63AcD 0x084Db627c7c7714Dc7BeFA8521cb93696DE84fF3 EXECUTE_PAYMENTS_ROLE 0x8C06aEBF29F20A2e09b32F5d44cEa49Db3EC2eE0 0x8C06aEBF29F20A2e09b32F5d44cEa49Db3EC2eE0 --environment aragon:rinkeby
...
```

```bash
dao acl create <dao-address> <finance-address> MANAGE_PAYMENTS_ROLE <voting-address> <voting-address>
```

```bash
dao acl create 0x9934E6c917675888D4E4747725A9d3c9B3f63AcD 0x084Db627c7c7714Dc7BeFA8521cb93696DE84fF3 MANAGE_PAYMENTS_ROLE 0x8C06aEBF29F20A2e09b32F5d44cEa49Db3EC2eE0 0x8C06aEBF29F20A2e09b32F5d44cEa49Db3EC2eE0 --environment aragon:rinkeby
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

