# Map of Zones description #


**Map of Zones demo video:** https://www.youtube.com/watch?v=Q30mDD2N3UY

**Map of Zones website:** https://mapofzones.com/


Map of Zones is a visualizer that displays a galaxy of the interconnected networks built on cosmos-SDK with IBC module. It connects to public RPCs provided by individual networks and parses all of the IBC-transactions that have token transfers in it.
We will provide the MoZ overall description below as well as the description of the modules we have been actively working on: txs-processor and cosmos-watcher
***Note*** : This repository was created for the cross-chain hackathon results estimation convenience, the txs-processor and cosmos-watcher further updates arising in the txs-processor and cosmos-watcher repositories (https://github.com/mapofzones/txs-processor and https://github.com/mapofzones/cosmos-watcher).


***There are 3 main components: aggregated global data dashboard, the map and the rating table.***


**Dashboard:**

![alt text](https://github.com/mapofzones/mapofzones-crosschain-hackathon/blob/master/resources/dashboard.png?raw=true)

It includes aggregated data of the:
- number of overall IBC token transfers across all of the chains
- number of zones (overall and active)
- number of channels (overall and active)
- the most active pair of zones according to the number of the IBC transfers they sent to each other

**Map:** 

![alt text](https://github.com/mapofzones/mapofzones-crosschain-hackathon/blob/master/resources/map.jpg?raw=true)

Each zone is displayed as a circle with animated rays that represent active connection channels. We used different circle sizes to highlight zones that are more ”active” in terms of number of IBC token transfers or transactions (chosen column). Color coding is used to display zones’ the ratio of incoming/outgoing IBC token transfer transactions. 
Map could be zoomed in and zones could be selected in order to interact with zones that are of a higher interest to you.



**Rating Table:**

![alt text](https://github.com/mapofzones/mapofzones-crosschain-hackathon/blob/master/resources/rating_table.png?raw=true)

Rating table consist of 8 columns:
- a certain zone name
- number of IBC transfers a certain zone sent 
- number of total TXs a certain zone sent
- IBC share (%) - number of TXs having IBC messages to the total number of transactions in a certain zone
- IBC out - sending tokens in IBC transfer
- IBC in - receiving tokens in IBC transfer
- number of channels
- IBC TXs activity chart



# TXs-Processor 


### General
The MoZ processor is a standalone process that listens to a queue for new blocks. There can be multiple processors. In this case, every processor must listen to his own queue. 
 
### Responsiblities
The processor gets performs the following functions:
* get a new block from the queue,
* recognize a type of the messages,
* process each message according to its type. For example, it can be an updating of the MoZ stats in case of an ibc transfer or adding a new record into the database if it's an ibc init message,
* update the database with the latest processed block number
 
### Possible errors
The processor will reject a new block if it has wrong block number (higher, or lower than expected)
 
 


#Cosmos-watcher

### General
The MoZ watcher is a standalone process that takes 2 input arguments: 
* a zone RPC address, 
* a starting block number, 

and listens to the given zone starting from the given block number.

### Responsibilies
The watcher listens to the new blocks, parses them, and assembly the information into the zone-neutral data structures.
```
block {
   chain_id: <string>, the zone chain id
   block_time: <timestamp> 
   block_num: <number>
   txs: array [transaction]
}

transaction {
   hash: <string>
   msgs: array [message]
}

message {
   transfer_info: {
     sender: <address>
     recipient: <address>
     quantity: <int>
     precision: <smallint>
     token: <code>
   }
   type: (send | receive | open_channel | open_connection | open_client | unknown)
   ibc: true | false
   ibc_channel_id: <string>
   ibc_connection_id: <string>
   ibc_client_id: <string>
}
```

the newly created object of the ```block``` type is sent to the queue.



