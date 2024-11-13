Operation of the QUBIC Token Bridge
===================================

The QUBIC Token Bridge securely facilitates the transfer of native **QUBIC** tokens between the **Qubic** and **Ethereum** and EVM compatible blockchains. It utilizes smart contracts and a middleware backend to validate and coordinate operations, ensuring smooth and secure transactions across both networks.

This is a proposal for the bridge operation.

Bridge Mechanism
----------------

This bridge employs a classical **lock and mint** mechanism, with a main smart contract on Qubic and another one on Ethereum, to guarantee token equivalence and security:

*   **From Qubic to Ethereum**: **QUBIC** tokens are **locked** on the Qubic network, and equivalent **WQUBIC** tokens are **minted** on Ethereum.

*   **From Ethereum to Qubic**: **WQUBIC** tokens are **burned** on Ethereum, and equivalent **QUBIC** tokens are **released** on the Qubic network.

System Components
-----------------

*   **Bridge Contract on Qubic**: Initiates transfers by taking the tokens from the user's wallet and retaining them until the transfer is confirmed, after which they are moved to the lock contract. It emits transfer events to inform the backend and frontend about the transaction status. Also receives transfers from the Ethereum bridge contract.

*   **Lock Contract on Qubic**: Stores the **QUBIC** tokens locked by the bridge. Each token locked in this contract must be backed by an equivalent token minted on Ethereum.

*   **Bridge Contract on Ethereum**: Mints and burns **WQUBIC** tokens based on instructions received from the backend. Each token minted on Ethereum must be backed by a token locked in the Qubic lock contract. Also initiates transfers to the Qubic bridge contract.

*   **Middleware Backend**: Acts as an intermediate coordinator, ensuring proper execution of transactions between both chains. From the perspective of the bridge, tokens are **pulled** from one blockchain and **pushed** to the other. It also holds the required keys to operate the bridge contracts.

*   **Tokens**:

    *   **QUBIC**: The Qubic network's native token.

    *   **WQUBIC**: The Ethereum ERC20 token representing **QUBIC**.



Transfer Process
----------------

### 1\. Transfer from Qubic to Ethereum

1.  **Initiation**: The user calls the bridge contract on Qubic via the frontend, specifying the amount to transfer and the destination wallet address on Ethereum.

2.  **Pulling**: The bridge contract verifies the information, takes the user's **QUBIC** tokens, and emits a tokens pulled event. The tokens are retained in the contract until the push on Ethereum is confirmed.

3.  **Pull Coordination**: The **backend** captures the tokens pulled event, verifies the information, and calls the bridge contract on Ethereum, specifying the number of tokens to push and the destination wallet.

4.  **Pushing**: The bridge contract on Ethereum verifies the information and mints **WQUBIC** tokens in the destination wallet. A tokens pushed event is emitted to confirm the operation.

5.  **Push Coordination**: The **backend** captures the tokens pushed event and confirms to the bridge contract on Qubic that the push was successful.

6.  **Completion**: The bridge contract on Qubic transfers the retained **QUBIC** tokens to the lock contract and emits a transfer completion event.

7.  **User Notification**: The frontend captures the transfer completion event and displays a confirmation of the operation to the user.


### 2\. Transfer from Ethereum to Qubic

1.  **Initiation**: The user calls the bridge contract on Ethereum via the frontend, specifying the amount of **WQUBIC** tokens to transfer and the destination wallet address on Qubic.

2.  **Pulling**: The bridge contract on Ethereum verifies the information and takes the user's **WQUBIC** tokens, emitting a tokens pulled event. The tokens are retained until the push on Qubic is confirmed.

3.  **Pull Coordination**: The **backend** captures the tokens pulled event, verifies the information, and calls the bridge contract on Qubic, specifying the number of tokens to push and the destination wallet.

4.  **Pushing**: The bridge contract on Qubic verifies the information and transfers the **QUBIC** tokens from the lock contract to the destination wallet, emitting a tokens pushed event.

5.  **Push Coordination**: The **backend** captures the tokens pushed event and confirms to the bridge contract on Ethereum that the push was successful.

6.  **Completion**: The bridge contract on Ethereum burns the retained tokens and emits a transfer completion event.

7.  **User Notification**: The frontend captures the transfer completion event and displays a confirmation of the operation to the user.




Security Measures
-----------------

Additional protective mechanisms against potential risks in the bridge's operation will be detailed later.