
```
// SPDX-License-Identifier: MIT
// Compatible with OpenZeppelin Contracts ^5.0.0
pragma solidity ^0.8.20;

import "@openzeppelin/contracts/token/ERC1155/ERC1155.sol";
import "@openzeppelin/contracts/access/Ownable.sol";
import "@openzeppelin/contracts/token/ERC1155/extensions/ERC1155Burnable.sol";
import "@openzeppelin/contracts/token/ERC1155/extensions/ERC1155Supply.sol";
import "@openzeppelin/contracts/token/ERC1155/extensions/ERC1155URIStorage.sol";
import "@openzeppelin/contracts/utils/Strings.sol";

import "@solady/src/utils/LibString.sol";

contract Trybe is
    ERC1155,
    Ownable,
    ERC1155Burnable,
    ERC1155Supply,
    ERC1155URIStorage
{
    using LibString for uint256;
    event NewTrybe(uint256 indexed tokenId, address indexed to, string tribe);

    enum Options {
        LOW,
        MEDIUM,
        HIGH
    }

    struct Stats {
        uint pop_mod;
        string resources;
        string technology;
    }

    struct Trybes {
        string name;
        uint256 bgHue;
        uint256 textHue;
        Stats stats;
    }
    using Strings for uint256;

    mapping(uint256 => Trybes) private wordsToTokenId;
    uint private fee = 0.05 ether;

    constructor(
        address initialOwner
    ) ERC1155(unicode"Trybe 🔥") Ownable(initialOwner) {
        mint(unicode"🔥");
    }

    function setURI(string memory newuri) public onlyOwner {
        _setURI(newuri);
    }

    function randomHue(
        uint8 _salt,
        uint _spread
    ) private view returns (uint256) {
        return
            uint256(
                keccak256(
                    abi.encodePacked(
                        block.number,
                        false,
                        totalSupply(),
                        false,
                        _salt
                    )
                )
            ) % _spread;
    }

    function changeTribes(uint256 _tokenId, uint256 newTribe) public {
        require(exists(newTribe), "Target Tribe not found");
        require(balanceOf(msg.sender, _tokenId) >= 1);
        _burn(msg.sender, _tokenId, 1);
        _mint(msg.sender, newTribe, 1, "");
    }

    function mint(string memory tribe) public payable {
        require(bytes(tribe).length <= 30, "Tribe is too long");

        if (msg.sender != owner()) {
            require(
                msg.value >= fee,
                string(
                    abi.encodePacked("Missing fee of ", fee.toString(), " wei")
                )
            );
        }

        uint256 newSupply = totalSupply() + 1;
        _mint(msg.sender, newSupply, 1, "");
        emit NewTrybe(newSupply, msg.sender, tribe);

        Stats memory newStats = Stats(
            randomHue(1, 42),
            returnState(randomHue(2, 3)),
            returnState(randomHue(3, 3))
        );

        Trybes memory newTrybe = Trybes(
            tribe,
            randomHue(1, newSupply),
            randomHue(2, newSupply),
            newStats
        );

        wordsToTokenId[newSupply] = newTrybe;
    }

    function tribeStats(
        uint256 _tokenId
    ) external view returns (string[4] memory) {
        require(
            exists(_tokenId),
            "ERC1155Metadata: URI query for nonexistent token"
        );

        string[4] memory _stats;
        Trybes memory tokenWord = wordsToTokenId[_tokenId];
        _stats[0] = tokenWord.name;
        _stats[1] = string(
            abi.encodePacked(
                "x: ",
                tokenWord.bgHue.toString(),
                " y: ",
                tokenWord.textHue.toString()
            )
        );
        _stats[2] = string(
            abi.encodePacked(tokenWord.stats.pop_mod * totalSupply(_tokenId))
        );
        _stats[3] = string(
            abi.encodePacked(
                "Trybe: ",
                tokenWord.name,
                " Resources: ",
                tokenWord.stats.resources,
                " Technology: ",
                tokenWord.stats.technology
            )
        );
        return _stats;
    }

    function isMember(
        address account,
        uint256 _tokenId
    ) external view returns (bool) {
        return balanceOf(account, _tokenId) > 0;
    }

    function returnState(uint _id) public pure returns (string memory) {
        if (Options(_id) == Options.LOW) return "Low";
        if (Options(_id) == Options.MEDIUM) return "Medium";
        if (Options(_id) == Options.HIGH) return "High";
        return "";
    }

    function mintNew(uint256 _tokenId) public payable {
        require(
            exists(_tokenId),
            "ERC1155Metadata: URI query for nonexistent token"
        );
        if (msg.sender != owner()) {
            require(
                msg.value >= fee,
                string(
                    abi.encodePacked("Missing fee of ", fee.toString(), " wei")
                )
            );
        }
        _mint(msg.sender, _tokenId, 1, "");
    }

    function uri(
        uint256 _tokenId
    )
        public
        view
        virtual
        override(ERC1155, ERC1155URIStorage)
        returns (string memory)
    {
        require(
            exists(_tokenId),
            "ERC1155Metadata: URI query for nonexistent token"
        );

        //Trybes memory tokenWord = wordsToTokenId[_tokenId];
        return string(bytes.concat());
    }

    function mintBatch(
        address to,
        uint256[] memory ids,
        uint256[] memory amounts,
        bytes memory data
    ) public onlyOwner {
        _mintBatch(to, ids, amounts, data);
    }

    function getFee() public view returns (uint) {
        return fee;
    }

    function setFee(uint _newFee) public onlyOwner {
        fee = _newFee;
    }

    function withdraw() public payable onlyOwner {
        (bool success, ) = payable(msg.sender).call{
            value: address(this).balance
        }("");
        require(success);
    }

    // The following functions are overrides required by Solidity.

    function _update(
        address from,
        address to,
        uint256[] memory ids,
        uint256[] memory values
    ) internal override(ERC1155, ERC1155Supply) {
        super._update(from, to, ids, values);
    }
}

```