# Development Tasks for Paragon Pool Token

This document lists detailed steps to transform the current LOKQ-based repository into the "Paragon Pool" token project. Guidance comes from `AI_Explanation.txt` and the reference implementation under `SOLOTTO_WORKING_EXAMPLE`.

## 1. Configuration Updates
1. **Edit `MySolanaProjectConfg.json`**
   - Replace the current LOKQ values with your Paragon Pool parameters. Lines 2‑9 show the existing configuration:
     ```json
     {
       "payerKeyPath": "/home/solottotoke_gmail_com/.config/solana/id.json",
       "jsonMetadataUri": "https://arweave.net/-Jzxp64F3K2-K2hOpW9VqU3Vk8jodJCN2l4wcyW-8UY",
       "tokenName": "Lokquidity - mint auth revo - liquidity locked",
       "tokenSymbol": "LOKQ",
       "tokenDecimals": 9,
       "tokenSupply": 1000000000,
       "tokenAddress": "YOUR_TOKEN_MINT_ADDRESS_HERE",
       "metadataPDA": "YOUR_METADATA_PDA_ADDRESS_HERE"
     }
     ```
   - Set `tokenName` to **"Paragon Pool"** and choose a symbol (e.g. `"PARA"`).
   - Adjust `tokenSupply`, `payerKeyPath`, and leave `tokenAddress`/`metadataPDA` empty until minting.
   - After uploading metadata (see below), update `jsonMetadataUri` with the permanent URI.

2. **Create dedicated metadata JSON**
   - Duplicate `solana-token.js` as `paragon-pool-metadata.json` and fill in your new name, symbol, description, and creator address. The AI explanation recommends this in lines 47‑65.
   - Upload the logo image and this JSON to Arweave or IPFS, then store the resulting URL in `jsonMetadataUri`.

## 2. Script Improvements
1. **Token Creation (`solana-token-create.js`)**
   - The script currently uses `TOKEN_PROGRAM_ID` when creating the mint (lines 66‑71). Modify imports and instructions to use `TOKEN_2022_PROGRAM_ID` as described in `AI_Explanation.txt` lines 69‑118.
   - Ensure every SPL‑Token instruction (`createInitializeMintInstruction`, `createMintToInstruction`, `createSetAuthorityInstruction`, etc.) receives `TOKEN_2022_PROGRAM_ID`.
   - Keep `isMutable: true` when the metadata account is created so updates are possible.

2. **Metadata Update (`solana-metadata-update.js`)**
   - The script expects `project-token-config.json` (line 18). Change this to read from `MySolanaProjectConfg.json` so both scripts share one configuration file.
   - Replace hard coded names and symbols (lines 62‑64) with values read from the config or the off‑chain metadata file.
   - Decide whether you want metadata to become immutable. If so, keep `isMutable: false`; otherwise remove or parameterize this field (see `AI_Explanation.txt` lines 161‑173).

## 3. Workflow Steps
1. Install dependencies (`npm install`).
2. Run `node solana-token-create.js` to mint the token. Record the displayed `tokenAddress` and `metadataPDA` (AI explanation lines 132‑144).
3. Paste those addresses back into `MySolanaProjectConfg.json` (lines 146‑150 of the AI explanation).
4. Optionally execute `node solana-metadata-update.js` once your off‑chain JSON is final.
5. Verify the token and logo on explorers and wallets (lines 154‑158 of the AI explanation).

## 4. Repository Clean‑up
1. **Rename files for clarity** – `solana-token.js` is actually a JSON metadata file. Rename or copy it to `lokq-metadata.json` and keep the new `paragon-pool-metadata.json` separate.
2. **Update `README.md`** – Current README still references LOKQ (lines 1‑14). Replace the name, symbol, and description with Paragon Pool details. Update instructions and next steps accordingly.
3. **Synchronize config paths** – both scripts and documentation should reference the same configuration file name.

## 5. Additional Requirements to Launch a Real Token
The Solotto example demonstrates extra tooling such as distribution scripts and lottery automation (see `SOLOTTO_WORKING_EXAMPLE/README.md` lines 9‑22). To fully realize a new token project you may also need:
1. **Liquidity provisioning and locking** – README line 97 notes setting up liquidity pools with locked liquidity. Implement scripts or smart contracts to lock LP tokens after creating a pool (e.g. on Raydium or Orca).
2. **Security and wallet management** – Follow the security note in the README (lines 90‑92) and store keypairs safely.
3. **Automation or additional features** – If you plan to add lotteries or transfer‑fee mechanics similar to Solotto, study the example shell scripts (`create_token_with_metadata.sh`, `setup_weekly_lottery.sh`, etc.) and port the relevant logic to JavaScript or shell scripts for Paragon Pool.
4. **Mainnet deployment considerations** – Decide which network (devnet, testnet, mainnet) your scripts target and make this configurable instead of hard coded to `mainnet-beta`.

Implementing these tasks will bring the repository in line with the AI‑generated plan so you can launch the Paragon Pool token with upgradeable metadata and proper wallet compatibility.
