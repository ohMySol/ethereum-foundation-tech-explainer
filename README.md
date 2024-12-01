# CCIP(cross chain interoperability protocol)

## What is this?
CCIP - is a decentralised alternative to centralised bridges, and it solves an interoperability problem(blockchains are isolated networks and they cannot communicate natively).
This technology allows us to send a cross chain transactions to transfer tokens, messages or even create a programmable token transfers and wrap together value(tokens) with instructions(data/message) on what the receiving smart contract should do with those tokens on the destination chain.

## What we can transfer via CCIP?
1. Arbitrary data(in bytes). We can encode a function call on a source chain and execute this call on a destination chain.
2. Tokens.
3. Both tokens and messages together.

## How does this work?
1. **Message Creation:** The sender creates an `EVM2AnyMessage` structure containing the destination chain, data, tokens, fees, and gas limit. If tokens are included, the sender approves the Router contract to spend them.
2. **Message Submission:** The sender calls `Router.ccipSend()` with the destination chain selector. The Router validates the message and transfers tokens to the appropriate token pool if needed.
3. **Routing:** The Router forwards the message to the `OnRamp` contract for the destination chain. The OnRamp validates the message, emits an event, and returns a message ID. This event triggers the DON network to process the message.
4. **Message Finalization:** Committing DON nodes verify and finalize messages, storing their Merkle roots in the `CommitStore`. The Risk Management Network ensures messages are authentic and secure.
5. **Batching and Reporting:** Executing DON nodes monitor the `CommitStore`, batch finalized messages with Merkle proofs, and transmit a consensus report to the `OffRamp` contract on the destination chain.
6. **Execution:** The OffRamp verifies messages, checks rate limits, and processes token transfers. If the receiver supports the correct interface, the OffRamp calls `ccipReceive()` to enable the receiver to handle the message, data, and tokens, potentially transferring them to the end recipient.

