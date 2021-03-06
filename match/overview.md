# Overview
An pattern of match data contract like the fellow code, which is a data provider for dapp who need some match data. 
And the developer of match contract can decide which data is payable, and which is free used.
So you can provider some data to get Socs(Soc is the official token in Allsportschain), when Dapp used your data.

```
pragma solidity ^0.4.16;

        contract MatchData {

            address internal owner;

            modifier onlyOwner() {
                require(owner == msg.sender );
                _;
            }

            modifier idVaild(uint256 id) {
                require(id > 0 && id < matchs.length);
                _;
            }

            //data
            //string name;

            struct Match {
                string name;
                uint256 price;
                string desc;
                uint256 state;
                uint256 startTime;
                uint256 endTime;
                string  homeName;
                string  guestName;
                uint8   homeScore;
                uint8   guestScore;
            }

            Match[] private matchs;
            mapping (string => uint256) matchIndex; 

            constructor () public payable {
                owner = msg.sender;
            }

            function AddMatch(
                string name, 
                uint256 price, 
                string desc,
                uint256 state,
                uint256 startTime,
                uint256 endTime,
                string  homeName,
                string  guestName
            ) public onlyOwner returns (uint256) {
                require(matchIndex[name] == 0);//name is no-repeat
                matchs.push(Match(name,price,desc,state,startTime,endTime,homeName,guestName,0,0));
                uint256 index = matchs.length - 1;
                matchIndex[name] = index;
                return matchs.length - 1;
            }

            function GetMatchMaxId() public view returns (uint256) {
                return matchs.length - 1;
            }

            function GetMatchInfo(
                uint256 id
            ) public view idVaild(id) 
            returns (
                string name, 
                uint256 startTime, 
                uint256 endTime,
                string  homeName,
                string  guestName) {

                name = matchs[id].name;
                startTime = matchs[id].startTime;
                endTime = matchs[id].endTime;
                homeName = matchs[id].homeName;
                guestName = matchs[id].guestName;
            }

            function GetMatchInfo(
                string findName
            ) public view 
            returns (
                string, 
                uint256, 
                uint256,
                string, 
                string) {

                uint256 id = matchIndex[findName];
                return GetMatchInfo(id);
            }

            function GetMatchState(
                uint256 id
            ) public view idVaild(id) returns (uint256) {
                return matchs[id].state;
            }

            function GetMatchState(
                string name
            ) public view returns (uint256) {
                uint256 id = matchIndex[name];
                return GetMatchState(id);
            }

            function GetMatchPrice(
                uint256 id
            ) public view idVaild(id) returns (uint256) {
                return matchs[id].price;
            }

            function GetMatchPrice(
                string findName
            ) public view returns (uint256) {
                uint256 id = matchIndex[findName];
                return GetMatchPrice(id);
            }

            function GetMatchDesc(
                uint256 id
            ) public view idVaild(id) returns (string) {
                return matchs[id].desc;
            }

            function GetMatchDesc(
                string findName
            ) public view returns (string) {
                uint256 id = matchIndex[findName];
                return GetMatchDesc(id);
            }

            function GetMatchTime(
                uint256 id
            ) public view idVaild(id) returns (uint256 startTime, uint256 endTime) {
                startTime = matchs[id].startTime;
                endTime = matchs[id].endTime;
            }

            function GetMatchTime(
                string findName
            ) public view returns (uint256, uint256) {
                uint256 id = matchIndex[findName];
                return GetMatchTime(id);
            }

            function GetMatchScroe(
                uint256 id
            ) public payable idVaild(id)  returns  (uint256 state, uint8 homeScore, uint8 guestScore) {
                require(id < matchs.length);
                require(msg.value >= matchs[id].price);

                state = matchs[id].state;
                homeScore = matchs[id].homeScore;
                guestScore = matchs[id].guestScore;

                if (msg.value > matchs[id].price) {
                    address(msg.sender).transfer(msg.value - matchs[id].price);
                }
            }

            function GetMatchScroe(
                string findName
            ) public payable returns  (uint256 , uint8 , uint8 ) {
                uint256 id = matchIndex[findName];
                return GetMatchScroe(id);
            }

            function GetMatchInfoByOwner(
                uint256 id
            ) public view onlyOwner idVaild(id) 
            returns (
                string name, 
                uint256 price, 
                string desc, 
                uint256 state,
                string homeName,
                string guestName,
                uint8 homeScore,
                uint8 guestScore
            ){
                name = matchs[id].name;
                price = matchs[id].price;
                desc = matchs[id].desc;
                state = matchs[id].state;
                homeScore = matchs[id].homeScore;
                homeName  = matchs[id].homeName;
                guestScore = matchs[id].guestScore;
                guestName = matchs[id].guestName;
            }

            function SetMatchInfo(
                uint256 id, 
                uint256 price, 
                string desc, 
                uint256 state) public onlyOwner idVaild(id)
            {
                matchs[id].price = price;
                matchs[id].desc = desc;
                matchs[id].state = state;
            }

            function SetMatchInfo(
                string findName, 
                uint256 price, 
                string desc, 
                uint256 state) public onlyOwner
            {
                uint256 id = matchIndex[findName];
                SetMatchInfo(id,price,desc,state);
            }

            function SetMatchState(
                uint256 id, 
                uint256 state
            ) public onlyOwner idVaild(id) 
            {
                matchs[id].state = state;
            }

            function SetMatchState(
                string findName,
                uint256 state
            ) public onlyOwner
            {
                uint256 id = matchIndex[findName];
                SetMatchState(id, state);
            }

            function SetMatchScore(
                uint256 id, 
                uint8 homeScore,
                uint8 guestScore) public onlyOwner idVaild(id) {

                matchs[id].homeScore = homeScore;
                matchs[id].guestScore = guestScore;
            }

            function SetMatchScore(
                string findName,
                uint8 homeScore,
                uint8 guestScore) public onlyOwner {

                uint256 id = matchIndex[findName];
                SetMatchScore(id, homeScore, guestScore);
            }

            function SetMatchTime(
                uint256 id, 
                uint256 startTime,
                uint256 endTime) public onlyOwner idVaild(id) {

                matchs[id].startTime = startTime;
                matchs[id].endTime = endTime;
            }

            function SetMatchTime(
                string findName,
                uint256 startTime,
                uint256 endTime) public onlyOwner{

                uint256 id = matchIndex[findName];
                SetMatchTime(id, startTime, endTime);
            }

            function SetMatchPrice(
                uint256 id, 
                uint256 price) public onlyOwner idVaild(id) {
                matchs[id].price = price;
            }

            function SetMatchPrice(
                string findName,
                uint256 price) public onlyOwner {

                uint256 id = matchIndex[findName];
                SetMatchPrice(id, price);
            }

            function GetBalance() public view returns (uint256) {
                return address(this).balance;
            }

            function Draw(
                address drawer, 
                uint256 val
            ) public onlyOwner{
                if (val < address(this).balance) {
                    drawer.transfer(val);
                } else {
                    drawer.transfer(address(this).balance);
                }
            }

            function Draw(
                address drawer
            ) public onlyOwner{
                drawer.transfer(address(this).balance);
            }

            function () public payable {
            }
        }

```
