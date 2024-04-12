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