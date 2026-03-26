# Setup Portal Account

While managing public assets is straightforward, the true power of the COTI Privacy Portal lies in its ability to conceal financial data. To interact with confidential assets, you must configure your Privacy Portal account to handle advanced decryption processes securely. Start by:



{% stepper %}
{% step %}
### Connecting Your Wallet

* Navigate your secure web browser to the official to the [COTI Privacy Portal](https://privacy.coti.io/). Make sure you followed [Prerequisites](prerequisites.md) instructions and you already have MetaMask plugin installed.
*   Click on the **Connect Wallet** button on top Privacy Portal left corner. Your MetaMask wallet will present a dialogue box requesting authorization to connect to the portal.  Review the request details and authorize the connection.\
    <br>

    <figure><img src="../../.gitbook/assets/portal_connect.png" alt="Public Tokens"><figcaption><p>Public Tokens Balances</p></figcaption></figure>

    <br>
{% endstep %}

{% step %}
### Check Your Tokens Public Balance

Once your MetaMask wallet is connected, you can navigate to the **Public Tokens** dashboard within the interface. In this section, the application displays **public assets** balance (e.g., COTI, WETH, USDT) that lies on COTI blockchain. To learn how to bridge digital assets to COTI, please review [COTI Bridge instrunctions](../../coti-bridge/).\
\
Before proceeding, please verify that your assets public balances accurately reflect your on-chain holdings, as this confirms that your wallet is communicating flawlessly with COTI Privacy Portal.<br>

<div align="left" data-with-frame="true"><figure><img src="../../.gitbook/assets/Screenshot 2026-03-23 at 9.30.36 PM.png" alt="Public Tokens"><figcaption><p>Public Tokens Balances</p></figcaption></figure></div>

{% hint style="info" %}
💡 **Tip:** Use the search bar to quickly find a specific token.
{% endhint %}
{% endstep %}

{% step %}
### Unlock your privates tokens​

Please focus on the Private Tokens section of the portal interface. By default, this section is locked, and your balances are encrypted and hidden from view. To unlock them, follow the steps below.\
\
1\. Click the **Unlock** button on the **Private Tokens** dashboard.&#x20;

<div data-with-frame="true"><figure><img src="../../.gitbook/assets/Screenshot 2026-03-24 at 8.46.58 AM.png" alt="Unlock Button"><figcaption><p>Unlock button</p></figcaption></figure></div>

{% hint style="info" %}
If this is your first time using the Privacy Portal, it will prompt you to install the official COTI MetaMask Snap.  This extension handles the encryption and decryption of your private balances.  [Follow the instrunctions here](metamask-snap-setup.md).
{% endhint %}

2.  After clicking the unlock button, a modal screen will appear, requesting authorization to access your [COTI AES security key.](../../how-coti-works/advanced-topics/aes-keys.md) Please review and authorize the request on the MetaMask snap.<br>

    <figure><img src="../../.gitbook/assets/Screenshot 2026-03-24 at 10.22.34 AM.png" alt="Private Balances"><figcaption><p>Private Balances</p></figcaption></figure>

    <br>
3. &#x20;Once unlocked, your decrypted private balances will be displayed. To aid in visual differentiation, COTI Privacy Portal utilizes a specific nomenclature system. You will observe that your decrypted assets carry a distinct prefix —such as p.COTI, p.WETH, or p.USDC.e— clearly distinguishing them from their transparent counterparts in the **Public Tokens** dashboard.

<div data-with-frame="true"><figure><img src="../../.gitbook/assets/Screenshot 2026-03-24 at 8.55.17 AM.png" alt="Private Balances"><figcaption><p>Private Balances</p></figcaption></figure></div>

{% hint style="info" %}
🔒 **Privacy Note:** The Private Token balances are encrypted on the blockchain. They are decrypted locally in your browser using your personal AES key stored in the COTI Snap. No one else can see your balances.
{% endhint %}


{% endstep %}
{% endstepper %}
