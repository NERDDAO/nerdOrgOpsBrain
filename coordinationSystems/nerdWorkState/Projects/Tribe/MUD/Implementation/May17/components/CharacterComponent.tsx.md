```tsx
import React, { useState } from "react";
import { SystemCalls } from "./mud/mud/createSystemCalls";

interface Props {
  systemCalls: SystemCalls;
}

export const CharacterComponent: React.FC<Props> = ({ systemCalls }) => {
  const [characterId, setCharacterId] = useState("");
  const [characterName, setCharacterName] = useState("");
  const [characterDescription, setCharacterDescription] = useState("");

  const handleCreateCharacter = async () => {
    if (!characterId || !characterName || !characterDescription) {
      alert("All fields are required to create a character");
      return;
    }
    await systemCalls.createCharacter(characterId, characterName, characterDescription);
  };

  return (
    <div className="form-control">
      <h2>Create Character</h2>
      <input
        className="input"
        type="text"
        placeholder="Character ID"
        value={characterId}
        onChange={e => setCharacterId(e.target.value)}
      />
      <input
        className="input"
        type="text"
        placeholder="Character Name"
        value={characterName}
        onChange={e => setCharacterName(e.target.value)}
      />
      <input
        className="input"
        type="text"
        placeholder="Character Description"
        value={characterDescription}
        onChange={e => setCharacterDescription(e.target.value)}
      />
      <button className="btn" onClick={handleCreateCharacter}>
        Create Character
      </button>
    </div>
  );
};
```