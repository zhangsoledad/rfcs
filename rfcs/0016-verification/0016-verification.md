---
Number: "0016"
Category: Informational
Status: Draft
Author: Dingwei Zhang
Organization: Nervos Foundation
Created: 2019-03-08
---

# CKB-Verification

## Abstract

This RFC describes ckb block data verification rules.

Verification rules are split into three categories in CKB implementation. `Header Verification`,  `Block Verification` and `Transaction Verification`.

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in RFC 2119.

## Definition

## Specification

1. Header Verification

    1. proof-of-work verification

        The `seal` field in block header is miner's proof of work, it MUST satisfy the block's difficulty target. Cuckoo Cycle is the temporary proof-of-work function used in CKB testnet, a new hash function will be proposed and used in mainnet.

    2. number verification

        The `number` field in block header MUST equal parent’s block number incremented by one, the genesis block has a number of zero.

    3. block version verification

        Block version MUST obey consensus rules.

    4. timestamp verification

        Timestamp (Unix’s time in milliseconds) of block timestamp MUST greater than the median timestamp of previous 11 blocks and less than the network-adjusted time + 15 seconds.

    5. difficulty verification

        The canonical difficulty of a block of header is defined as Diff == Max(Diff_last * o_last / o, Diff_last * 2) (o: orphan rate), It MUST greater or equal genesis' difficulty.

1. Block Verification

   1. empty transactions verification

        Block transactions MUST not be empty.

   2. duplicate

        Block transactions MUST be unique.

   3. cellbase

      * position: cellbase MUST be the first transaction in block.
      * input: cellbase MUST contain one null input, the input's script fill binary with current block number.
      * output_capacity must less or equal than block reward. Block reward includes primary issuance, secondary issuance and transaction fees, according to RFC#0015.

   4. merkle_root

        Verify transaction [merkle-proof][1].

   5. transaction proposal rules

        Commit transaction must be proposed within consensus rules specify proposal window.

   6. uncles

      * uncles hash: The hash MUST match the [CFB][2] serialized uncles.
      * number: there are at most 2 uncles included in one block.
      * depth: uncle's number MUST less than block's number - m, greater than block's number - n, the current m = 1, n =6.
      * epoch: uncle MUST be the same epoch with block.
      * uniqueness: an uncle must be different from all uncles included in previous blocks and all other uncles included in the same block (non-double-inclusion).

   7. block size limit

        The block size MUST be less or equal than block size limit. The block size limit is 10_000_000 bytes (10MB).

   8. cycles limit

        The maximum cycles expenditure per block. The current cycles limit is 100_000_000.

2. Transaction Verification

    Cellbase transaction is excluded from following rules unless otherwise noted.

    1. transaction version verification

        Transaction version MUST obey consensus rules.

    2. null verification

        All transaction inputs MUST not be null.

    3. empty inputs/outputs verification

        Transaction inputs or outputs MUST not empty.

    4. capacity verification

       * Transaction capacity sum of inputs MUST greater or equal capacity sum of outputs.
       * Each output's occupied_capacity MUST less than or equal to specify capacity.

    5. input uniqueness verification

        Input must be unique.

    6. input/dep status verification

        All inputs and deps MUST be live cell.

    7. script verification

        Execution script in [CKB-VM][3], perform user-define validation rules.


[1]: https://github.com/nervosnetwork/rfcs/blob/master/rfcs/0006-merkle-tree/0006-merkle-tree.md#merkle-proof
[2]: https://github.com/nervosnetwork/rfcs/pull/47
[3]: https://github.com/nervosnetwork/rfcs/tree/master/rfcs/0003-ckb-vm
