[![pypi](https://img.shields.io/pypi/v/portalsmp.svg)](https://pypi.org/project/portalsmp/) ![PyPI - Downloads](https://img.shields.io/pypi/dm/portalsmp)
 [![stars](https://img.shields.io/github/stars/bleach-hub/portalsmp?style=social)](https://github.com/bleach-hub/portalsmp/stargazers) [![Me](https://img.shields.io/badge/Telegram-@perfectlystill-blue?logo=telegram)](https://t.me/perfectlystill) [![Updates & Devs chat](https://img.shields.io/badge/Telegram-@giftsdevs-blue?logo=telegram)](https://t.me/giftsdevs)

# Portals Marketplace API

This is a simple module for interacting with Portals API.

Huge thanks to boostNT [for his code sample](https://github.com/boostNT/Portals-API) of getting `authData`

*[Join our devs chat!](t.me/giftsdevschat)*

#### Installation

`pip install portalsmp`

## Change log

#### Version 1.0

- First Beta release of the module. Working on most of the functions and bug fixes. Send your feedback to my [telegram](t.me/perfectlystill)

#### Version 1.0.1

- Removed `owner_id` from `buy()` function and `PortalsGift` class
- `makeOffer()` should be fixed now.

#### Version 1.1

- Added functions for working with offers: `myPlacedOffers()`, `myReceivedOffers()`, `myCollectionOffers()`, `collectionOffer()`, `cancelCollectionOffer()`
- More in updated documentation

#### Version 1.1.1

- Added `topOffer()` function

#### Version 1.1.2

- Added list support for `marketActivity()` function (activityType=str|list)

#### Version 1.1.3

- `cancelOffer()` should work now
- Returned `topOffer()` function.

#### Version 1.1.4

- Fixed `marketActivity()` function

#### Version 1.2

- Offers: added `editOffer()`, `allCollectionOffers()` functions
- Misc: added `filterFloors()` function
- Switched from pyrogram to kurigram in dependencies, also removed pycryptodome from there.

## Getting started (authData)

Unlike Tonnel Marketplace, every request in Portals needs to be authenticated.

The authentication system is also more complicated. There are currently 2 ways to get authData for Portals:

##### Telegram Web

- Pros: easy, doesn't require Telegram login.
- Cons: authData from Telegram Web is likely to have limited time (i think its around 1-7 days), so you will need to complete all the steps over and over

1) Go to web.telegram.org
2) Find @portals bot
3) Open developer console -> Network tab
4) Open @portals mini app
5) Find any request related to portals-market.com (Network tab)
6) Look for "Authorization" in request headers. It must look like this: "tma *(auth)*". Copy it entirely with "tma" at the start.

##### update_auth()

- Pros: you don't need to waste your time on web.telegram.org; if auth token is expired - its not a big problem, simply use update_auth() again
- Cons: api_id, api_hash and Telegram login needed. Pyrogram used.

*Make sure to have latest version of pyrogram and tgcrypto before using*

1. Go to my.telegram.org/auth and create app
2. Copy your api_id and api_hash
3. Get your authData like this: `token = asyncio.run(update_auth(api_id, api_hash))`
4. Use it in your code

---

## Some examples

```python
search(gift_name="toy bear", model="wizard", limit=10, authData=token)
```

```json
[
  {
    "id": "65c83c42-0ab7-42cc-a46d-6158057a7216",
    "tg_id": "294992",
    "collection_id": "060d4cef-3a21-47e6-98fc-cd338ae0b5e0",
    "external_collection_number": 41771,
    "owner_id": 488711606,
    "name": "Toy Bear",
    "photo_url": "https://nft.fragment.com/gift/toybear-41771.large.jpg",
    "price": "44",
    "attributes": [
      {
        "type": "model",
        "value": "Wizard",
        "rarity_per_mille": 1.5
      },
      {
        "type": "symbol",
        "value": "Champagne",
        "rarity_per_mille": 0.2
      },
      {
        "type": "backdrop",
        "value": "Silver Blue",
        "rarity_per_mille": 2
      }
    ],
    "listed_at": "2025-06-12T16:28:05.550053Z",
    "status": "listed",
    "animation_url": "https://nft.fragment.com/gift/toybear-41771.lottie.json",
    "emoji_id": "5289667857599197149",
    "has_animation": true,
    "floor_price": "31.43",
    "unlocks_at": "2025-03-10T11:04:26Z"
  },...
```

Can be wrapped using `PortalsGift` class

```python
gift = PortalsGift(search(gift_name="toy bear", model="wizard", limit=10, authData=token)[0])
```

`gift.id` - Portals ID of the gift

`gift.tg_id` - Telegram ID of the gift *(external_collection_number)*

`gift.collection_id` - Portals ID of the gift collection

`gift.owner_id` - ID of the gift owner

`gift.name` - Name of the gift

`gift.photo_url` - Photo URL of the gift (model + bg + symbol preview)

`gift.price` - Price of the gift

`gift.model` - Model name of the gift

`gift.model_rarity` - Model rarity of the gift

`gift.symbol` - Symbol of the gift

`gift.symbol_rarity` - Symbol rarity of the gift

`gift.backdrop` - Backdrop of the gift

`gift.backdrop_rarity` - Backdrop rarity of the gift

`gift.listed_at` - Time the gift was listed

`gift.status` - (usually listed)

`gift.animation_url` - Lottie animation URL of the gift

`gift.emoji_id` - Telegram custom emoji ID of the gift

`gift.floor_price` - Floor price of the gift (not the model)

`gift.unlocks_at` - Time of when the gift will be available to be minted

---

## Functionality

#### search()

```python
search(sort: str="price_asc", offset: int=0, limit: int=20, gift_name: str|list="", model: str|list="", backdrop: str|list="", symbol: str|list="", min_price: int=0, max_price: int=100000, authData: str="")
```

- Search for gifts with any filters.
- Available sorts: `"price_asc", "price_desc", "latest", "gift_id_asc", "gift_id_desc", "model_rarity_asc", "model_rarity_desc"`
- `offset` is a "page", but a little bit different. Basically you need to multiply your `limit` by a page you want to get.
- *Also now available to pass a list of gift names / models etc!*

#### giftsFloors()

```python
giftsFloors(authData: str="") -> dict
```

- Returns floors for all the gifts (short names only)

### filterFloors()

```python
filterFloors(gift_name: str = "", authData: str = "") -> dict
```

- Returns a dict of floors of all models/backdrops/symbols for specified gift collection
- Usage: `filterFloors(gift_name="toy bear", authData="...")["models"]` - will return all models of Toy Bear gift collection with floors etc.

#### myPortalsGifts()

```python
myPortalsGifts(offset: int=0, limit: int=20, listed: bool=True, authData: str="") -> list
```

- Returns a list containing your owned gifts.
- `listed=True` for listed gifts, `listed=False` for unlisted gifts.

#### myPoints()

```python
myPoints(authData: str="") -> dict
```

- Returns information about your Portals points.

#### myBalances()

```python
myBalances(authdata: str="") -> dict
```

- Returns information about your balances.

#### myActivity()

```python
myActivity(offset: int=0, limit: int=20, authData: str="") -> list
```

- Returns a list object with all your activities on the market.

#### collections()

```python
collections(limit: int=100, authData: str="") -> list
```

- Returns a list object with all collection names, floors, daily volumes etc.

#### marketActivity()

```python
marketActivity(sort: str="latest", offset: int=0, limit: int=20, activityType: str="", gift_name: str|list="", model: str|list="", backdrop: str|list="", symbol: str|list="", min_price: int=0, max_price: int=100000, authData: str="") -> list
```

- Like `saleHistory()` in `tonnelmp`
- Activity types: `"", "buy", "listing", "price_update", "offer"`
- Available sorts: `"price_asc", "price_desc", "latest", "gift_id_asc", "gift_id_desc", "model_rarity_asc", "model_rarity_desc"`

#### sale()

```python
sale(nft_id: str="", price: int|float=0,authData: str="") -> dict|None
```

- List a single nft for sale

#### bulkList()

```python
bulkList(nfts: list= [], authData: str="") -> dict
```

- List multiple nfts for sale
- *Not tested yet*

#### buy()

```python
buy(nft_id: str="", owner_id: int=0, price: int|float=0, authData: str="") -> dict|None
```

- Buy a single nft
- *Will add bulkBuy soon*

#### makeOffer()

```python
makeOffer(nft_id: str="", offer_price: float=0, expiration_days: int=7, authData: str="") -> dict|None
```

- Make offer for nft
- `expiration_days`: either 0 or 7. 0 means no expiration, 7 - 7 days.

#### cancelOffer()

```python
cancelOffer(offer_id: str="", authData: str="") -> dict|None
```

- Cancel offer with known offer_id

#### editOffer()

```python
editOffer(offer_id: str = "", new_price: float = 0, authData: str = "") -> None
```

- Edit the price of the offer with known `offer_id`

#### collectionOffer()

```python
collectionOffer(gift_name: str = "", amount: float | int = 0, expiration_days: int = 7, max_nfts: int = 1, authData: str = "")
```

- Make offer for collection

#### cancelCollectionOffer()

```python
cancelCollectionOffer(offer_id: str = "", authData: str = "")
```

- Cancel collection offer with known offer_id

#### allCollectionOffers()

```python
allCollectionOffers(gift_name: str = "", authData: str = "") -> list
```

- Returns a list of dicts with all collection offers for the specified gift collection (`gift_name`)

#### topOffer()

```python
topOffer(gift_name: str = "", authData: str = "")
```

- Returns top offer for specified gift collection

#### myPlacedOffers()

```python
myPlacedOffers(offset: int = 0, limit: int = 20, authData: str = "")
```

- Returns a list of dicts with your placed offers

#### myReceivedOffers()

```python
myReceivedOffers(offset: int = 0, limit: int = 20, authData: str = "")
```

- Returns a list of dicts with offers you have received on your gifts

#### myCollectionOffers()

```python
myCollectionOffers(authData: str = "")
```

- Returns a list of dicts with collection offers you have made

#### changePrice()

```python
changePrice(nft_id: str="", price: float=0, authData: str="") -> dict|None
```

- Change price of the listed nft

#### withdrawPortals()

```python
withdrawPortals(amount: float=0, wallet: str="", authData: str="") -> dict
```

- Withdraw from portals to your TON wallet
- Warning: pass `UQ` addresses at your own risk. May work only with `EQ` addresses.

---

## Info

currently working on detailed documentation

*my telegram: [t.me/perfectlystill](https://t.me/perfectlystill)*

*chat: [t.me/giftsdevschat](https://t.me/giftsdevschat)*

*donations:*

- ton: `UQC9f1mTuu2hKuBP_0soC5RVkq2mLp8uiPBBhn69vadU7h8W`

---

*made with ❤️ by bleach*
