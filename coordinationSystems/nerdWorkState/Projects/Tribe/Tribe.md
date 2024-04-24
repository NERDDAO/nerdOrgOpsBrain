![[Pasted image 20240411222503.png]]

---
## Tribe

Goal: To develop a text-based Survival MMORPG that combines traditional gameplay elements of survival and role-playing genres with innovative AI-driven narrative techniques. The game will focus on player survival in a harsh environment, requiring them to manage resources, interact with other players, and adapt to changing conditions.

---

Tech Stack: #llamaIndex #scaffoldEth #farcaster #frames 

Team Members: #At0x #rraigal #jbate

---

Summary:

**"Tribe by Nerds"** aims to redefine the survival MMORPG genre through a minimalist, text-based approach. The game's core mechanic revolves around the use of AI to dynamically generate narrative content in the form of haikus. 

---

These haikus serve not only as a unique storytelling medium but also as a method for maintaining game coherence and updating the game state. This innovative approach allows for a deeply immersive experience, as players must interpret and react to the poetic cues provided by the AI to survive and progress in the game.

---

1. **Tribe as Main Character**:
    
    - **Tribe Attributes**: Each tribe has its own set of attributes, such as culture, technology level, resources, and population. These attributes affect the scenarios (haikus) generated and the options available for response.
    - **Progression**: The tribe progresses by making decisions in response to scenarios. Success can lead to growth in resources, technology, and population, while failure might result in losses.
2. **Individual Scenarios (Haikus)**:
    
    - **Scenario Generation**: Scenarios are generated based on the tribe's current state, location in the game world, and random events. Each scenario is presented as a haiku that hints at the situation and possible outcomes.
    - **Decision Making**: Players respond to scenarios by choosing from a set of actions. These actions are influenced by the tribe's attributes and the artifacts they possess.
3. **Artifacts**:
    
    - **Discovery**: Artifacts are discovered through exploration and successful navigation of scenarios. They can also be traded with other players.
    - **Usage**: Artifacts provide benefits such as boosting tribe attributes, unlocking new actions, or affecting scenario outcomes.

1. **Echo-Driven Narrative**:
    
    - Every action in the game leaves an echo, influencing not just the immediate outcome but also future scenarios and interactions. These echoes are woven into the AI-generated haikus, providing hints of past actions and foreshadowing future consequences.
    - The narrative evolves dynamically, reflecting the collective impact of player actions across different dimensions of the game world.
2. **Tribe Survival and Progression**:
    
    - Players manage their tribes, balancing resources, technological advancement, and population growth. Echoes can affect these aspects, offering new challenges or opportunities based on past decisions.
    - Scenarios generated through AI-driven haikus challenge players to make strategic decisions, with the tribe's attributes and the echoes of previous actions influencing the available choices.
3. **Dimensional Layers and Exploration**:
    
    - The game world is structured in layers, each representing different dimensions affected by echoes. Players explore these layers, encountering unique environments, challenges, and artifacts.
    - Artifacts discovered can harness or manipulate echoes, granting tribes advantages or altering the course of events across dimensions.
4. **Multiplayer Dynamics and Echo Guilds**:
    
    - Players can form Echo Guilds, collaborating to shape the narrative and environment through coordinated actions. Guilds can compete or cooperate to resolve anomalies and quests that span across dimensions.
    - Anomaly events and ripple quests are influenced by the collective echoes of player actions, requiring guilds to strategize and leverage their understanding of echoes for success.

### Smart Contract Architecture Refactor:

#### Trybe Contract (ERC1155):

- **Purpose**: Represents tribes and their attributes. Each tribe is a unique token with attributes that can evolve over time.
- **Changes**:
    - **Attribute Evolution**: Include functions to update tribe attributes based on gameplay decisions.
    - **Scenario Interaction**: Integrate a mechanism to receive and respond to scenarios, possibly through external calls to a scenario manager contract.

#### Artifact Contract (ERC721):

- **Purpose**: Represents artifacts discovered during gameplay. Each artifact is a unique token with specific benefits.
- **Features**:
    - **Discovery and Minting**: Functionality for minting new artifacts upon discovery in the game.
    - **Artifact Attributes**: Each artifact has attributes detailing its benefits and possible usage conditions.
    - **Ownership and Trade**: Players can own, trade, and utilize artifacts to influence their tribe's progression.

### Integration:

- **Scenario Management**: A separate module or contract that generates scenarios based on the current state of the tribe and available artifacts. It interacts with both the Trybe and Artifact contracts to check conditions and apply outcomes.
- **Gameplay Loop**:
    1. The game generates a scenario based on the tribe's state and sends it to the player.
    2. The player chooses an action in response to the scenario.
    3. The chosen action, along with any relevant artifact benefits, is processed to determine the outcome.
    4. The Trybe and Artifact contracts are updated based on the outcome.

### Example Scenario Flow:

1. **Scenario Generation**: The game generates a scenario where the tribe encounters a rival tribe.
2. **Player Decision**: The player decides to negotiate peace, using an artifact that boosts diplomacy.
3. **Outcome Processing**: The game processes the decision, applying the artifact's benefits, and determines a successful negotiation.
4. **State Update**: The Trybe contract updates the tribe's attributes to reflect the new alliance, and the Artifact contract records the usage of the artifact.

### Ontology Definition:

1. **Player Attributes:**
   - **Location**: The current position of the player within the game world.
   - **Inventory**: A list of up to 4 items that the player currently holds.
   - **Health**: A numerical or qualitative measure of the player's current health status.
   - **Status**: Additional conditions affecting the player (e.g., poisoned, cursed).
---

2. **Output:**
   - **Haiku**: A three-line poem with a 5-7-5 syllable structure that describes the player's current situation, incorporating elements from the player attributes.
   - **Actions**: Four potential actions the player can take, expressed as single verbs (e.g., "run", "fight", "heal", "search").

---
```typescript
type Haikipu = {
Location: {x:number, y:number, name:string};
Inventory: string[];
Health:number;
Status:string;
Haiku: string;
Actions:string[];
}

type Player = {
playerId: string;
playerName:string;
isAlive:bool;
Logs: Haikipu[];
}

```

---
### Example Ontology Implementation:

- **Player Attributes:**
  - **Location**: "Ancient Forest"
  - **Inventory**: ["Sword", "Shield", "Potion", "Map"]
  - **Health**: "Wounded"
  - **Status**: "Lost"
---

- **Output:**
  - **Haiku**:
    ```
    In the forest deep,
    Wounded, lost, with sword in hand,
    Hope flickers like flame.
    ```
  - **Actions**: ["Run", "Fight", "Heal", "Search"]

This ontology provides a structured way to represent the game's data concerning the player and outlines how the LLM should process this information to generate a creative and contextually relevant output.


## Trybe Contract TODO:

-> Map randomness to variables
-> create environment enum
-> Fix text display

-> Mint trybe frame
-> Integrate trybe with haiku routes
-> req trybe for haiku mint
-> req haiku for 

### Contract Work To-Do List:

1. **Smart Contract Refinement:**
    
    - Finalize and test the `Trybe` and `Artifact` contracts, ensuring all functions are optimized and secure.
    - Implement the `Scenario Management` module or contract that will generate scenarios based on the current state of the tribe and available artifacts.
2. **Attribute Evolution in Trybe Contract:**
    
    - Develop and integrate functions to update tribe attributes (e.g., population, resources, technology) based on gameplay decisions within the `Trybe` contract.
3. **Scenario Interaction Mechanism:**
    
    - Create a mechanism in the `Trybe` contract for receiving and responding to scenarios, possibly through external calls to a scenario manager contract.
4. **Artifact Discovery and Minting:**
    
    - Ensure the `Artifact` contract has functionality for minting new artifacts upon discovery in the game, including setting artifact attributes and usage conditions.
5. **Ownership and Trade Functionality:**
    
    - Implement and test ownership and trade functionalities in the `Artifact` contract, allowing players to own, trade, and utilize artifacts.
6. **Integration Testing:**
    
    - Conduct thorough integration testing between the `Trybe`, `Artifact`, and `Scenario Management` modules or contracts to ensure seamless interaction and data flow.

### Updated Ontologies:

1. **Expand Player Attributes:**
    
    - Consider adding more attributes to players, such as "Experience" or "Skills", to enrich gameplay and scenario outcomes.
2. **Enhance Scenario Outputs:**
    
    - Develop a more complex structure for scenario outputs, including potential consequences or rewards for each action, to deepen the strategic aspect of the game.
3. **Artifact Attributes and Effects:**
    
    - Define a clear ontology for artifact attributes, including their effects on scenarios, tribe attributes, and player decisions.

### Integration Between Contracts and Gameplay:

1. **Scenario Generation and Processing:**
    
    - Ensure the `Scenario Management` module or contract can access necessary data from both `Trybe` and `Artifact` contracts to generate relevant scenarios.
    - Implement a feedback loop where the outcomes of player decisions in scenarios are processed and reflected in the `Trybe` and `Artifact` contracts.
2. **Dynamic Content Creation:**
    
    - Integrate AI-driven narrative techniques to dynamically generate content, ensuring the haikus and scenarios remain coherent and engaging based on the evolving game state.
3. **Gameplay Loop Optimization:**
    
    - Refine the gameplay loop to ensure a smooth and intuitive experience for players, from scenario generation to action selection and outcome processing.
4. **UI/UX for Contract Interactions:**
    
    - Develop a user-friendly interface for players to interact with the smart contracts, especially for artifact management and scenario responses.
5. **Documentation and Examples:**
    
    - Create comprehensive documentation and examples for all contract functions and interactions, aiding both developers and players in understanding the game mechanics.
      
      
