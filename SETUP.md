# Elephant Oracle Setup Guide

Complete these steps before running the oracle notebook.

## Prerequisites Checklist

### 1. Google Account

Create a Google account to access Google Colab for running the oracle notebook.

- [Create Google Account](https://accounts.google.com/signup)

### 2. Pinata Account & JWT Token

#### Why You Need a Pinata JWT

When you submit property data to Elephant, the actual data needs to be stored somewhere accessible to everyone. IPFS is that storage system, and Pinata is a service that ensures your data stays available on IPFS.

Think of it like this: IPFS is like a global filing cabinet, and Pinata makes sure your files don't get lost or removed. The JWT (JSON Web Token) is your personal key that proves to Pinata you're allowed to add files.

#### Creating Your Pinata JWT

1. **Sign up for Pinata**

   - Go to [pinata.cloud](https://pinata.cloud)
   - Click "Get started"
   - Create a free account (includes 1GB storage)
   - Select free plan
   - For the "Defining your project" step, you can enter any description - it won't affect future actions.

2. **Generate Your API Key**

   - Once logged in, find sidebar on the left
   - Select "API Keys"
   - Click "New Key"
   - Enter a name like "Elephant Oracle" in the Key name field
   - **Toggle ON the "Admin" switch** (this gives full permissions)
   - Click "Create Key"

3. **Copy Your JWT Token**
   - A popup will appear showing three items:
     - API Key (you don't need this)
     - API Secret (you don't need this)
     - **JWT (secret access token)** - THIS is what you need!
   - Click the copy button next to the JWT field (bottom one)
   - **Save this immediately** - it won't be shown again

### 3. MetaMask Wallet

Needed for blockchain transactions.

- Go to [metamask.io](https://metamask.io) and click **Install MetaMask** in the top right corner
- Create new wallet and save recovery phrase
- Add Polygon network using [this guide](https://polygon.technology/blog/getting-started-with-metamask-on-polygon)
- [Detailed MetaMask Setup Guide →](https://support.metamask.io/start/getting-started-with-metamask/)

### 4. Purchase POL Tokens

Required for transaction gas fees (~$5 worth covers many submissions).

- Create [Binance](https://www.binance.com) account
- Complete KYC verification
- Purchase POL tokens using the [Binance POL buying guide](https://www.binance.com/en/how-to-buy/polygon-ecosystem-token)
- Buy via card, P2P, or convert from other crypto

### 5. Withdraw POL to MetaMask

Transfer tokens to your wallet on Polygon network following [Binance's withdrawal guide](https://academy.binance.com/en/articles/your-guide-to-binance-deposit-withdrawal#How-to-Withdraw-From-Binance).

- Hover over account icon (top right) → Select [Assets]
- Click [Withdraw] → Select POL cryptocurrency
- **⚠️ CRITICAL: Choose "Polygon" as the network** - NOT Ethereum/ERC-20
- Paste your MetaMask wallet address
- Enter withdrawal amount
- Complete 2FA and email confirmation
- **Wrong network = lost funds** - always double-check!

### 6. Create an Alchemy Account for a Custom RPC URL

While public RPC endpoints for the Polygon network are available, they are often rate-limited, which can cause failures when submitting transactions with the `elephant-cli`. For a more reliable and performant experience, we strongly recommend creating a dedicated RPC URL using Alchemy.

1.  **Create a Free Alchemy Account**
    *   Go to [alchemy.com](https://alchemy.com) and sign up for a free account.

2.  **Create a New App**
    *   From your dashboard, click on **+ CREATE APP**.
    *   **Name**: Enter a descriptive name, like "Elephant Oracle".
    *   **Chain**: Select **Polygon**.
    *   **Network**: Select **Polygon PoS Mainnet**.
    *   Click **CREATE APP**.

3.  **Get Your Custom RPC URL**
    *   From the dashboard, select the app you just created.
    *   Click on **VIEW KEY**.
    *   Copy the **HTTPS** URL. It will look something like this: `https://polygon-mainnet.g.alchemy.com/v2/YOUR_API_KEY`

4.  **Save Your RPC URL**
    *   Save this URL in a secure place. You will need to provide it to the `elephant-cli` when submitting transactions.

## Ready to Start?

With all prerequisites complete:

1. Request seed file from Elephant team
2. Open the [Oracle Notebook](https://colab.research.google.com/drive/14tSNSP8Pe-mY4VwX9JhXgfyOvzmN3kC0?usp=sharing)
3. Follow the step-by-step process

## Need Help?

- [Elephant Lexicon](https://lexicon.elephant.xyz/)
