 ## I want to save patient data (name, age, weight, height) in Lit and I want to use the Datil-dev net. How could I do that? The data should be encrypte</user>

To save and encrypt patient data using the Lit Protocol on the Datil-dev network, you can follow these steps:

1. Install the Lit SDK: Ensure you have the necessary packages installed.

2. `npm i @lit-protocol/lit-node-client @lit-protocol/constants @lit-protocol/auth-helpers`

3. Set Up the Lit Client: Connect to the Lit network using the Lit SDK.
```
import * as LitJsSdk from "@lit-protocol/lit-node-client";
import { LitNetwork } from "@lit-protocol/constants";

class Lit {
  litNodeClient;
  chain;
  constructor(chain) {
    this.chain = chain;
  }
  async connect() {
    this.litNodeClient = new LitJsSdk.LitNodeClient({
      litNetwork: "datil-dev",
    });
    await this.litNodeClient.connect();
  }
}

const chain = "ethereum";
let myLit = new Lit(chain);
await myLit.connect();

```

4. Define Access Control Conditions (ACCs): Specify who can decrypt the data.

```
const accessControlConditions = [
  {
    contractAddress: '',
    standardContractType: '',
    chain: 'ethereum',
    method: '',
    parameters: [':userAddress'],
    returnValueTest: {
      comparator: '=',
      value: '0xYourEthereumAddressHere',
    },
  },
];
```

5. Encrypt the Patient Data: Encrypt the data using the Lit SDK.

```
    const dataToEncrypt = JSON.stringify({
      name: "John Doe",
      age: 30,
      weight: 70,
      height: 175,
    });

    const { encryptedString, symmetricKey } = await LitJsSdk.encryptString(
      dataToEncrypt
    );

    const encryptedSymmetricKey = await myLit.litNodeClient.saveEncryptionKey({
      accessControlConditions,
      symmetricKey,
      authSig,
      chain: 'ethereum',
    });
```

6. Store the Encrypted Data: Save the encryptedString and encryptedSymmetricKey to your storage solution (e.g., IPFS, Arweave).

For more detailed guidance, you can refer to the Lit Protocol Quick Start Guide.

## oh, so litprotocol does not provide a storage?

Correct, Lit Protocol does not provide storage for the encrypted data itself. It is used to generate and manage encryption keys and enforce access control. You will need to store the encrypted data and metadata using a storage provider of your choice, such as IPFS, Arweave, or a centralized storage solution.

For more details, you can check the Lit Protocol Quick Start Guide.

## so lets assume I saved the encryptedSymmetricKey and the encryptedString to my storage solution, how can I retrieve and decrypt the data?

To retrieve and decrypt the data using Lit Protocol, follow these steps:

    Retrieve the Encrypted Data: Fetch the encryptedString and encryptedSymmetricKey from your storage solution.

    Connect to the Lit Network: Ensure you are connected to the Lit network as shown in the previous setup.

    Generate Session Signatures: Obtain session signatures required for decryption.

    Decrypt the Data: Use the decryptToString method to decrypt your data.

Here's a code snippet to guide you through the decryption process:
```
async function decryptData(encryptedString, encryptedSymmetricKey) {
  // Ensure connection to Lit Network
  if (!myLit.litNodeClient) {
    await myLit.connect();
  }

  // Generate an authentication signature
  const authSig = await LitJsSdk.checkAndSignAuthMessage({ chain: "ethereum" });

  // Define the access control conditions
  const accessControlConditions = [
    {
      contractAddress: '',
      standardContractType: '',
      chain: 'ethereum',
      method: '',
      parameters: [':userAddress'],
      returnValueTest: {
        comparator: '=',
        value: '0xYourEthereumAddressHere',
      },
    },
  ];

  // Retrieve the symmetric key
  const symmetricKey = await myLit.litNodeClient.getEncryptionKey({
    accessControlConditions,
    toDecrypt: encryptedSymmetricKey,
    chain: 'ethereum',
    authSig,
  });

  // Decrypt the string
  const decryptedString = await LitJsSdk.decryptString(
    encryptedString,
    symmetricKey
  );

  return decryptedString;
}

// Usage
const decryptedData = await decryptData(encryptedString, encryptedSymmetricKey);
console.log(decryptedData);
```

This code assumes you have already retrieved the encryptedString and encryptedSymmetricKey from your storage. For more details, you can refer to the Lit Protocol Decryption Guide.

## `const authSig = await LitJsSdk.checkAndSignAuthMessage({ chain: "ethereum" });` <- will this prompt the users wallet to sign?

Yes, the checkAndSignAuthMessage function will prompt the user's wallet to sign a message. This is part of the authentication process to generate an authSig, which is necessary for interacting with the Lit Protocol. The user will need to approve the signature request in their wallet, such as MetaMask, to proceed.

For more details, you can refer to the Lit Protocol Authentication Guide. Please make sure to have the user questions as titles and the answer as content
