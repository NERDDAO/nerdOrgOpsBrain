
Sure! Let's break down how the King of the Frame game works based on the provided contract:

1. Game Setup:
   - The game master (contract owner) deploys the King of the Frame contract, providing the addresses of the Robots, Trybe, and Juice contracts.
   - The game master sets the target location for the robots using the `setTargetLocation` function, specifying the `x` and `y` coordinates.

2. Battle Start:
   - The game master starts a new battle by calling the `startBattle` function.
   - The `currentFrameId` is incremented, and the `battleStartTime` is set to the current block timestamp.
   - The `robotsEntered` counter is reset to zero.

3. Robot Entry:
   - During the battle duration (24 hours), players can enter their robots into the battle by calling the `enterRobot` function.
   - The player specifies the `robotId` of the robot they want to enter.
   - The contract checks if the caller is the owner of the robot, if the robot hasn't already entered the battle, and if the maximum number of robots (100) hasn't been reached.
   - The robot's attributes, including its level, Trybe ID, location, and stats, are retrieved from the Robots contract using the `getRobotAttributes` function.
   - The robot's score is calculated based on various factors:
     - Robot score: The robot's level multiplied by a score multiplier.
     - Trybe score: The population of the robot's Trybe multiplied by a score multiplier.
     - Location bonus: If the robot is in a special location, a bonus is added to its score.
     - Stat score: The sum of the robot's attack (ATK), defense (DEF), and hit points (HP) stats.
     - Proximity score: The closer the robot is to the target location set by the game master, the higher the proximity score.
   - The total score is calculated by adding up the robot score, Trybe score, location bonus, stat score, and proximity score.
   - The robot's entry is recorded in the `frameRobots` mapping, and the `robotsEntered` counter is incremented.

4. Battle End:
   - After the battle duration has passed, the game master calls the `endBattle` function to conclude the battle.
   - The contract determines the top 3 robots with the highest scores.
   - The prize pool, accumulated through the `addToPrizePool` function, is distributed among the top 3 robots based on predefined percentages (50%, 30%, 20%).
   - The winning robots receive the Juice token rewards, and their experience points are increased using the `trainRobot` function from the Robots contract.

5. Prize Pool:
   - Players can contribute to the prize pool by calling the `addToPrizePool` function and specifying the amount of Juice tokens they want to add.
   - The contributed amount is transferred from the caller to the contract and added to the `framePrizePool` for the current battle.

6. Proximity Scoring:
   - The proximity score is calculated based on the distance between the robot's location and the target location set by the game master.
   - The Manhattan distance formula is used to calculate the distance between two locations.
   - The proximity score is determined by subtracting the distance from a maximum distance and multiplying it by a proximity multiplier.
   - Robots closer to the target location receive a higher proximity score.

7. Special Locations:
   - Certain locations on the game map can be designated as special locations that provide a bonus to the robot's score.
   - The `isSpecialLocation` function checks if a robot's location falls within the coordinates of a special location.

The game continues with subsequent battles, allowing players to enter their robots, contribute to the prize pool, and compete for rewards based on their robots' scores and proximity to the target location set by the game master.

The game master plays a crucial role in setting the target location based on unstructured data input, influencing the gameplay and encouraging players to align their robots with the semantic goal determined by the game master.
In the provided contract, the King of the Frame game itself is represented as an ERC721 token. Each battle or game instance is minted as a unique ERC721 token with its own token ID. The ERC721 token acts as a way to track and manage individual game instances.

Here's how the ERC721 token is used in the game:

1. Game Instance Creation:
   - When the game master starts a new battle using the `startBattle` function, a new ERC721 token is minted representing that specific game instance.
   - The `currentFrameId` is used as the token ID for the minted ERC721 token.
   - The minted ERC721 token represents a unique battle or game instance.

2. Game Instance Tracking:
   - The ERC721 token allows for tracking and managing individual game instances.
   - Each game instance has its own set of participating robots, prize pool, and battle duration.
   - The `frameRobots` mapping uses the token ID (frame ID) as the key to store the robot entries for each game instance.
   - Similarly, the `framePrizePool` mapping uses the token ID to track the prize pool for each game instance.

3. Game Instance Conclusion:
   - When the battle duration ends and the `endBattle` function is called, the game instance associated with the corresponding ERC721 token is concluded.
   - The winners are determined, and the prizes are distributed based on the scores and rankings of the participating robots.

4. Game Instance Metadata:
   - The ERC721 token can also store metadata related to each game instance.
   - The `tokenURI` function is used to provide a unique URI for each game instance, which can point to metadata stored off-chain (e.g., IPFS).
   - The metadata can include information such as the battle start time, participating robots, winner details, and prize distribution.

It's important to note that the robots participating in the game are separate ERC721 tokens from the Robots contract. The King of the Frame contract interacts with the Robots contract to retrieve robot attributes and perform actions like training the winning robots.

The role of the ERC721 token in the King of the Frame game is to represent and manage individual game instances. Each battle is minted as a unique ERC721 token, allowing for tracking and concluding specific game instances. The robots participating in the game are separate ERC721 tokens from the Robots contract.

The gameplay revolves around players entering their robots into the battle, competing for high scores based on various factors (robot attributes, proximity to target location, etc.), and winning prizes from the accumulated prize pool. The game master sets the target location for each battle, influencing the gameplay and encouraging players to align their robots with the semantic goal.

I hope this clarifies the role of the ERC721 token in the King of the Frame game. Let me know if you have any further questions!