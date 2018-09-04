# Ethereum-Smart-Contract-Web3j-Spring-Boot

this project is : 2 applications decentralized communicate together using a smart contrat : a seller deploy this contract (create an instance of it) with the buyer address (to pass, delivery, pay, send invoices and confirm receiving orders)


 For compiling the contract MyDeal.sol :
 Solc is Solidity Compiler :

# $ solc MyDeal.sol --bin --abi --optimize -o <output-directory>

 This command creates two files: a binary .bin file, which is the smart contract code in a format the EVM can interpret, 
 and an application binary interface .abi file, which defines the smart contract methods.

 Now we have to generate source code from compiled contract using executable file provided together with Web3j library. 
 It is available under directory ${WEB3J_HOME}/bin. 
 When generating source code using Web3j, we should pass location of .bin and .abi files, then set target package name and directory. 

# $ ./web3j solidity generate <Path-directory>/MyDeal.bin <Path-directory>/MyDeal.abi -p <Package-Name> -o <Output-Directory>

 Now we hava a ava source file with Solidity contract name inside a given package. 

 replace the java file found under  : vente-achat/acheteur/src/main/java/com/sofrecom/stage/acheteur/model
                                 and : vente-achat/com.sofrecom.stage/src/main/java/com/sofrecom/stage/com/sofrecom/stage/model
                                 
# Test Scenario : 
 To run the test scenario, we need to have launched:

    Ethereum node in development on Docker container
    Ethereum Geth console client on Docker container
    The instance of vendeur application, by default available on port 8090
    The instance of acheteur application, by default available on port 8091

 Running Ethereum locally : 
 we can easily run Geth node locally using Docker container. 
 By default it connects the node to the Ethereum main network. 
 Alternatively, you can connect it to test network or Rinkeby network. 
 But the best option for beginning is just to run it in development mode by setting --dev parameter on Docker container running command.
 Here’s the command that starts Docker container in development mode and exposes Ethereum RPC API on port 8545.

# $ docker run -d --name ethereum -p 8545:8545 -p 30303:30303 ethereum/client-go --rpc --rpcaddr "0.0.0.0" --rpcapi="db,eth,net,web3,personal" --rpccorsdomain "*" --dev

 In development mode we have plenty of Ethers on your default test account. So we don’t have to mine any Ethers to be able to start tests. 
 Now we need to run Geth’s interactive JavaScript console inside Docker container.

# $ docker exec -it ethereum geth attach ipc:/tmp/geth.ipc

 The next step is to create a contract using the owner account (seller) created automatically by seller application on startup. 
 We should call the Get /contract method (we need the address of buyer account , created automatically by buyer application,  which is tooked , in this case, by the seller application using a rest controller)

# $ curl -X GET -H "Content-Type: application/json" http://localhost:8090/contract

# If contract has been successfully deployed, we have to send some orders by calling endpoint POST /order exposed by buyer application

# $ curl -X POST -H "Content-Type: application/json" -d '{"goods":"voiture", "quantity":1}' http://localhost:8091/order

 after sending price of order by seller, the buyer should send a safePayement to the contract :

# $ curl -X GET -H "Content-Type: application/json" http://localhost:8091/order/sendSafePayement/{order-id}

 after sending delivery by seller to buyer, he should wait for confirmation by buyer to have his ethers into his balance.
 so buyer, when he get his order finally, he should confirm that to transact ethers from contract to seller account

# $ curl -X POST -H "Content-Type: application/json" -d '01-01-2018 11:00:00' http://localhost:8091/order/confirm/{order-id}







