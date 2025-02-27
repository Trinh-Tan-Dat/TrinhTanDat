# How to Filter and Send API Requests Using CLI

## Objective
We have a file `transaction-log.txt` that contains order data in JSON format. Our goal is to:

1. Find orders where:
   - `symbol` is **TSLA**
   - `side` is **sell**
2. Extract the `order_id` from these orders.

## CLI Command
```bash
jq -r 'select(.symbol == "TSLA" and .side == "sell") | .order_id' ./transaction-log.txt | xargs -I {} curl -s "https://example.com/api/{}" >> ./output.txt
```

## Explanation
### **Step 1: Filter orders using `jq`**
```bash
jq -r 'select(.symbol == "TSLA" and .side == "sell") | .order_id' ./transaction-log.txt
```
- `jq` is a tool to process JSON in the terminal.
- `select(.symbol == "TSLA" and .side == "sell")` finds orders where:
  - `symbol` is **TSLA**
  - `side` is **sell**
- `.order_id` extracts the `order_id` from these orders.
- `-r` outputs raw text (removes quotes around values).



### **Step 2: Send API requests with `xargs` and `curl`**
```bash
xargs -I {} curl -s "https://example.com/api/{}" >> ./output.txt
```
- `xargs` takes the `order_id` from `jq` and runs `curl` for each ID.
- `-I {}` replaces `{}` with the `order_id`.
