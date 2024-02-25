// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import "@openzeppelin/contracts/token/ERC721/ERC721.sol";
import "@openzeppelin/contracts/access/Ownable.sol";

contract NFTMarketplace is ERC721, Ownable {
    using SafeMath for uint256;

    uint256 public nextTokenId;
    mapping(uint256 => uint256) public tokenPrice;

    constructor() ERC721("NFTMarketplace", "NFTM") {}

    function mint() external onlyOwner {
        _safeMint(msg.sender, nextTokenId);
        nextTokenId++;
    }

    function setTokenPrice(uint256 tokenId, uint256 price) external onlyOwner {
        require(_exists(tokenId), "Token does not exist");
        tokenPrice[tokenId] = price;
    }

    function buyToken(uint256 tokenId) external payable {
        require(_exists(tokenId), "Token does not exist");
        require(msg.value >= tokenPrice[tokenId], "Insufficient funds");

        address owner = ownerOf(tokenId);
        payable(owner).transfer(msg.value);
        _transfer(owner, msg.sender, tokenId);
    }
}
