# RAVENCOIN ATOMIC ASSET SWAPS
Simple method for engaging in RVN-for-Asset Atomic Swaps for admin assets using the QT.

```Boxes contain an actual testnet example using an admin asset exchanged for 100 tRVN.```

## Step 1:  Alice provisions an asset UTXO for the swap ("lists the asset")

*Alice sends the admin asset to herself at new address.  This generates a TXID.  Capture the TXID and VOUT for the asset UTXO.*

```
	LISTING ADDRESS:
		n3zoToEkAmCjjxDTsJW9ZKcNXth3zCuRtD

	TXID: 
		e8e6f8b7c322fbfd23f5b9e55b69978a7abe7180bc3a2344bcfd73b390c43fe9
```

*Alice then creates a new address to receive Bob's RVN*

```
	ALICE'S RVN RECEIVING ADDRESS: 
		mrrc1r5a2797YF7ShbiRhsmBesx4YLp1mx
```

## Step 2.  Bob constructs the proposed RVN-for-Asset transaction ("creates the bid")

*Bob uses data from Alice's listing to construct a raw transaction:*

Inputs:

1. Bob's RVN
2. Alice's Asset

Outputs:

3. Bob's RVN to Alice
4. Change to Bob
5. Alice's admin Asset to Bob++

Model Raw Transaction:
```
	createrawtransaction "[{\"txid\":\"<TXID OF BOB'S RVN INPUT>\",\"vout\":<VOUT FOR BOB RVN >},{\"txid\":\"<TXID OF ALICE ASSET INPUT>\",\"vout\":<VOUT FOR ALICE ASSET>}]" "{\"<ALICE ADDRESS FOR PURCHASE PRICE>\":<PURCHASE PRICE>,\"<BOB CHANGE ADDRESS FOR RVN>\":<BOB CHANGE>,\"<BOB'S ASSET RECEIVING ADDRESS>\":{\"transfer\":{\"<ASSET NAME>\":<ASSET QTY>}}}"
```
++ **Important Note:**  this model is for our example, which involves a transfer of one admin asset from Alice to Bob.  *If you send an asset with QTY of more than one in the UTXO, you also have to create an asset change output back to Alice in your raw transaction.*

Example transaction...
```
Inputs and outputs:

	1. Bob uses a 101 RVN UTXO: TXID 3de15ee6858e1fa67ac93bbb33317bade4da8c83c33d8176e95afa1903fbec17, VOUT 1

	2. Alice's asset UTXO from step 1 is:  TXID e8e6f8b7c322fbfd23f5b9e55b69978a7abe7180bc3a2344bcfd73b390c43fe9, VOUT 0

	3. Alice's RVN receiving address from step 1 is: mrrc1r5a2797YF7ShbiRhsmBesx4YLp1mx

	4. Bob creates a RVN change address: mfvNcwDgyKeTnwBTgXxL9UibkadvNy5A3L
	
	5. Bob creates an asset receiving address: n2HgKEedSHMUXmkBQyQSc6kiVqVNR837SR.

Raw transaction RPC call with this information using the model:

	createrawtransaction "[{\"txid\":\"3de15ee6858e1fa67ac93bbb33317bade4da8c83c33d8176e95afa1903fbec17\",\"vout\":1},{\"txid\":\"e8e6f8b7c322fbfd23f5b9e55b69978a7abe7180bc3a2344bcfd73b390c43fe9\",\"vout\":0}]" "{\"mrrc1r5a2797YF7ShbiRhsmBesx4YLp1mx\":100,\"mfvNcwDgyKeTnwBTgXxL9UibkadvNy5A3L\":0.5,\"n2HgKEedSHMUXmkBQyQSc6kiVqVNR837SR\":{\"transfer\":{\"MANGO_ATOMIC_TEST!\":1}}}"

Output from the QT:

	020000000217ecfb0319fa5ae976813dc3838cdae4ad7b3133bb3bc97aa61f8e85e65ee13d0100000000ffffffffe93fc490b373fdbc44233abc8071be7a8a97695be5b9f523fdfb22c3b7f8e6e80000000000ffffffff0300e40b54020000001976a9147c60c641bc768a0e546334104191b5081a402b6288ac80f0fa02000000001976a914046ddf8115592b2a388bf034d32f67f00b6e217988ac00000000000000003b76a914e3d7e51460a0edc7a7a101d0163293c6c35afe9188acc01f72766e74124d414e474f5f41544f4d49435f544553542100e1f505000000007500000000
```

## Step 3. Bob signs the RVN UTXO and gives the partially-signed raw transaction to Alice ("makes the bid")

*Bob uses his RVN QT to sign the RVN part of the unsigned raw transaction he got from Alice:*

``` 
signrawtransaction 020000000217ecfb0319fa5ae976813dc3838cdae4ad7b3133bb3bc97aa61f8e85e65ee13d0100000000ffffffffe93fc490b373fdbc44233abc8071be7a8a97695be5b9f523fdfb22c3b7f8e6e80000000000ffffffff0300e40b54020000001976a9147c60c641bc768a0e546334104191b5081a402b6288ac80f0fa02000000001976a914046ddf8115592b2a388bf034d32f67f00b6e217988ac00000000000000003b76a914e3d7e51460a0edc7a7a101d0163293c6c35afe9188acc01f72766e74124d414e474f5f41544f4d49435f544553542100e1f505000000007500000000
```
*Bob retrieves the partially signed transaction so he can give it to Alice:*
```
020000000217ecfb0319fa5ae976813dc3838cdae4ad7b3133bb3bc97aa61f8e85e65ee13d010000006b483045022100d8cdf9f1f3ee6d8155efd8b32bd1cac80fc60ba52503558e1e31b93077ba806502202f9fa23b403f2f45d5c8dbcb534c9a98bef3123d4746c05f93dcd96e5815e4a7012102f464ccd5123cfac19163530e5080b14a7ebbb083e11d0bcc517834c1f8068023ffffffffe93fc490b373fdbc44233abc8071be7a8a97695be5b9f523fdfb22c3b7f8e6e80000000000ffffffff0300e40b54020000001976a9147c60c641bc768a0e546334104191b5081a402b6288ac80f0fa02000000001976a914046ddf8115592b2a388bf034d32f67f00b6e217988ac00000000000000003b76a914e3d7e51460a0edc7a7a101d0163293c6c35afe9188acc01f72766e74124d414e474f5f41544f4d49435f544553542100e1f505000000007500000000
```
The QT will produce an error message here that says "Unable to sign input, invalid stack size (possibly missing key)."  This is expected, because Bob does not have the key to sign Alice's asset UTXO.

## Step 4.  Alice signs the Asset UTXO and broadcasts the fully-signed transaction ("accepts the bid")

*Alice uses her RVN QT to sign the Asset part of the partially-signed raw transaction from Bob:*

```
signrawtransaction 020000000217ecfb0319fa5ae976813dc3838cdae4ad7b3133bb3bc97aa61f8e85e65ee13d010000006b483045022100d8cdf9f1f3ee6d8155efd8b32bd1cac80fc60ba52503558e1e31b93077ba806502202f9fa23b403f2f45d5c8dbcb534c9a98bef3123d4746c05f93dcd96e5815e4a7012102f464ccd5123cfac19163530e5080b14a7ebbb083e11d0bcc517834c1f8068023ffffffffe93fc490b373fdbc44233abc8071be7a8a97695be5b9f523fdfb22c3b7f8e6e80000000000ffffffff0300e40b54020000001976a9147c60c641bc768a0e546334104191b5081a402b6288ac80f0fa02000000001976a914046ddf8115592b2a388bf034d32f67f00b6e217988ac00000000000000003b76a914e3d7e51460a0edc7a7a101d0163293c6c35afe9188acc01f72766e74124d414e474f5f41544f4d49435f544553542100e1f505000000007500000000
```

*Alice retrieves the fully-signed raw transaction from her QT:*
```
020000000217ecfb0319fa5ae976813dc3838cdae4ad7b3133bb3bc97aa61f8e85e65ee13d010000006b483045022100d8cdf9f1f3ee6d8155efd8b32bd1cac80fc60ba52503558e1e31b93077ba806502202f9fa23b403f2f45d5c8dbcb534c9a98bef3123d4746c05f93dcd96e5815e4a7012102f464ccd5123cfac19163530e5080b14a7ebbb083e11d0bcc517834c1f8068023ffffffffe93fc490b373fdbc44233abc8071be7a8a97695be5b9f523fdfb22c3b7f8e6e8000000006b483045022100d94bec968ad989bf559ba6fce5c129c643e99e00e275aeaef43dcfdbaf99afb8022046462b9102bf827929b33e9d04d8a1b9a148d7dd643e7cee1806b4ae4e7530e0012102301159deb54f00aabedbefc02caa676ed885f0a939c6bf6a4757578992151289ffffffff0300e40b54020000001976a9147c60c641bc768a0e546334104191b5081a402b6288ac80f0fa02000000001976a914046ddf8115592b2a388bf034d32f67f00b6e217988ac00000000000000003b76a914e3d7e51460a0edc7a7a101d0163293c6c35afe9188acc01f72766e74124d414e474f5f41544f4d49435f544553542100e1f505000000007500000000
```
*Alice broadcasts the fully-signed transaction to the network:*

```
sendrawtransaction 020000000217ecfb0319fa5ae976813dc3838cdae4ad7b3133bb3bc97aa61f8e85e65ee13d010000006b483045022100d8cdf9f1f3ee6d8155efd8b32bd1cac80fc60ba52503558e1e31b93077ba806502202f9fa23b403f2f45d5c8dbcb534c9a98bef3123d4746c05f93dcd96e5815e4a7012102f464ccd5123cfac19163530e5080b14a7ebbb083e11d0bcc517834c1f8068023ffffffffe93fc490b373fdbc44233abc8071be7a8a97695be5b9f523fdfb22c3b7f8e6e8000000006b483045022100d94bec968ad989bf559ba6fce5c129c643e99e00e275aeaef43dcfdbaf99afb8022046462b9102bf827929b33e9d04d8a1b9a148d7dd643e7cee1806b4ae4e7530e0012102301159deb54f00aabedbefc02caa676ed885f0a939c6bf6a4757578992151289ffffffff0300e40b54020000001976a9147c60c641bc768a0e546334104191b5081a402b6288ac80f0fa02000000001976a914046ddf8115592b2a388bf034d32f67f00b6e217988ac00000000000000003b76a914e3d7e51460a0edc7a7a101d0163293c6c35afe9188acc01f72766e74124d414e474f5f41544f4d49435f544553542100e1f505000000007500000000
```

## Step 5.  Both parties check for a successful broadcast and confirmation

The QT will provide a TXID if Alice's broadcast is successful.  It can be checked on any explorer or node with transaction and asset indexes.  

[Success!](https://api.testnet.ravencoin.org/tx/72b78055a888b680b39ac0d228b7aedcb4a0fce5f298dee5a617746bb7809b98)  You've just done your first RVN-for-Asset atomic swap.
