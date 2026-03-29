# NFT/Collectibles dApp Template

## Standard Structure
```
my-nft-dapp/
├── contracts/
│   ├── Collection.sol            # ERC-721 collection with metadata
│   ├── Marketplace.sol           # List, buy, offer (or use GOB)
│   ├── MintController.sol        # Mint phases, allowlist, pricing
│   └── interfaces/
│       └── ICollection.sol
├── frontend/
│   ├── app/
│   │   ├── layout.tsx
│   │   ├── page.tsx              # Collection gallery
│   │   ├── mint/page.tsx         # Mint page
│   │   ├── [tokenId]/page.tsx    # NFT detail page
│   │   └── my-nfts/page.tsx      # User's collection
│   ├── components/
│   │   ├── nft/
│   │   │   ├── NFTCard.tsx
│   │   │   ├── MintButton.tsx
│   │   │   ├── NFTGrid.tsx
│   │   │   ├── TraitFilter.tsx
│   │   │   └── NFTDetail.tsx
│   │   └── shared/
│   │       ├── ConnectButton.tsx
│   │       └── XPNotification.tsx
│   ├── hooks/
│   │   ├── useCollection.ts
│   │   ├── useMint.ts
│   │   └── useNFTMetadata.ts
│   └── lib/
│       └── contracts.ts
├── metadata/
│   ├── generator.ts              # Metadata generation script
│   └── traits.json               # Trait definitions
├── test/
│   ├── Collection.test.ts
│   └── Marketplace.test.ts
└── docs/
```

## NFT Patterns on Integra

### Collection with Asset Passport Bridge
NFTs can be linked to Asset Passports, creating a bridge between digital collectibles and tokenized RWAs:
```solidity
contract IntegraCollection is ERC721, Ownable, IXPSystem {
    uint256 private _nextTokenId;

    struct NFTMetadata {
        string name;
        string description;
        string imageURI;
        uint256 passportId;  // 0 if no passport link
        mapping(string => string) traits;
    }

    mapping(uint256 => NFTMetadata) private _metadata;

    function mint(string memory name, string memory imageURI) external returns (uint256) {
        uint256 tokenId = _nextTokenId++;
        _safeMint(msg.sender, tokenId);
        _metadata[tokenId].name = name;
        _metadata[tokenId].imageURI = imageURI;
        emit XPAction(msg.sender, "nft_mint", 100);
        return tokenId;
    }

    function linkPassport(uint256 tokenId, uint256 passportId) external {
        require(ownerOf(tokenId) == msg.sender, "Not owner");
        _metadata[tokenId].passportId = passportId;
        emit XPAction(msg.sender, "nft_link_passport", 150);
    }
}
```

### Marketplace Options
1. **Custom marketplace** — build your own listing/offer system
2. **GOB integration** — list NFTs on Global Order Book for ecosystem-wide discovery
3. **Hybrid** — custom UI that reads/writes to GOB contracts

### Dynamic NFTs
NFTs whose metadata changes based on on-chain events:
- XP level displayed on NFT
- Agent performance badges
- Trading history stats
- Portfolio value indicators

## XP Event Map
| Action | Points |
|--------|--------|
| Mint NFT | 100 |
| List on marketplace | 75 |
| Purchase NFT | 150 |
| Link to Asset Passport | 150 |
| First collection interaction | 100 |
