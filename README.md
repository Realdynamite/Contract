// SPDX-License-Identifier: MIT
pragma solidity ^0.8.26;

contract SecureContract {
    address public owner;
    bool private locked;

    mapping(address => uint256) public balances;

    constructor() {
        owner = msg.sender;
        locked = false;
    }

    modifier onlyOwner() {
        require(msg.sender == owner, "Not the contract owner");
        _;
    }

    modifier noReentrancy() {
        require(!locked, "No reentrancy allowed");
        locked = true;
        _;
        locked = false;
    }

    function deposit() external payable {
        balances[msg.sender] += msg.value;
    }

    function withdraw(uint256 amount) external noReentrancy {
        require(balances[msg.sender] >= amount, "Insufficient balance");
        balances[msg.sender] -= amount;
        payable(msg.sender).transfer(amount);
    }
}
