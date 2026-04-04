# Convert to Public Tokens

The Private Tokens dashboard offers **Portal Out**, which converts private tokens back into standard public tokens. The portal uses the same pattern as Portal In: when the bridge needs permission to move your **private** balance, you **Approve** first, then confirm the withdrawal.

{% stepper %}
{% step %}
### Unlock and choose the asset

* Ensure your private balances are unlocked (see [Setup Portal Account — Unlock private tokens](setup-portal-account.md)).
* Select the asset from your **Private Tokens** list and enter the amount you want to withdraw.

<div align="left" data-with-frame="true"><figure><img src="../../.gitbook/assets/Screenshot 2026-03-24 at 3.42.27 PM.png" alt="Portal Out"><figcaption><p>Portal Out</p></figcaption></figure></div>
{% endstep %}

{% step %}
### Approve (when the portal asks)

* If the modal shows an **Approve** step, confirm it in MetaMask. This grants the privacy bridge permission to spend the **private** token amount you are withdrawing (encrypted allowance), similar to approving a public ERC-20 before Portal In.
* Wait until the approval transaction is confirmed. The UI will then let you continue to the actual withdrawal.

{% hint style="info" %}
Native **p.COTI** withdrawals also use an approval step for the native privacy bridge when required by the app flow.
{% endhint %}
{% endstep %}

{% step %}
### Confirm Portal Out (withdraw)

* Click **Portal Out** (or the button shown to complete the withdrawal) and confirm the transaction in MetaMask.
* The private tokens are burned on the private side and the corresponding **public** tokens return to your wallet.

    <div data-with-frame="true"><figure><img src="../../.gitbook/assets/Screenshot 2026-03-24 at 3.42.27 PM.png" alt="Transaction Approval"><figcaption><p>Confirm withdrawal in the wallet</p></figcaption></figure></div>

    <br>
{% endstep %}
{% endstepper %}
