
# **Ganache-Truffle-Smart-Contract-Guide**  
**A comprehensive guide to writing, deploying, and testing Solidity smart contracts using Truffle and Ganache. This project uses a Vending Machine smart contract as a practical example to demonstrate the basics of Ethereum blockchain development.**

---

## **Table of Contents**  
1. [Introduction](#introduction)  
2. [Features](#features)  
3. [Prerequisites](#prerequisites)  
4. [Setup and Installation](#setup-and-installation)  
5. [Guide: Writing the Smart Contract](#guide-writing-the-smart-contract)  
6. [Deploying the Contract](#deploying-the-contract)  
7. [Interacting with the Contract](#interacting-with-the-contract)  
8. [Future Enhancements](#future-enhancements)  
9. [License](#license)  

---

## **Introduction**  
This project is designed for beginners interested in Ethereum smart contract development. It focuses on creating a basic **Vending Machine smart contract**, deploying it to a local blockchain using **Ganache**, and testing it with **Truffle**.  

---

## **Features**  
- A simple vending machine contract to manage donut balances.  
- Integration with Truffle for contract development, deployment, and testing.  
- Use of Ganache as a local Ethereum blockchain for testing.  

---

## **Prerequisites**  
Before you begin, ensure you have the following installed on your system:  
1. **Node.js** (v12 or higher): [Download Node.js](https://nodejs.org/)  
2. **Truffle** (latest version):  
   ```bash
   npm install -g truffle
   ```  
3. **Ganache** (GUI or CLI):  
   - GUI: [Download Ganache](https://trufflesuite.com/ganache/)  
   - CLI:  
     ```bash
     npm install -g ganache
     ```  
4. **A Code Editor**: Preferably **Visual Studio Code**.  

---

## **Setup and Installation**  

1. **Clone the Repository**:  
   ```bash
   git clone https://github.com/alikhizarsohal/ganache-truffle-smart-contract-guide.git
   cd ganache-truffle-smart-contract-guide
   ```  

2. **Install Dependencies**:  
   Navigate to the project directory and run:  
   ```bash
   npm install
   ```  

3. **Start Ganache**:  
   - **GUI**: Launch Ganache and ensure the RPC server is running on `http://127.0.0.1:7545`.  
   - **CLI**: Run:  
     ```bash
     ganache --port 7545
     ```  

4. **Configure Truffle**:  
   Ensure `truffle-config.js` is set to connect to Ganache:  
   ```javascript
   module.exports = {
     networks: {
       development: {
         host: "127.0.0.1",
         port: 7545,
         network_id: "*", // Match any network id
       },
     },
     compilers: {
       solc: {
         version: "0.8.11", // Specify the Solidity version
       },
     },
   };
   ```

---

## **Guide: Writing the Smart Contract**  

### **Vending Machine Contract**  
1. Navigate to the `contracts/` folder.  
2. Create a file named `VendingMachine.sol` and add the following code:  

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract VendingMachine {
    address public owner;
    mapping(address => uint) public donutBalances;

    uint public donutPrice = 2 ether;

    constructor() {
        owner = msg.sender;
        donutBalances[address(this)] = 100; // Initialize with 100 donuts
    }

    function purchaseDonut(uint amount) public payable {
        require(msg.value >= amount * donutPrice, "Not enough Ether sent.");
        require(donutBalances[address(this)] >= amount, "Not enough donuts in stock.");
        
        donutBalances[address(this)] -= amount;
        donutBalances[msg.sender] += amount;
    }

    function updateDonutPrice(uint newPrice) public {
        require(msg.sender == owner, "Only the owner can update the price.");
        donutPrice = newPrice;
    }

    function restock(uint amount) public {
        require(msg.sender == owner, "Only the owner can restock.");
        donutBalances[address(this)] += amount;
    }
}
```
## **Deploying the Contract**

1. **Create a Deployment Script**:  
   In the `migrations/` folder, create a new file named `2_deploy_contracts.js`:  
   ```javascript
   const VendingMachine = artifacts.require("VendingMachine");

   module.exports = function (deployer) {
     deployer.deploy(VendingMachine);
   };
   ```

2. **Compile the Contract**:  
   Run:  
   ```bash
   truffle compile
   ```

3. **Deploy the Contract**:  
   Run the following command to deploy the contract on the **development network** (as configured in `truffle-config.js`):  
   ```bash
   truffle migrate --network development
   ```

4. Verify the deployment on the Truffle console:  
   ```bash
   truffle console --network development
   ```
---

## **Interacting with the Contract**  

1. Open the Truffle console:  
   ```bash
   truffle console
   ```

2. Retrieve the deployed contract instance:  
   ```javascript
   const instance = await VendingMachine.deployed();
   ```

3. Check the initial donut stock:  
   ```javascript
   (await instance.donutBalances.call(instance.address)).toString();
   ```

4. Purchase donuts:  
   ```javascript
   await instance.purchaseDonut(5, { from: "your_account_address", value: web3.utils.toWei("10", "ether") });
   ```

5. Update the donut price (owner only):  
   ```javascript
   await instance.updateDonutPrice(web3.utils.toWei("3", "ether"), { from: "your_owner_address" });
   ```

---

## **Future Enhancements**  
1. Add a **frontend** using Web3.js to interact with the contract.  
2. Deploy the contract to a public Ethereum testnet (e.g., Goerli or Sepolia).  
3. Integrate with MetaMask for wallet-based interactions.  

---

## **License**  
This project is licensed under the MIT License.  

---
