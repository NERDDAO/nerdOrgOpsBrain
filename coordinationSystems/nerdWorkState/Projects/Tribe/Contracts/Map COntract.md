To implement a map structure in your contract, you can use a 2D array (matrix) of integers to represent the maze-like structure. Each element in the matrix can represent a specific type of terrain or object on the map. The size of the map that you can store depends on the gas limitations and storage costs of the Ethereum network.

Regarding where to store the map data, it depends on how the map is used within your game. If the map is specific to each quest and varies between quests, it makes sense to store it in the quest contract. If the map represents a global structure that is shared across all quests or trybes, you can consider storing it in a separate contract that can be referenced by other contracts.

Here's a reference implementation of a map structure using a 2D array of integers:

```solidity
pragma solidity ^0.8.0;

contract Map {
    uint256 public constant MAP_SIZE = 100; // Adjust the size as needed
    uint256[][] public map;

    constructor() {
        initializeMap();
    }

    function initializeMap() internal {
        map = new uint256[][](MAP_SIZE);
        for (uint256 i = 0; i < MAP_SIZE; i++) {
            map[i] = new uint256[](MAP_SIZE);
            for (uint256 j = 0; j < MAP_SIZE; j++) {
                map[i][j] = 0; // Initialize all cells to 0 (empty)
            }
        }
    }

    function setCell(uint256 x, uint256 y, uint256 value) external {
        require(x < MAP_SIZE && y < MAP_SIZE, "Invalid coordinates");
        map[x][y] = value;
    }

    function getCell(uint256 x, uint256 y) external view returns (uint256) {
        require(x < MAP_SIZE && y < MAP_SIZE, "Invalid coordinates");
        return map[x][y];
    }
}
```

In this implementation:

- The `map` is defined as a 2D array of `uint256` values.
- The `MAP_SIZE` constant determines the size of the map. You can adjust it according to your requirements.
- The `initializeMap` function is called in the constructor to initialize the map with default values (0 for empty cells).
- The `setCell` function allows you to set the value of a specific cell in the map by providing the x and y coordinates and the value.
- The `getCell` function allows you to retrieve the value of a specific cell in the map by providing the x and y coordinates.

The size of the map that you can store depends on the gas limitations and storage costs. In the example above, a map size of 100x100 is used, which means the map can hold 10,000 cells. However, keep in mind that storing and manipulating large maps on the blockchain can be expensive in terms of gas costs.

If you need to store larger maps or more complex structures, you can consider using off-chain storage solutions like IPFS (InterPlanetary File System) to store the map data and only store references or hashes of the map data on the blockchain.

As for where to integrate this map structure, it depends on your game design:

- If the map is quest-specific and each quest has its own unique map, you can integrate the map structure into the quest contract.
- If the map is shared across all quests or trybes, you can create a separate map contract that can be referenced by other contracts.
- If the map is global and affects the entire game world, you can consider having a dedicated map contract that is independent of quests and trybes.

The choice depends on how the map fits into your overall game architecture and how it interacts with other components of your game.