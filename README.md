# IOTA Stresstest Data Sheet 

In preparation of the stresstest of the IOTA Tangle, the team has setup a central infrastructure to collect all the relevant data that is accumulated during the tests to make it possible to properly analyze the results and provide insights into how secure, reliable and scalable IOTA is. The central infrastructure  consists of several MySQL and Redis databases (more information about the architecture later).

The stresstest is setup in such a way that we can centrally control a large network of nodes, and through that send commands (C&C) to start spamming, increase spamming or start performing some of the predefined tests that were setup. Each node that is participating in the network is automatically connected to the database and logs the data on each event.  



---

## Table of Contents		

- **[Database Tables](#database-tables)**
    - **[spamStats](#spamStats)**
    - **[nodeStats](#nodeStats)**
    - **[heartbeat](#heartbeat)**
    - **[status](#status)**

---


## Database Tables 

In total, there are 4 tables that are most relevant to look closer at in order to have a full picture of the data that is being collected (and can then be analyzed):
- **`spamStats`**: Each node writes to this table, recording information about the most recent transaction that was made with the relevant datapoints for each. 
- **`nodeStats`**: Central controller writes heartbeat data to nodeStats, which includes number of neighbors, spamCount and others. 
- **`heartbeat`**: Everytime a heartbeat request is sent, each node records getNodeInfo and neighbors data here.
- **`status`**: Table with full overview of what each node is doing right now (spamming, value spamming, or idle).

---

### `spamStats` 

The spamStats table is accessed by every participating node in the network. Each row is a new transaction that was made by one of the nodes and contains relevant information about the number of transactions that were made and how long each step in the transaction generation / submission process took. 

Field | Datatype | Values | Description | Example Data
--- | :---: | --- | --- | ---
**`id`** | `INTEGER PRIMARY KEY AUTO_INCREMENT` | int | Auto incrementing unique id for each row | `13423`
**`uuid`** | `CHAR(10)` | 10-byte string | Unique identifier (10 bytes) which was assigned to each node upon spam | `6e6d91632574f1459c0f`
**`type`** | `VARCHAR(5)` | `MSG` or `VALUE` | Indicates whether it's a message or value transaction | `MSG` or `VALUE`
**`txs`** | `TINYINT` | [1 - 6] | Number of transactions that were generated in the bundle | `4`
**`prep`** | `DECIMAL(10, 5)` | Decimal, max 5 digits after decimal point | Total time in milliseconds that it took to generate and *sign* a bundle | `123.456`
**`tips`** | `DECIMAL(10, 5)` | Decimal, max 5 digits after decimal point | Total time in milliseconds that it took to select two tips with MCMC | `123.456`
**`depth`** | `TINYINT` | int | Depth which was used for the tip selection | `5`
**`pow`** | `DECIMAL(10, 5)` | Decimal, max 5 digits after decimal point | Total time in milliseconds that it took for the Proof of Work to complete | `123.456`
**`tail`** | `CHAR(81)` | 81 char string | 81-tryte transaction hash of the tail  | Standard transaction hash. Look up the docs
**`timestamp`** | `TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP` | UTC Timestamp | Automatically adds the latest UTC timestamp to the row. | `2017-04-14T19:39:46.000Z`
