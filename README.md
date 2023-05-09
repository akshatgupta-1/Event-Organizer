# Event-Organizer
Here is my solidity code for a project Event Organizer. In this a event can be created with event date , tickets available and many other attributes. Participants can buy the tickets and can transfer them to other addresses also


// SPDX-License-Identifier: MIT

pragma solidity ^0.8.0;

contract EventContract{

    struct Event{                   //creating a structure to store event details
        address organizer;
        string name;
        uint date;
        uint price;
        uint ticketCount;
        uint ticketRemain;
    }

    mapping(uint => Event) public events;        //mapping event id with event struct
    mapping(address => mapping(uint => uint)) public tickets;   
    //mapping address of participant with event id and number of tickets

    uint public nextId;    //Event id number 

    function createEvent(string memory name, uint date, uint price, uint ticketCount) external{
        require(date>block.timestamp,"You can organize event for future date");
        //checking if date of event ct=reated is more than current block timestamp

        require(ticketCount>0,"You can organize event only with more than 0 tickets");   
        //checking if more than 1 ticket created 


        events[nextId] = Event(msg.sender, name, date, price,ticketCount,ticketCount);
        //creating event
        nextId++;
        
    }

    function buyTicket(uint id , uint quantity) external payable{
        require(events[id].date!=0,"This event does not exist");
        //checking if event is created or not 

        require(block.timestamp<events[id].date,"Event already over");
        //checking if event is already over or not

        Event storage _event = events[id];
        require(msg.value == (_event.price*quantity),"Not enough ether");
        //checking if ether tranfer is equal to expected value

        require(_event.ticketRemain>=quantity,"Not enough tickets available");
        //checking if number of tickets are available as per request or not

        _event.ticketRemain-=quantity;

        tickets[msg.sender][id] += quantity;
        //transfering tickets to participant

    }

    function transferTicket(uint id , uint quantity , address to) external{

        require(events[id].date!=0,"This event does not exist");
        //checking if event is created or not 

        require(block.timestamp<events[id].date,"Event already over");
        //checking if event is already over or not

        require(tickets[msg.sender][id]>=quantity,"You do not have enough tickets");
        //checking if particiant has enough tickets to transfer 

        tickets[msg.sender][id] -= quantity;
        tickets[to][id] += quantity;            //transfering tickets


    }

}
