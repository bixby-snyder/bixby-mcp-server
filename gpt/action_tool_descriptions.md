GET_CHAINS_LIST
Get the list of known blockchain chains with their IDs.
Useful for getting a chain ID when the chain name is known.
This information can be used in other tools that require a chain ID to request information.

GET_BLOCK_INFO
Get block information like timestamp, gas used, burnt fees, transaction count etc.
Can optionally include the list of transaction hashes contained in the block. Transaction hashes are omitted by default; request them only when truly needed, as high-traffic chains may return long lists.

GET_LATEST_BLOCK
Get the latest indexed block number and timestamp, which represents the most recent state of the blockchain.
No transactions or token transfers can exist beyond this point, making it useful as a reference timestamp for other API calls.

GET_ADDRESS_BY_ENS_NAME
Convert an ENS domain name (e.g. "blockscout.eth") to its corresponding Ethereum address.

GET_TRANSACTIONS_BY_ADDRESS
Retrieves native currency transfers and smart contract interactions (calls, internal txs) for an address.
**Excludes token transfers** (ERC-20, etc.). You’ll see calls *to* token contracts, but not the `Transfer` events. Use `get_token_transfers_by_address` for token history.
A single tx can have multiple records from internal calls; use `internal_transaction_index` for execution order.
Supports filters:
- `address, age_from` → all txs since a date
- `address, age_from, age_to` → txs in a date range
- `address, age_from, age_to, methods` → txs filtered by method
**Supports pagination**: use `pagination.next_call` until complete.

GET_TOKEN_TRANSFERS_BY_ADDRESS
Get ERC-20 token transfers for an address within a specific time range.
Supports:
- `address, age_from` → all transfers since a date
- `address, age_from, age_to` → transfers in a date range
- `address, age_from, age_to, token` → transfers for a specific token
**Supports pagination**.

LOOKUP_TOKEN_BY_SYMBOL
Search for token addresses by symbol or name. Returns up to 7 matches from the Blockscout API.

GET_CONTRACT_ABI
Get a smart contract ABI (Application Binary Interface).
An ABI defines all functions, events, parameters, and return types.
Required to format function calls or interpret contract data.

INSPECT_CONTRACT_CODE
Inspect a verified contract’s source code or metadata.

READ_CONTRACT
Call a smart contract function (view/pure, or non-view simulated via eth_call) and return the decoded result.

Mini-Schema:
- chain_id (string or int) → e.g. "1" for Ethereum Mainnet
- address (string) → contract address
- abi (object or array) → ABI for the function, from `get_contract_abi`
- function_name (string) → name of function to call
- args (array) → ordered arguments for the function

Examples:

ERC-20 balanceOf
```json
{
  "tool_name": "read_contract",
  "params": {
    "chain_id": "1",
    "address": "0xdAC17F958D2ee523a2206206994597C13D831ec7",
    "abi": {
      "inputs": [{"name": "_owner", "type": "address"}],
      "name": "balanceOf",
      "outputs": [{"name": "balance", "type": "uint256"}],
      "stateMutability": "view",
      "type": "function"
    },
    "function_name": "balanceOf",
    "args": ["0xF977814e90dA44bFA03b6295A0616a897441aceC"]
  }
}
```

ERC-20 decimals
```json
{
  "tool_name": "read_contract",
  "params": {
    "chain_id": "1",
    "address": "0xdAC17F958D2ee523a2206206994597C13D831ec7",
    "abi": {
      "inputs": [],
      "name": "decimals",
      "outputs": [{"name": "", "type": "uint8"}],
      "stateMutability": "view",
      "type": "function"
    },
    "function_name": "decimals",
    "args": []
  }
}
```

Notes:
- Always fetch ABI first if unsure.
- If call fails, retry with corrected types.
- Report both attempts if still failing.

GET_ADDRESS_INFO
Comprehensive info about an address:
- Address existence check
- Native balance (as-is, no decimals adjusted)
- ENS association
- Contract status (contract, verified, etc.)
- Proxy details (implementation addresses, proxy type)
- Token details (if applicable: name, symbol, decimals, supply)

GET_TOKENS_BY_ADDRESS
Get ERC-20 token holdings for an address with metadata and market data.
Returns:
- Contract details (name, symbol, decimals)
- Market metrics (exchange rate, market cap, volume)
- Holders count
- Balance (raw, no decimal adjustment)
**Supports pagination**.

TRANSACTION_SUMMARY
Human-readable transaction summaries from Blockscout Transaction Interpreter.
Classifies txs (transfers, swaps, NFT sales, DeFi ops).
Useful for dashboards or quick analysis.
Note: Not all txs can be summarized; accuracy may vary.

NFT_TOKENS_BY_ADDRESS
Retrieve NFT tokens owned by an address, grouped by collection.
Includes:
- Collection (type, address, name, symbol, supply, holders)
- Token instance data (ID, name, description, URL, metadata)
**Supports pagination**.

GET_TRANSACTION_INFO
Detailed transaction info, richer than `eth_getTransactionByHash`.
Includes:
- Decoded input params
- Token transfers with metadata
- Fee breakdown (priority, burnt)
- Categorized tx types
Raw input is omitted if decoded version exists; request with `include_raw_input=True` if necessary.

GET_TRANSACTION_LOGS
Detailed transaction logs, richer than `eth_getLogs`.
Includes decoded event parameters with types and values.
Useful for token transfers, DeFi monitoring, event debugging.
**Supports pagination**.
