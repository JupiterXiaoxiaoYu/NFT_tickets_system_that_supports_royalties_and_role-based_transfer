## Overview
"The Smart ASA introduced with ARC-0020 represents a new building block for blockchain applications. It offers a more flexible way to work with ASAs providing re-configuration functionalities and the possibility of building additional business logic around operations like asset transfers, royalties, role-based transfers, limit-amount-transfers, mints, and burns. "

This project aims to build a demo of an NFT ticketing system using the Smart ASA with ARC-0020, which supports customized asset transfer methods including royalties and discounts. 

## A better NFT ticketing system using ARC-0020
Relying on the encryption and anti-counterfeiting capabilities provided by the Algorand blockchain, NFT can easily realize data transparency, non-forgeability, non-duplication, and controllable circulation. In the real world, we have noticed that movie tickets, zoo tickets, and park annual tickets can all be regarded as vouchers with unique IDs in essence. Based on this feature, NFT is very suitable for the ticket market.

Someone may have such a question: the existing ticket market has realized online ticket purchase, online payment, and QR code ticket verification, which is a good experience, is there any need to improve?

In fact, the existing ticketing systems are scattered and centralized. For example, there are many online platforms and systems which sell movie tickets, however, these systems cannot be interconnected. If every theater or cinema needs to connect with so many ticketing systems, the IT cost for those will be very high.

In addition, cinemas cannot reach those users directly because of these third-party systems that users are directly facing, even a discount message cannot be pushed to users by these cinemas.

So how do we solve the problem? The NFT technology based on blockchain is an effective measure to reduce the IT cost of the ticketing system. All the existing ticketing systems will never share the user and ticket purchasing data as their own private data storage. On the contrary, NFT data is open and transparent on the chain. Any theater or cinema only needs to distribute all movie tickets of a movie on the blockchain in the form of NFT, which is equivalent to completing the "registration and distribution" of the primary market. Any third-party ticketing system, even individuals, can sell NFT movie tickets of the primary market to other users, which is called "secondary market sales". Since NFT tickets cannot be forged, there is no fake ticket problem. The cinema itself does not rely on any third-party system for ticket verification. It can verify NFT movie tickets by itself only by synchronizing blockchain data.

Because NFT is also programmable, especially as we implement the ticking system with ARC-0020, the publisher can completely customize the primary sales and secondary sales rules. For example, some tickets are not allowed to be transferred, and some tickets can be transferred, but a certain handling fee or royalties will be charged. These flexible rules do not need to be defined by third parties, and the issuer can create them in the form of a smart contract.

Finally, because the distribution and circulation data of NFT are completely transparent, cinemas can analyze their own user data to improve their services to users. 

To sum up, an ARC0020-based ticketing system will have the following significant advantages:
Security: Each NFT ticket has the characteristics of traceability, anti-counterfeiting, and verifiability;
Low cost: The issuer does not need to integrate with multiple third-party ticketing systems, and any third party with technical and sales capabilities can act as a sales channel;
Transparency: All transaction data is stored on the blockchain, and the data is no longer proprietary to a third party.

## Implementation in this project
This implementation is based on https://github.com/algorandlabs/smart-asa, I create new contract methods to allow transfer with royalties. Specifically, the customized loyalty rate is 0.2, which means sending 10 tickets will actually send only 8 tickets and the other 2 tickets are sent back to the smart contract. This could be customized, either the rate or the asset of royalties. For example, you can use Algo as the royalty charing asset, and you can set the royalty rate per ticket, for instance, 1 Algo per ticket. 

The method of royalty transfer is shown below: 
```
def smart_asa_transfer_inner_txn_with_royaties(
    smart_asa_id: Expr,
    asset_amount: Expr,
    asset_sender: Expr,
    asset_receiver: Expr,
) -> Expr:
    return Seq(
        InnerTxnBuilder.Begin(),
        InnerTxnBuilder.SetFields(
            {
                TxnField.fee: Int(0),
                TxnField.type_enum: TxnType.AssetTransfer,
                TxnField.xfer_asset: smart_asa_id,
                TxnField.asset_amount: asset_amount/Int(10)*Int(9),
                TxnField.asset_sender: asset_sender,
                TxnField.asset_receiver: asset_receiver,
            }
        ),
        InnerTxnBuilder.Submit(),
        InnerTxnBuilder.Begin(),
        InnerTxnBuilder.SetFields(
            {
                TxnField.fee: Int(0),
                TxnField.type_enum: TxnType.AssetTransfer,
                TxnField.xfer_asset: smart_asa_id,
                TxnField.asset_amount: asset_amount/Int(10),
                TxnField.asset_sender: asset_sender,
                TxnField.asset_receiver: App.globalGet(GlobalState.manager_addr),
            }
        ),
        InnerTxnBuilder.Submit(),
    )
```



## Reference
