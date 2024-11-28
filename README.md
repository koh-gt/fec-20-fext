# fec-20-fext

Automatic token deploy/mint/transfer tool ->  `Unavailable`

## FEXT
FEC-20-FEXT uses JSON on FEXT for its operation.  
JSON is written as a key and value pair.  
A JSON name/value pair consists of a key name (in double quotes), followed by a colon, followed by the value (in double quotes)  
An example is as follows: `"time":"10"`

## Create your own token on FEXT manually
This process requires careful attention to several key considerations and configurations to ensure your token functions as intended and meets your goals.  
First, [choose an appropriate standard](#token-standards) for the purpose of your L2 token.  
Then, configure your token deploy parameters. ([`fec-20`](#deploy-fec-20), [`fec-721`](#deploy-fec-721), [`fec-m`](#deploy-fec-m))
Finally, [deploy your token](#broadcast) on FEXT.

## Token standards 
| standard (p) | Use |
|--------------|-----|
| [`fec-20`](#fec-20)     | Standard Token |
| [`fec-721`](#fec-721)    | Non-Fungible Token (NFT) |
| [`fec-m`](#fec-m)      | Multimedia and documents |

There are currently three standards in FEC-20-FEXT - *FEC-20*, *FEC-721* and *FEC-m*
These three Layer-2 (L2)standards are used for fungible tokenisation, non-fungible tokenisation, and multimedia tokenisation.

### FEC-20
FEC-20 is the basic L2 [token protocol on FEXT](ferritecoin.org/fext). It is used to create fungible tokens, where every token in a collection is identical.
FEC-20 has three basic operations (op) - `deploy`, `mint` and `transfer` which serve different functions.

| op | Use | FEXT content example |
|----|-----|---------|
| `deploy`   | Creates a new token.       | `{"p":"fec-20","op":"deploy","tick":"FEXT","max":"60221400","lim":"3461"}` |
| `mint`     | Issues specified token.    | `{"p":"fec-20","op":"mint","tick":"FEXT","amt":"3461"}` |
| `transfer` | Transfers specified token. | `{"p":"brc-20","op":"transfer","tick":"FEXT","amt":"500"}` |
> Note: A token cannot be minted or transferred unless it is deployed.

#### Configure `deploy` `fec-20`
To deploy a token is to launch your very own L2 token.  
You need to decide on the fundamental properties of your token.
```python
{
  "p": "fec-20",      #    "p" - This is the token standard, in this case "fec-20" or Standard Token.
  "op": "deploy",     #   "op" - This specifies the operation to be used in this case "deploy"
  "tick": "FEXT",     # "tick" - The unique ticker. It will be used as an identifier for your token.
  "max": "60221400",  #  "max" - The maximum supply of the token. Must be an integer.
  "lim": "3461"       #  "lim" - The maximum amount of token that can be issued in a single mint operation. Must be an integer.
}
```
> Note: JSON keys and values should be enclosed in double quotes. A human-readable name can be attached later to the ticker.


## Broadcast
`{"p":"fec-20","op":"deploy","tick":"FEXT","max":"60221400","lim":"3461"}`
Ensure that the JSON is smaller than 80 bytes.  

After configuring the parameters, broadcast the JSON on the [web FEXT](ferritecoin.org/fext).  
If a token with your chosen ticker exists, then the deploy is void.
Spaces can be omitted and to save space and transaction fees an single line JSON is recommended.    

Note that while `deploy` broadcasts do not require ownership and can be done on web FEXT, `mint` will issue tokens into the sender's address and `transfer` further requires that the source address sending the broadcast possess the tokens, and also requires a valid destination `vout`.

## Other operations
For more details about manually executing other operations such as `mint` and `transfer` on `fec-20`, or other FEC-20-FEXT standards such as `fec-721` and `fec-m` consider browsing the [fec-20-fext Wiki](https://github.com/koh-gt/fec-20-fext/wiki).

## History of BRC-20

BRC-20 is shorthand for Bitcoin Request for Comment #20. Obviously, Bitcoin does not have "Requests for Comment", but rather pull requests and issues on GitHub. 
This naming is a wordplay on ERC-20 (Ethereum Request for Comment #20) which is the 20th proposal on the ERC system, Ethereum's development protocol to suggest such improvements, similar to BIP on GitHub.

## Why FEC-20-FEXT

The `OP_FALSE OP_IF OP_ENDIF` (Inscription envelope) may have greater data limits than `OP_RETURN`'s 83 bytes, and may even be cheaper due to the SegWit discount.  

However, the overhead to first create the Taproot (P2TR) output and then spend the UTXO in a future transaction can be heavy especially for small transactions.  

We should encourage efficient transactions even if it means that the network "seems less utilised", has lower mining fees for scalability and reducing full-node space requirements.   

Furthermore, `OP_RETURN` data is marked as non-spendable - this opcode is specifically designed to store data on-chain without polluting the UTXO set.

![op_return](https://github.com/user-attachments/assets/5e619c4d-6852-446d-a4fa-2cf9e5d5ab0a)

| Protocol | BRC-20 | FEC-20-FEXT |
|----------|--------|-------------|
| Content | <code>OP_FALSE</code><br><code>OP_IF</code><br><code>  OP_PUSH "ord"</code><br><code>  OP_PUSH 1</code><br><code>  OP_PUSH "text/plain;charset=utf-8"</code><br><code>  OP_PUSH 0</code><br><code>  OP_PUSH "Hello, world!"</code><br><code>OP_ENDIF</code> | <code>OP_RETURN</code><br><code>  <JSON_data></code>
