# Hackathn SBRC

This repo contains the code develop in the SBRC24 hackathon. The __dApp__ contains the ideia of the descentralized market. The __ecr20-power-token__ deploys a ECR20 contract that represents the power credits of the application, making 1k credits available to use.

To run the local blockchain, one must: 
- follow this [tutorial](https://docs.cartesi.io/cartesi-rollups/1.3/quickstart/) to setup the cartesi environment.
- After the setup is done, on the __dApp__, onem must build the application with the command `cartesi build`.
- Once build is ready, executes the dApp with `cartesi run`. It will up the local server.

The example developed in the hackathon aims to simulate a descentralized market where users can sell and buy energy credits - receveid by the power distribution company whenever the user sells its surplus energy from solar panels to the distribution company. To simulate this marker we use the library [Wallet](https://github.com/jplgarcia/python-wallet/), where a user has ether (a.k.a money :p) and solar tokens, a [ECR20](https://www.investopedia.com/news/what-erc20-and-what-does-it-mean-ethereum/) contract to simulate our credits in the blockchain.

To simulate the transaction, one must follow the steps:
- Once the server is running, follow the instructions on the __ecr20-power-token__ to deploy our power credits.
- To make transactions we must certify that the users have money and solar credits. To do so we start add ether to one account and power credits to another, to do it one must use the cartesi cli:
    * Run `cartesi send` and follow the steps __#TODO specify the steps__ to add ethers to an account
    * Run `cartesi send` and follow the steps __#TODO specify the steps__ to add ecr20(power credits) to another account 
- Run `cartesi send` and follow the steps __#TODO specify the steps__ to transfer the solar credits. It can be done by sending a generic string as a json in the followin format:
    ```json
        {
            "method": "erc20_transfer",
            "from" : "account_address_from",
            "to": "account_address_to",
            "erc20": "power_token_address",
            "amount": 500,
            "value_per_token": 0.0001
        }
The __from__ and __to__ are default address generated by the application. One must choose btw the available addresses when running the Cartesi CLI (`cartesi send`). The __ecr20__ is the adress of the token. It's generated in the token deploy. The __amount__ of power tokens will be transfered at a price of __value_per_token__ per token. When a transfer is made from __from__ to __to__, we made a transfer of ethers from __to__ to __from__, based on the agreed value.


After each transaction is possible to check the blockchain states with graphql queries,
available at localhost:8080/graphql. A few usefull queries can be found [here](https://github.com/prototyp3-dev/cartesi-client/blob/main/graphql%2Fqueries.graphql). 

To check all notices (when a transaction runs with no further problems) and reports (when a transaction is not done due to some error) run the following queries
```graphql
query notices {
    notices {
        edges {
            node {
                index
                input {
                    index
                    timestamp
                    msgSender
                    blockNumber
                }
                payload
            }
        }
    }
}

query reports {
    reports {
        edges {
            node {
                index
                input {
                    index
                    status
                    timestamp
                    msgSender
                    blockNumber
                }
                payload
            }
        }
    }
}


```

One can also check the wallet content with the endpoints bellow:

```
ether:
localhost:8080/inspect/balance/ether/{wallet_account}

power_token:
localhost:8080/inspect/balance/erc20/{wallet_account}/{power_token_address}
```