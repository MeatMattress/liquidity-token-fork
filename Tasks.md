# Tasks for Preparing LOKQ Token Repository

This repo provides scripts and metadata for the **LOKQ** Solana token. Several pieces are missing or inconsistent compared with the full launch steps outlined in the project goals. Below is a step-by-step list of recommended updates.

## 1. Repository Hygiene
1. **Add `.gitignore`** – ignore `node_modules`, generated key files (`id.json`, `token-keys.json`), and local config files.
2. **Remove stray dependencies** – ensure `node_modules` and `package-lock.json` are not committed.

## 2. Configuration Files
1. **Rename `MySolanaProjectConfg.json`** to `project-token-config.json` (correct spelling) so that it matches the path expected in `solana-metadata-update.js`.
2. Add comments or a README section explaining each field. The current file contains placeholders:
   - `tokenAddress`: `"YOUR_TOKEN_MINT_ADDRESS_HERE"`
   - `metadataPDA`: `"YOUR_METADATA_PDA_ADDRESS_HERE"`
   shown in the config file lines 8‑9【F:MySolanaProjectConfg.json†L8-L9】.
3. Store the keypair path in this config and mention it should not be committed.

## 3. Token Creation Script
1. **Fix README instructions** – it currently says to run `node solana-token.js` (line 28‑31) which is a metadata JSON file, not a script【F:README.md†L28-L31】. Update README to instruct running `node solana-token-create.js`.
2. Change the Solana cluster from mainnet to devnet for initial tests. `solana-token-create.js` uses `clusterApiUrl('mainnet-beta')` on line 35【F:solana-token-create.js†L35-L36】. Add a config option to select network.
3. Ensure the script writes the newly created token address and metadata PDA back to `project-token-config.json` so `solana-metadata-update.js` can read them automatically.
4. Provide a helper to generate the payer keypair if it does not exist.

## 4. Metadata and Update Authority
1. Clarify that `solana-token.js` is metadata. Consider renaming it to `token-metadata.json` to avoid confusion.
2. Implement a script (or instructions) to run the Metaplex CLI command `metaplex token create-metadata-account` as described in the goal steps. This allows wallet compatibility checks.
3. Add a script or example command for `metaplex token verify-metadata`.
4. Include a script to transfer update authority to a separate keypair for security, following step 8 of the goal description.

## 5. Token Distribution
1. Provide a script that creates associated token accounts for other users via `spl-token create-account <MINT> <USER>`.
2. Provide a minting helper using `spl-token mint` so tokens can be distributed beyond the payer account.

## 6. Metadata Hosting
1. README references scripts `nft-storage-upload.js`, `arweave-upload.js`, and `upload-one-command.sh`, but these files are missing. Implement them or remove the references. Ensure they upload the logo `lokq-logo.png` and metadata JSON to IPFS/Arweave.
2. Update `solana-metadata-update.js` to fetch the new hosted metadata URI from the upload scripts and mark the metadata as immutable after finalizing.

## 7. Development Environment Setup
Add a setup guide (or script) describing installation of:
- Rust and Solana CLI
- Node.js and npm/yarn
- Metaplex CLI
These steps mirror Phase 1 Step 1 in the goal description.

## 8. Optional Enhancements
1. Add verification scripts or unit tests to confirm the mint authority has been revoked after creation.
2. Document how to submit the token to a Solana token list for wallet discovery.
3. Provide instructions for locking liquidity (currently only mentioned conceptually).

Implementing the above will bring the repository in line with the detailed token‑launch procedure and make it ready for broader wallet compatibility and future updates.
