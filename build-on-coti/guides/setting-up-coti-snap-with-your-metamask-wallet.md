# Setting up COTI Snap with your MetaMask wallet

Last updated: May 2026\
Estimated time: 5 minutes\
Requirements: MetaMask (desktop), COTI Mainnet connection

\
Stage 1 — Install the COTI Snap

#### What you’ll need

* MetaMask wallet (desktop browser extension)
* Access to the official COTI Snap install link

Mobile browsers are not supported yet.

#### Step 1 — Connect your wallet

1. Navigate to metamask.coti.io.
2. Click Connect Wallet.
3. MetaMask will open a prompt asking:

<figure><img src="../../.gitbook/assets/unknown.png" alt="" width="337"><figcaption></figcaption></figure>

This step allows MetaMask to enable private token support through the Snap.

#### Step 2 — Install the COTI Snap

1. Click Install COTI MetaMask Snap.
2. If you don’t already have the Snap installed, MetaMask will prompt you automatically.
3.  Approve the installation.\
    \
    

    <figure><img src="../../.gitbook/assets/unknown (1).png" alt="" width="365"><figcaption></figcaption></figure>

#### Step 3 — Approve connection

1. MetaMask will ask for permission to connect the Snap to your wallet.
2. Click Approve when prompted.



<figure><img src="../../.gitbook/assets/unknown (2).png" alt="" width="375"><figcaption></figcaption></figure>



#### Step 4 — Choose your account and connect

Select the account you wish to use with the Snap (recommended: your COTI Mainnet account) and click Connect.

You’ve successfully added the COTI Snap to MetaMask!

### Stage 2 — View your COTI Private Token balances

#### Step 5 — Confirm Snap installation

1. In MetaMask, click your Profile icon → Settings → Snaps.
2. You should now see COTI Snap listed and active.\
   

<figure><img src="../../.gitbook/assets/unknown (3).png" alt="" width="375"><figcaption></figcaption></figure>

\


<figure><img src="../../.gitbook/assets/unknown (4).png" alt="" width="375"><figcaption></figcaption></figure>

#### Step 6 — Onboard your account

1. In MetaMask, click Onboard Account.
2. MetaMask will open a popup to register your account with the Snap.
3.  Click Confirm when prompted.\
    \
    \
    

    <figure><img src="../../.gitbook/assets/unknown (6).png" alt="" width="375"><figcaption></figcaption></figure>

#### Step 7 — Sign and authorize

MetaMask will display a Signature Request popup.\
Click Confirm to authorize the onboarding.\
The message may appear encrypted — this is expected.\
This simply verifies your ownership and uses a small amount of COTI for gas.\
\


<figure><img src="../../.gitbook/assets/unknown (7).png" alt="" width="375"><figcaption></figcaption></figure>

#### Step 8 — Approve the connection

1. MetaMask will display the request from snap.coti.io on the COTI Mainnet.
2. Click Confirm to proceed.



<figure><img src="../../.gitbook/assets/unknown (9).png" alt="" width="375"><figcaption></figcaption></figure>

#### Step 9 — Grant AES key access

1. The COTI app will now request access to your AES Key — used for decrypting your private balances.
2. Click Request, and when MetaMask pops up, choose Approve.

Without this step, you can hold private tokens, but you won’t be able to view balances.

<figure><img src="../../.gitbook/assets/unknown (10).png" alt="" width="375"><figcaption></figcaption></figure>

#### Step 10 — Launch the dApp

Click Launch dApp to open the COTI Snap application and view your private token balances.\


<figure><img src="../../.gitbook/assets/unknown (11).png" alt="" width="326"><figcaption></figcaption></figure>

You’ve completed Stage 2 — your COTI Snap is live and connected!



### Stage 3 — Send and Receive Private Tokens

Private ERC-20 tokens on COTI are not standard ERC-20s — they include encrypted data and special logic.\
Some wallet or explorer behaviors may differ from standard tokens.

#### Step 13 — Import your private token

1. In MetaMask Snap, click Import Tokens.
2. Paste the private token’s contract address
3. Click Next → Import and confirm in MetaMask.\
   

<figure><img src="../../.gitbook/assets/unknown (12).png" alt="" width="375"><figcaption></figcaption></figure>

#### Why MetaMask can show a very large “balance” for private ERC-20 tokens

Private ERC-20 (p.ERC-20) tokens are **interface-compatible** with standard ERC-20 (for example, MetaMask can import them using the contract address). The **on-chain `balanceOf` value is not a human-readable token amount** in the same way as for a normal ERC-20: it encodes **private balance data** (for example, ciphertext or other packed representation).

The main MetaMask extension still treats `balanceOf` like a plain decimal integer. When that value is actually **encrypted or encoded data** (often a large hex-like payload), MetaMask **interprets those bytes as one huge decimal number**. That is why you may see an extremely long number next to the token in the default MetaMask **Assets** list — it is **not** your real spendable balance shown as digits; it is that encoded value displayed as if it were a normal balance.

**Where to see the real private balance:** use the **COTI Snap dApp** (for example via [metamask.coti.io](https://metamask.coti.io)), where the Snap decrypts and displays the correct amount (for example **5 p.USDC.e** in the privacy UI vs. a nonsensical long number in the extension).

<figure><img src="../../.gitbook/assets/coti-snap-metamask-vs-dapp-p-erc20-balance.png" alt="Side by side: COTI Snap dApp showing correct private token balance versus MetaMask Assets list showing the same token as a very long decimal interpreted from encoded balance data" width="700"><figcaption>COTI Snap dApp (left) shows the decrypted private balance; the MetaMask extension (right) may show a huge number for the same token because it reads <code>balanceOf</code> as a plain ERC-20 amount.</figcaption></figure>

#### Step 14 — View token details

In the **COTI Snap dApp**, open the token to see details and the **decrypted** balance.\
The main MetaMask **Assets** list may still show a misleading huge number for private tokens (see the note above); rely on the Snap UI for the amount you can actually use.\


<figure><img src="../../.gitbook/assets/unknown (13).png" alt="" width="375"><figcaption></figcaption></figure>

#### Step 15 — Send your private token

1. Click Send Token in the COTI dApp or Snap interface.
2. Enter the recipient’s address and confirm.
3. Approve the request in MetaMask.

<figure><img src="../../.gitbook/assets/unknown (14).png" alt="" width="375"><figcaption></figcaption></figure>

The transaction is now live on-chain — encrypted and private.


