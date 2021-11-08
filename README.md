# Step-by-step guide to deploy an NFT on Ethereum, OpenSea and IPFS

If you like what you see, please donate some BTC to bc1qf3gsvfk0yp9fvw0k8xvq7a8dk80rqw0apcy8kx or some ETH to 0x7e674C55f954d31B2f86F69d7A58B2BCe84Cf6b4

## Introduction
A simple no non-sense guide to deploy your [NFT](https://en.wikipedia.org/wiki/Non-fungible_token) on Ethereum and have it automatically displayed on OpenSea.  
References:

- We will use [Metamask](https://metamask.io/) to fund the smart contract deployment
- We will use [Remix](http://remix.ethereum.org/) to code the smart contract - and deploy it
- We will use [OpenSea](https://opensea.io/) to display our NFT - Metamask will be used to log in
- Our NFT will be saved on [IPFS](https://ipfs.io/) via [Pinata](https://www.pinata.cloud/)
- Finally, we will use [Rinkeby](https://www.rinkeby.io/) as a test network

## NFT Smart Contract

We will use the code provided by [OpenZeppelin](https://docs.openzeppelin.com/contracts/4.x/erc721):

```solidity
pragma solidity ^0.8.0;

import "@openzeppelin/contracts/token/ERC721/extensions/ERC721URIStorage.sol";
import "@openzeppelin/contracts/utils/Counters.sol";
import "@openzeppelin/contracts/access/Ownable.sol";

contract AcmeNFT is ERC721URIStorage, Ownable {
    using Counters for Counters.Counter;
    Counters.Counter private _tokenIds;

    constructor() ERC721("ACME", "ACM") {}

    function awardItem(address invitee, string memory tokenURI) public onlyOwner returns (uint256) {
        _tokenIds.increment();

        uint256 newItemId = _tokenIds.current();
        _mint(invitee, newItemId);
        _setTokenURI(newItemId, tokenURI);

        return newItemId;
    }
}
```

In the code above 'ACME' is the name and 'ACM' the symbol.  
The 'awardItem' function will be invoked to 'mint' a token, or associate a token to an address 'invitee'.

### Create a NFT design
Using your artistic skills, design a movie, image or animated image.
This is mine (I used [PhotoFunia](https://photofunia.com/) on one of my pics):
![rhonolac.jpg](./rhonolac.jpg)

### Storing the NFT design
We need to store the design as well as some metadata. Several options for storage are possible, github, Google cloud, etc.
[IPFS](https://en.wikipedia.org/wiki/InterPlanetary_File_System) seems to be the one that people tend to use.

The metadata is a JSON file:

```json
{
  "description": "NFT Step-By-Step",
  "external_url": "TODO",
  "image": "TODO",
  "name": "ACM",
  "attributes": [
    {
      "trait_type": "Base",
      "value": "Hash of Image"
    },
    {
      "trait_type": "Personality",
      "value": "Happy"
    }
  ]
}
```

The 'external_url' can point to a collection within OpenSea. You can go to [https://testnets.opensea.io/collection/create](https://testnets.opensea.io/collection/create)
and create your collection and place update 'external_url'.  
Mine is [https://testnets.opensea.io/collection/canoe-nft-step-by-step](https://testnets.opensea.io/collection/canoe-nft-step-by-step)

The next step is first to upload the image to IPFS - then the json (once we have the URL for the image).
My image via Pinata is: [https://gateway.pinata.cloud/ipfs/QmPpSAxeg8mJGyvaogYfqXJpmZHMNkkvWaBFVpmHmhMzYM](https://gateway.pinata.cloud/ipfs/QmPpSAxeg8mJGyvaogYfqXJpmZHMNkkvWaBFVpmHmhMzYM)

Once done, the JSON looks like:

```json
{
  "description": "NFT Step-By-Step",
  "external_url": "https://testnets.opensea.io/collection/canoe-nft-step-by-step",
  "image": "https://gateway.pinata.cloud/ipfs/QmPpSAxeg8mJGyvaogYfqXJpmZHMNkkvWaBFVpmHmhMzYM",
  "name": "ACM",
  "attributes": [
    {
      "trait_type": "Base",
      "value": "Hash of Image"
    },
    {
      "trait_type": "Personality",
      "value": "Happy"
    }
  ]
}
```

Now we can upload the json onto IPFS via Pinata: [https://gateway.pinata.cloud/ipfs/QmTKNXG5E94FELcbNeoj55x5Ay4MvoSZb9n4n8As8KzmW7](https://gateway.pinata.cloud/ipfs/QmTKNXG5E94FELcbNeoj55x5Ay4MvoSZb9n4n8As8KzmW7)

This link is effectively what will be 'minted' or 'associated' to an address in the Solidity smart contract above.

### Publication of the smart contract on Rinkeby

Publish via Remix, using Metamask on Rinkeby: this is the [transaction](https://rinkeby.etherscan.io/tx/0x8f7cb25711e73a80886200a5f6d619549cba8f26b05b7e2f62fc06c36d4dccf8) and
the [contract](https://rinkeby.etherscan.io/address/0x594f1298017b702b3e871cfc12f23dad2053b40b).

### Mint the NFT to an address

Next step is to link the Pinata Json metadata to an address.
I will use my '0xD1eCf18C4703F524B787B9A44d7EcF58365EA74F' address to receive the NFT.
Use Remix for invoking the 'awardItem' call:

![remix.PNG](./remix.PNG)

The resulting [transaction](https://rinkeby.etherscan.io/tx/0x52052233dccea0712e21f4f34c24565ad09d3b45733c126df9499ea3ae45dce9).

### Check on OpenSea

Once the call is successful, depending on how busy the network and OpenSea is, you can see the result directly on [OpenSea](https://testnets.opensea.io/assets/0x594f1298017b702b3e871cfc12f23dad2053b40b/1).
