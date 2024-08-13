---
title: Chia Blockchain Database
slug:
---

# Chia Blockchain Database

## Database Location

Linux / Mac:
```shell
  .chia/mainnet/db
```

Windows:
```shell
   ???
```
### Files within the database folder

- blockchain_v2_mainnet.sqlite

   This is the Chia blockchain database (SQLite).


- blockchain_v2_mainnet.sqlite-shm
   
   SQLite shared memory file. A temporary file used by SQLite.


- blockchain_v2_mainnet.sqlite-wal

   SQLite write-ahead log. A temporary file used by SQLite.


- height-to-hash

   ???


- peers.dat

   ???


- sub-epoch-summaries

   ???


## Tables and Indexes in the Chia Blockchain Database (blockchain_v2_mainnet.sqlite)

   Some columns in the tables are of type 'BLOB'. A blob is a binary data type. The blobs in the Chia database are 32-byte values and can be displayed in hex.
    
   Example:
   ```shell
  SELECT HEX(coin_name), confirmed_index, HEX(puzzle_hash), timestamp FROM coin_record LIMIT 1;
  ```
  Output:
```shell
  DCE550A4341E5EC31C7E3FE5C6AB9801C66ED02689725939537D8D4492465800|1|3D8765D3A597EC1D99663F6C9816D915B9F68613AC94009884C4ADDAEFCCE6AF|1616162525
   ```

### coin_record

The coin_record table holds all spent and unspent coins in the Chia blockchain.

```shell
CREATE TABLE coin_record(coin_name blob PRIMARY KEY, confirmed_index bigint, spent_index bigint, coinbase int, puzzle_hash blob, coin_parent blob, amount blob, timestamp bigint);
CREATE INDEX coin_confirmed_index on coin_record(confirmed_index);
CREATE INDEX coin_spent_index on coin_record(spent_index);
CREATE INDEX coin_puzzle_hash on coin_record(puzzle_hash);
CREATE INDEX coin_parent_index on coin_record(coin_parent);
```

- coin_name

   The coin ID. The coin ID is the SHA256 hash of the coin's parent coin ID, puzzle hash and amount.


- confirmed_index

  The block height when the coin was created.


- spent_index

   The block height when the coin was spent.


- coinbase

   Whether the coin is a block reward or not. The value can be 0 or 1 (false or true).


- puzzle_hash

   The puzzle hash of the coin. The puzzle hash is a SHA256 hash of the coin's puzzle.


- coin_parent

   The coin ID of the parent coin.


- amount

   The amount in mojos. A mojo is one trillionth of a XCH (0.000000000001 XCH or 1 x 10^-12 XCH).


- timestamp

   A Unix timestamp (the number of non-leap seconds since January 1st 1970, 00:00:00 UTC. AKA the Unix epoch).


### current_peak

The current_peak table has one row containing the header hash of the most recent blockchain block.

```shell
CREATE TABLE current_peak(key int PRIMARY KEY, hash blob);
```

- key

   The value of the key column is always 0 (zero). Its purpose is to make the update statement for this row/table simple.


- hash

   The hash column contains the header hash of the most recent block.


### database_version

The database_version table has a single row which contains the current version of the Chia blockchain database.

```shell
CREATE TABLE database_version(version int);
```

- version

   The version number of the Chia blockchain database.


### full_blocks

The full_blocks table contains ... ???

```shell
CREATE TABLE full_blocks(header_hash blob PRIMARY KEY,prev_hash blob,height bigint,sub_epoch_summary blob,is_fully_compactified tinyint,in_main_chain tinyint,block blob,block_record blob);
CREATE INDEX height on full_blocks(height);
CREATE INDEX is_fully_compactified ON full_blocks(is_fully_compactified, in_main_chain) WHERE in_main_chain=1;
CREATE INDEX main_chain ON full_blocks(height, in_main_chain) WHERE in_main_chain=1;
```

- header_hash

   The header hash is the block identifier. 


- prev_hash

   The header hash of the block that came before this block. 


- height

   The height of this block.


- sub_epoch_summary

   ???


- is_fully_compactified

   ???


- in_main_chain

   ???


- block

   ???


- block_record

   ???


### hints

The hints table contains ... ???

```shell
CREATE TABLE hints(coin_id blob, hint blob, UNIQUE (coin_id, hint));
CREATE INDEX hint_index on hints(hint);
```

- coin_id

   ???


- hint

   ???


### sub_epoch_segments_v3

The sub_epoch_segments_v3 table contains ... ???

```shell
CREATE TABLE sub_epoch_segments_v3(ses_block_hash blob PRIMARY KEY,challenge_segments blob);
```

- ses_block_hash

   ???


- challenge_segments

   ???
   

## Other Files in the Database folder

### height-to-hash

   The height-to-hash binary file maps the block heights to their corresponding block header hashes. A block header 
   hash is the identifier for a given block. The file is used to quickly check if the blockchain database is up to date 
   on the correct and valid chain.
   

   To decode this file ... ???

### peers.dat

The peers.dat binary file contains a lists of valid and banned peers. This file enables the node to quickly connect to 
other nodes in the Chia network.

To decode this file ... ???

### sub-epoch-summaries

The sub-epoch-summaries binary file contains a snapshot of each epoch. The file is used by the lite syncing protocols to 
quickly check if the blockchain database is valid and up to date.
  
To decode this file ... ???
   
