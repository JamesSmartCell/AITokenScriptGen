Instructions for Creating the TokenScript
1. Understand the Token Requirements:
- Identify the key attributes of the token, such as name, symbol, contract address, and type (NFT).
- Gather any additional information that may be relevant, such as the image URL, description, and any specific functionalities (e.g., minting and burning).

2. Refer to the TokenScript Documentation:
- Review the latest TokenScript documentation to understand the required XML structure, namespaces, and elements.
- Pay attention to the examples provided in the documentation to ensure compliance with the expected format.

3. Define the XML Namespace:
- Start the TokenScript with the appropriate XML namespaces for TokenScript (ts) and Ethereum (ethereum):
- note that the "name" attribute cannot have any spaces, only hyphens. Replace TOKEN-NAME with the name of the token.
```xml
     <?xml version="1.0" encoding="UTF-8" standalone="no"?>
     <ts:token xmlns:ts="http://tokenscript.org/2024/01/tokenscript"
		  xmlns:xml="http://www.w3.org/XML/1998/namespace"
		  xsi:schemaLocation="http://tokenscript.org/2024/01/tokenscript https://www.tokenscript.org/schemas/2024-01/tokenscript.xsd"
		  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
		  xmlns:ethereum="urn:ethereum:constantinople"
		  name="TOKEN-NAME">  
```
4. Add the label data, this is the name of the token and will be used in the UI. Replace TOKEN_NAME with the name of the token.
```xml
    <ts:label>
        <ts:plurals xml:lang="en">
            <ts:string quantity="one">
                TOKEN_NAME Token
            </ts:string>
            <ts:string quantity="other">
                TOKEN_NAME Tokens
            </ts:string>
        </ts:plurals>
    </ts:label>
```
5. Add Token Metadata:
- Include the basic information for the token:     
```xml
    <ts:meta>
        <ts:description xml:lang="en">
        </ts:description>
        <ts:aboutUrl xml:lang="en">
        </ts:aboutUrl>
        <ts:iconUrl xml:lang="en">
            https://ipfs.io/ipfs/QmQgPRvpucr7FgCKXHfAUJaV1a3EoKX3guDBiDt1zozFrv
        </ts:iconUrl>
    </ts:meta>
```
6. Add the Token's Contract Address and Chain ID, and token type (eg ERC-20, ERC-721, etc): Replace CONTRACT_ADDRESS with the contract address, and CHAIN_ID with the chain ID.
```xml
    <ts:contract interface="erc721" name="ThisToken">
        <ts:address network="CHAIN_ID">CONTRACT_ADDRESS</ts:address>
    </ts:contract>
```
7. Specify the Token's Origins:
```xml
    <ts:origins>
        <!-- Define the contract specified previously which holds the token that the user will use -->
        <ts:ethereum contract="ThisToken"/>
    </ts:origins>
```
8. Now add the cards for the actions that the user can perform:
```xml
    <ts:cards>
        <!-- first specify the type of card and the name of the card, along with which contract it is for -->
        <ts:card type="action" name="mint" buttonClass="primary" origins="ThisToken">
            <!-- Add a label which will be displayed as a button on the main token page -->
            <ts:label>
                <ts:string xml:lang="en">Mint</ts:string>
            </ts:label>
            <!-- Add the transaction details for the card -->
            <ts:transaction>
                <ethereum:transaction function="mint" contract="ThisToken">
                    <ts:data/>
                </ethereum:transaction>
            </ts:transaction>
            <!-- Add a view which will be displayed as a page on the token page -->
            <ts:view xmlns="http://www.w3.org/1999/xhtml" xml:lang="en">
                <p>Mint your very own MyToken! Click the Mint button below to receive a token directly to your wallet.</p>
            </ts:view>
        </ts:card>
        <!-- Add additional cards as needed -->
    </ts:cards>
```

Detailed Instructions for each card type:

Let's generate the cards for each solidity function in the contract.

To generate the cards for each solidity function in the contract, we need to follow these steps:

i. Generate the card header. This will always be type="action" for a transaction. for the card name, use the solidity function name. "Origins" will usually be the origin contract defined in step 7.
```xml
<ts:card type="action" name="mint" buttonClass="primary" origins="ThisToken">
```
ii. Generate the label. This will be the label of the button on the main token page.
```xml
<ts:label>
    <ts:string xml:lang="en">Mint</ts:string>
</ts:label>
```
iii. Generate the transaction. This will be the transaction details for the card.

In order to generate the transaction, we need to know the function name, and parameters.
for Example here's an ERC20 mint function:
```solidity
function mint(address to, uint256 amount) public
```
Here's the XML for that transaction. Note that there are two parameters in this function, "to" and "amount". These are the parameters that the user will input, as such they are refered to as local-ref. "ref" refers to global types such as the contract address, tokenId.
```xml
<ts:transaction>
    <ethereum:transaction function="mint" contract="ThisToken">
        <ts:data>
                <ts:address local-ref="to" />
                <ts:uint256 local-ref="amount" />
        </ts:data>
    </ethereum:transaction>
</ts:transaction>
```

iv. Generate the view. This will be the view that is displayed when the card is clicked.
```xml
<ts:view xmlns="http://www.w3.org/1999/xhtml" xml:lang="en">
        <p>Enter the address to mint tokens to and the amount of tokens to mint.</p>
        <label for="to">Recipient Address:</label>
        <input type="text" id="to" name="to" placeholder="0x..."/>
        <label for="amount">Amount:</label>
        <input type="number" id="amount" name="amount" placeholder="100"/>
</ts:view>
```
v. Close the card tag
```xml
</ts:card>
```



9. Now close the token tag
```xml
</ts:token>
```

Final Review and Testing:
- Review the entire TokenScript for correctness, ensuring that all required fields are filled in and that the XML structure is valid.
- Test the TokenScript in a suitable environment to ensure it behaves as expected.