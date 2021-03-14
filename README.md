# TurtleCoin Wallet Repair Tool

In the event that you have wallet wherein you cannot send any transactions or optimize the wallet due to 
"Transaction Input Already Spent" errors and you've tried rewinding, resynching, and everything you can think
of to get access to your funds, this tool is designed for one purpose: allowing you to spend
any unspent funds in the wallet by using a sledge hammer to correct data inconsistencies.

What this tool does:

* Creates a backup of the wallet file
* Scans through all outputs currently in the wallet and determines if they have been spent already
  * Using the [Blockchain Cache API](https://docs.turtlepay.io/blockapi) to check key image use.
* If the key image (output) has been used already, discards the output
* If the key image (output) has **not** been used already, keeps the output
* Re-writes the transaction history in the wallet file such that it reflects a balance of only the unspent
outputs in the file.
  * **Transaction history in the wallet software is unlikely to match the previous history or a transaction as shown 
    in the [Block Explorer](https://explorer.turtlecoin.lol)**
* Sets the scan height of the wallet to the block **after** the block of highest block found in the wallet file.
* Saves the wallet file such that it can be opened in Zedwallet or Proton
* Allows you to get the remaining unspent funds out of the wallet and into a new wallet

**It is very likely that the resulting balance will not match the previous balance displayed by the wallets. This
is expected behavior as the data inconsistencies that are resolved resulted in wallet software displaying an incorrect 
balance in the first place that ultimately prevented you from sending funds or optimizing the wallet.**

***TO BE VERY CLEAR: THE PREVIOUS BALANCE DISPLAYED BY WALLET SOFTWARE WAS VERY LIKELY NOT REAL AND ANY DIFFERENCE
IN THE WALLET BALANCE BEFORE AND AFTER RUNNING THIS TOOL ARE FUNDS THAT WERE ALREADY SPENT.***

What this tool does **not** do:

* Crack your wallet password
* Allow you to double spend funds
* Restore access to wallet keys
* Give you magic god-like powers
* Create funds out of thin air
* **GUARANTEE YOU ACCESS TO YOUR FUNDS**
  * This is a sledger hammer that slams a square peg into a round hole. It's a last ditch effort to restore access
    to funds.

### How to Use

**USE OF THIS TOOL IS AT YOUR OWN RISK. YOU ASSUME ALL RISK IN USING THIS TOOL.**

**ALWAYS BACK UP YOUR KEYS BEFORE USING THIS OR ANY OTHER TOOL.**

#### Step 1

Sync your wallet file to 100% and **save** it.

It doesn't matter if you sync from 0 or from the scan-height of just before you started receiving funds.

#### Step 2

```bash
> npm install -g turtlecoin-wallet-repair@latest
```

#### Step 3

```bash
> turtlecoin-wallet-repair <wallet-file> <wallet-password>
```

Allow the tool to run against the file. Depending on how many inputs are in the file, this could be a fast process
or it may take a while. **Be patient**.

You'll see a bunch of text scroll by that looks something like this:

```
2021-03-14T15:49:43.532Z info: TurtleCoin Wallet Repair Utility v0.0.11
2021-03-14T15:49:43.537Z warn: A backup of the wallet file has been created as: D:\test.wallet.backup
2021-03-14T15:49:43.539Z info: Daemon connection established to: node.turtlepay.io:443
2021-03-14T15:49:43.688Z info: Wallet file opened successfully: D:\test.wallet
2021-03-14T15:49:43.689Z info: Wallet reports a balance of: 30.00 TRTL
2021-03-14T15:49:43.690Z info: Checking subwallet: TRTLv1pacKFJk9QgSmzk2LJWn14JGmTKzReFLz1RgY3K9Ryn7783RDT2TretzfYdck5GMCGzXTuwKfePWQYViNs4avKpnUbrwfQ
2021-03-14T15:49:43.691Z info: Found 3 unspent inputs in wallet
2021-03-14T15:49:43.925Z info: Input with keyImage: d044d5012dca585aa73d169518b9f648490a13a7ead2f3211a0c735645ba90d4 marked as unspent
2021-03-14T15:49:44.060Z info: Input with keyImage: 92528a3e7755b7cb0a3b72ddbd80c7a1d4ded071f309e72d679f45576451b355 marked as unspent
2021-03-14T15:49:44.214Z info: Input with keyImage: ef2103692b0e7fa9b46a7698f044f38effda23930f22f3004579af834ca4c564 marked as unspent
2021-03-14T15:49:44.214Z info: Found 0 spent inputs in wallet
2021-03-14T15:49:44.215Z info: Found 0 locked inputs in wallet
2021-03-14T15:49:44.215Z info: Updating subwallet structure...
2021-03-14T15:49:44.215Z info: Reconstructing incoming transactions...
2021-03-14T15:49:44.382Z info: Reconstructed transaction: fd427b442ce1a8f2c2cb59881b7dcdf3b70982d0d7bb0797810b735a75c069d6
2021-03-14T15:49:44.535Z info: Reconstructed transaction: bac1e4da69be375ca9d31faf2050b0a1116e152c260cf5bdbd7e033745ee1830
2021-03-14T15:49:44.714Z info: Reconstructed transaction: 736214f4046b303737c175e7a300a24666fba707ce3854cc16e0a712b45fb452
2021-03-14T15:49:44.717Z info: Locked: 0.00 TRTL
2021-03-14T15:49:44.717Z info: Unlocked: 30.00 TRTL
2021-03-14T15:49:44.717Z warn: Wallet now reports a balance of: 30.00 TRTL
2021-03-14T15:49:44.717Z warn: This balance is likely different than what you expected, but it is the balance of unspent funds.
2021-03-14T15:49:44.717Z warn: Saving updated wallet...
2021-03-14T15:49:44.864Z warn: The transaction history of the wallet has been altered!
2021-03-14T15:49:44.864Z warn: Transactions as found in the wallet may not match transactions as provided on block explorers
2021-03-14T15:49:44.864Z warn: You may now re-open the wallet file in your usual wallet software.
2021-03-14T15:49:44.864Z warn: THe wallet had inputs starting at block 3406926 that were checked
2021-03-14T15:49:44.864Z warn: The wallet will begin re-scanning at block 3408022
2021-03-14T15:49:44.865Z warn: Once the scanning is complete, please move ALL remaining funds to a NEW wallet.
```

**Note:** Only key images marked as **unspent** can still be spent, all others have already been spent and likely
contributed to the problem you were experiencing, even more so if they were found as **unspent** and this tool
marked them as **spent**. See the problem?

#### Step 4

Open the wallet file using Zedwallet or Proton (GUI). Attempt to **send_all** to a new wallet address.

If the process fails, you will likely need to **optimize** the wallet first before attempting to **send_all** again.

If, after optimizing the wallet you receive "Transaction input has already been spent" errors again, re-run
this tool on the wallet file again.
