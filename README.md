# Exploring Cryptocurrency Public Datasets in BigQuery

![Google Cloud](https://img.shields.io/badge/Google_Cloud-4285F4?logo=googlecloud&logoColor=white)
![BigQuery](https://img.shields.io/badge/BigQuery-669DF6?logo=googlebigquery&logoColor=white)
![Bitcoin](https://img.shields.io/badge/Bitcoin-F7931A?logo=bitcoin&logoColor=white)

Reference SQL for the Google Cloud lab **Exploring Cryptocurrency Public Datasets in BigQuery**.

## 📖 Overview

Bitcoin's full transaction history is available as a BigQuery public dataset. These queries show two classic blockchain analytics patterns:

1. **Find a transaction by output value.** Locate the famous pizza transaction worth 194,993 BTC by unnesting transaction outputs.
2. **Compute an address balance.** Build a double-entry ledger where inputs are debits and outputs are credits, then sum it for one address.

## 🧰 Services used

BigQuery · `bigquery-public-data.crypto_bitcoin` · `UNNEST` on repeated fields · CTEs

## ✅ Prerequisites

Create a dataset named `lab` in your project:

```bash
bq mk lab
```

## 🚀 Solution

### Query 1: Find the transaction hash by output value

```sql
CREATE OR REPLACE TABLE lab.`51` (transaction_hash STRING) AS
SELECT transaction_id
FROM `bigquery-public-data.bitcoin_blockchain.transactions`,
  UNNEST(outputs) AS outputs
WHERE outputs.output_satoshis = 19499300000000;
```

### Query 2: Compute the balance of an address

Adapted from Google Cloud's blog post on Bitcoin analytics in BigQuery.

```sql
CREATE OR REPLACE TABLE lab.`52` (balance NUMERIC) AS
WITH double_entry_book AS (
  -- Debits
  SELECT ARRAY_TO_STRING(inputs.addresses, ",") AS address, -inputs.value AS value
  FROM `bigquery-public-data.crypto_bitcoin.inputs` AS inputs
  UNION ALL
  -- Credits
  SELECT ARRAY_TO_STRING(outputs.addresses, ",") AS address, outputs.value AS value
  FROM `bigquery-public-data.crypto_bitcoin.outputs` AS outputs
)
SELECT SUM(value) AS balance
FROM double_entry_book
WHERE address = "1XPTgDRhN8RFnzniWCddobD9iKZatrvH4";
```

> The older `bitcoin_blockchain` dataset is no longer updated. For new work, use `crypto_bitcoin`.

---

## ⚠️ Notes

- Resource names, zones, regions and ports are generated per lab session. Always use the values shown in **your** lab instructions.
- These steps were written when the lab was live. Google revises labs over time, so the current version may differ.

## 📌 Disclaimer

This repository is a study reference. Attempt the lab on your own first, then compare approaches. It is not affiliated with or endorsed by Google.

## 👤 Author

**Girish Sharma** · Cloud & DevOps Engineer · [GitHub](https://github.com/codewithgarry) · [LinkedIn](https://www.linkedin.com/in/codewithgarry/)
