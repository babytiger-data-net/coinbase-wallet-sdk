# Using Coinbase Wallet SDK v4 beta with wagmi

This quickstart guide walks through building a dapp for Coinbase smart wallet using wagmi.

#### If you have any questions or feedback please [Join the Coinbase Developer Platform Discord Server!](https://discord.com/invite/cdp)

## Steps:

1. **Create a new wagmi app**
   ```sh
   pnpm create wagmi
   ```
1. **Select desired options**
   ```sh
   ✔ Project name: test-app-name
   ✔ Select a framework: › React
   ✔ Select a variant: › Vite
   ```
1. **Go to your new app directory**
   ```sh
   cd test-app-name
   ```
1. **Add the following resolutions block to your `package.json` to force wagmi to use version `4.0.0-beta.12`**
   ```json
   "resolutions": {
       "@coinbase/wallet-sdk": "4.0.0-beta.12"
   },
   ```
1. **Install dependencies**
   ```sh
   pnpm install
   ```
1. **Start the test app**
   ```sh
   pnpm dev
   ```
1. **Configure the coinbaseWallet connector**

   - Only to `baseSepolia` is supported by http://keys.coinbase.com at this time.
   - Remove unecessary connectors
   - Edit `src/wagmi.ts` to look like this:

   ```typescript
   import { http, createConfig } from 'wagmi';
   import { baseSepolia } from 'wagmi/chains';
   import { coinbaseWallet } from 'wagmi/connectors';

   export const config = createConfig({
     chains: [baseSepolia],
     connectors: [
       coinbaseWallet({
         appName: 'Create Wagmi',
         appChainIds: [baseSepolia.id],
       }),
     ],
     transports: {
       [baseSepolia.id]: http(),
     },
   });

   declare module 'wagmi' {
     interface Register {
       config: typeof config;
     }
   }
   ```

   **Note: Any of the `CoinbaseWalletSDKOptions` may be passed to the `coinbaseWallet` connector:**

   - `appName: string`
     - Your dapp's name to display in wallet with requests
   - `appLogoUrl?: string`
     - Your dapp's logo
     - Favicon is used if unspecified
   - `chainIds?: number[]`

     - _Note: you will see typescript warnings since wagmi hasn't been upgraded to use Coinbase Wallet SDK v4 yet_
     - An array of chain ids your dapp supports
     - The first chain in this array will be used as the default chain.
     - Removes the need for non-mainnet dapps to request switching chains immediately.
     - Default value is `[1]` (mainnet), which is not supported by scw fe at this time.

   - There is an option to allow users to connect via smart wallet only or EOA wallet only, but that option will not be available to use via wagmi until wagmi has released a new version with Coinbase wallet SDK v4. More details in the [Migration Guide](migration_guide.md)

1. **In the test app, click the 'Coinbase Wallet' button to open the smart wallet popup and connect!**

   - If you have the Coinbase wallet extension enabled, that will be the default connection method. Disable it to see the option to connect via smart wallet.
   - The Coinbase wallet popup will allow users to choose their connection method:
     - Create and connect a new smart wallet
     - Connect with an existing smart wallet via passkey
     - Connect with Coinbase Wallet mobile app via QR code scan
   - Once connected, your app will display the connected status, wallet address, and the current chainId

1. **Take a look at `src/App.tsx` to see how things work, and edit as needed**

   - For example, you may want to show different content depending on connection status. You can accomplish that by adding these few lines _before_ the return statement in App.tsx.

     ```ts
     // src/App.tsx

     const connector = connectors[0];
     if (account.status !== 'connected') {
       return (
         <button key={connector.uid} onClick={() => connect({ connector })} type="button">
           {connector.name}
         </button>
       );
     }
     ```

1. **Make more requests!**

   - You can find everything you need here: https://wagmi.sh/react/api/hooks
