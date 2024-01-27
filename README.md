# Mvc20 Interface 

## Interface Entrance 

- mainnet: https://api.mvc20.space/

## Interface Introduction

## 1. Get all listed token pairs

### Request
- Method: **GET**
- URL: ```/allpairs```

### Response
```
{
    "code": 0,
    "data": {
        "mvc20-abcd": {
            "mvc20Token": {
                "symbol": "abcd",
                "tokenID": "e60bfacf9ee1c57caf950cbc14a4bedbc45aa29ab2e76edcaf8f1833bf7c56e900000000",
                "genesis": "338bdbb0ec3faedc9f9f79d850c58d638977924a",
                "codeHash": "a2421f1e90c6048c36745edd44fad682e8644693",
                "decimal": 8
            },
            "maxTokenAmount": "2100000000000000",
            "maxMintAmount": "210000",
            "mintedAmount": "3",
            "burnSats": 100000
        }
    }
}
```

When code is 0, it means success. Otherwise it means failed and there will be a msg which describes the specific information.

data format:
> * mvc20Token: token1 info.
> * maxTokenAmount: token max amount.
> * maxMintAmount: token max mint amount.
> * mintedAmount: the amount of minted times.
> * burnSats: the burn sats for each mint.

**Note: The request header of this interface must include {Accept-Encoding: gzip}**

## 2. Get Mvc20 Info

### Request
- Method: **GET**
- URL: ```/mvc20info?symbol=mvc20-abcd```

> * symbol: mvc20 token symbol which got from ```/allpairs```.

### Response
```
{
    "code": 0,
    "msg": "",
    "data": {
        "maxTokenAmount": "2100000000000000",
        "maxMintAmount": "210000",
        "mintedAmount": "3"
    }
}
```

data format:

> * maxTokenAmount: token max amount.
> * maxMintAmount: token max mint amount.
> * mintedAmount: the amount of minted times.

## 3. Requst mvc20 arguments

Before doing mvc20 operation, you need request mvc20 args.

### Request

- Method: **POST**

- URL: ```/reqmvc20args```

- Body:
```
{
    symbol: "mvc20-abcd",
    address: "msREe5jsynP65899v1KJCydf6Sc9pJPb8S",
    op: 0,
    source: 'mvcswap.io'
}
```

> * symbol: mvc20 symbol.
> * address: the user's address used for accepting mvc and tokens.
> * op: mvc20 operation: 0: mint.
> * source: mark the identity of the caller to make it easier to find errors.

### Response
```
{
    code: 0,
    msg: "",
    data: {
        requestIndex: "1", 
        mvcToAddress: "mzJR1zKcZCZvMJj87rVqmFFxmaVEe62BBW", 
        txFee: 10000, 
        op: 0
    },
}
```

**Note: requestIndex can't be used repeatedly. Need call ```/reqmvc20args``` before every mvc20 operation.**

data format:

> * requestIndex: request id to idendify this request.
> * mvcToAddress: mvcAddress for mvc20 to accept mvc transfer.
> * txFee: miner fee of this mvc20 opeartion.
> * op: mvc20 operation. 0: mint

## 4. Mint

### Request
- Methos: **POST**
- URL: ```/mint```
- Body: 
```
{
    data: compressedData
}
```

compressData format:
```
data = {
    symbol: "mvc20-mvcs",
    requestIndex: "1",
    mvcRawTx: "",
    mvcOutputIndex: 0,
}
compressData = gzip(JSON.stringify(data))
```

> * symbol: mvc20 symbol.
> * requestIndex: returned in ```/reqmvc20args```.
> * mvcRawTx: The raw transaction for transfering mvc to mvcAddress.
> * mvcOutputIndex: The outputIndex of transaction for mvc transfering.

**Note2: Do not broadcast rawTx，just send to mvc20 api. And the data need to be compressed to compressedData with request header {'Content-Type': 'application/json'}. There is a reference code in typescript:**

### Response
```
{
    "code": 0,
    "msg": "",
    "data": {
        txid: '1649c55319187fc7047f0bb372e89b5d2e2c716ce7e387470e3c0460d19065a6',
    }
}
```

data format:

> * txid: the mvc20 transaction id.
   
## 5. Requst create new mvc20 token

### Request

- Method: **POST**

- URL: ```/reqcreatemvc20```

- Body:
```
{
    address: "msREe5jsynP65899v1KJCydf6Sc9pJPb8S",
}
```

> * address: the user's address used for accepting mvc and tokens.

### Response
```
{
    code: 0,
    msg: "",
    data: {
        requestIndex: "1", 
        mvcToAddress: "mzJR1zKcZCZvMJj87rVqmFFxmaVEe62BBW", 
        txFee: 30000, 
    },
}
```

**Note: requestIndex can't be used repeatedly. Need call ```/reqcreatemvc20``` before every mvc20 operation.**

data format:

> * requestIndex: request id to idendify this request.
> * mvcToAddress: mvcAddress for mvc20 to accept mvc transfer.
> * txFee: miner fee of this mvc20 opeartion.


## 4. Create new MVC-20 token

### Request
- Methos: **POST**
- URL: ```/createmvc20```
- Body: 
```
{
    data: compressedData
}
```

compressData format:
```
data = {
    requestIndex: "1",
    mvcRawTx: "",
    mvcOutputIndex: 0,
    tick: "abcd",
    decimal: 4,
    max: "21000000",
    lim: "100",
    burnSats: 100000,
}
compressData = gzip(JSON.stringify(data))
```

> * symbol: mvc20 symbol.
> * requestIndex: returned in ```/reqcreatemvc20```.
> * mvcRawTx: The raw transaction for transfering mvc to mvcAddress.
> * mvcOutputIndex: The outputIndex of transaction for mvc transfering.
> * tick: token tick, only support 4 bytes lowercase letter.
> * max: max token amount.
> * lim: the token amount for each mint.
> * burnSats: the burned sats for each mint, at least 100000 sats.

**Note2: Do not broadcast rawTx，just send to mvc20 api. And the data need to be compressed to compressedData with request header {'Content-Type': 'application/json'}. There is a reference code in typescript:**

### Response
```
{
    "code": 0,
    "msg": "",
    "data": {
        mvc20txid: '1649c55319187fc7047f0bb372e89b5d2e2c716ce7e387470e3c0460d19065a6',
    }
}
```

data format:

> * mvc20txid: the mvc20 transaction id.