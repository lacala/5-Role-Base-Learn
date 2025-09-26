// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.13;

import "@openzeppelin/contracts/token/ERC721/ERC721.sol";

contract HaikuNFT is ERC721 {
    error HaikuNotUnique();
    error NoHaikusShared();
    error NotHaikuOwner();

    struct Haiku {
        address author;
        string line1;
        string line2;
        string line3;
    }

    mapping(bytes32 => bool) public haikuLinesUsed;
    Haiku[] public haikus;
    mapping(address => uint256[]) public sharedHaikus;
    uint256 public counter;

    constructor(
        string memory _name,
        string memory _symbol
    ) ERC721(_name, _symbol) {
        counter = 1; // Start counter at 1
    }

    function mintHaiku(
        string calldata _line1,
        string calldata _line2,
        string calldata _line3
    ) external {
        bytes32 line1hash = keccak256(abi.encodePacked(_line1));
        bytes32 line2hash = keccak256(abi.encodePacked(_line2));
        bytes32 line3hash = keccak256(abi.encodePacked(_line3));

        // Check for uniqueness
        if (
            haikuLinesUsed[line1hash] ||
            haikuLinesUsed[line2hash] ||
            haikuLinesUsed[line3hash]
        ) {
            revert HaikuNotUnique();
        }

        // Mark the lines as used
        haikuLinesUsed[line1hash] = true;
        haikuLinesUsed[line2hash] = true;
        haikuLinesUsed[line3hash] = true;

        // Create and store the haiku
        Haiku memory newHaiku = Haiku({
            author: msg.sender,
            line1: _line1,
            line2: _line2,
            line3: _line3
        });
        haikus.push(newHaiku);

        // Mint the NFT and assign it to the sender
        _mint(msg.sender, counter);

        // Increment the counter for the next token
        counter++;
    }

    function shareHaiku(uint256 _tokenId, address _to) external {
        // Ensure the caller is the owner of the Haiku NFT
        if (ownerOf(_tokenId) != msg.sender) {
            revert NotHaikuOwner();
        }

        // Add the haiku to the recipient's list of shared haikus
        sharedHaikus[_to].push(_tokenId);
    }

    function getMySharedHaikus() external view returns (Haiku[] memory) {
        uint256[] memory haikuIds = sharedHaikus[msg.sender];
        uint256 totalShared = haikuIds.length;

        // Revert if no haikus have been shared with the caller
        if (totalShared == 0) {
            revert NoHaikusShared();
        }

        // Create an array of haikus to return
        Haiku[] memory haikusToReturn = new Haiku[](totalShared);
        for (uint256 i = 0; i < totalShared; i++) {
            haikusToReturn[i] = haikus[haikuIds[i] - 1]; // Adjust index for 0-based array
        }

        return haikusToReturn;
    }
}
