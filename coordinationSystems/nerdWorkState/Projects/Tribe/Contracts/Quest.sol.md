```solidity
// SPDX-License-Identifier: MIT
pragma solidity 0.8.20;

import "@solady/src/tokens/ERC721.sol";
import "@solady/src/utils/LibString.sol";
import "@solady/src/auth/Ownable.sol";

interface IRobots {
    struct RobotAttributes {
        string name;
        uint256 trybeId;
        uint256 exp;
        uint256 level;
        Location location;
        Stats stats;
        Equipment equipment;
    }

    struct Location {
        uint256 x;
        uint256 y;
        string pointOfInterest;
    }

    struct Stats {
        uint256 HP;
        uint256 ATK;
        uint256 DEF;
    }

    struct Equipment {
        string head;
        string body;
        string legs;
        string weapon;
    }

    function ownerOf(uint256 tokenId) external view returns (address);
    function getRobotAttributes(uint256 tokenId) external view returns (RobotAttributes memory);
    function trainRobot(uint256 tokenId, uint256 exp, uint256 level, uint256 hp, uint256 atk) external;
}

interface ITrybe {
    function isMember(address account, uint256 _tokenId) external view returns (bool);
    function tribeStats(uint256 _tokenId) external view returns (string[4] memory);
}

interface IJuice {
    function transfer(address recipient, uint256 amount) external returns (bool);
    function transferFrom(address sender, address recipient, uint256 amount) external returns (bool);
}

contract KingOfTheFrame is ERC721, Ownable {
    using LibString for uint256;

    IRobots public immutable robotsContract;
    ITrybe public immutable trybeContract;
    IJuice public immutable juiceContract;

    uint256 public constant MAX_ROBOTS = 100;
    uint256 public constant BATTLE_DURATION = 24 hours;
    uint256 public constant ROBOT_SCORE_MULTIPLIER = 10;
    uint256 public constant TRYBE_SCORE_MULTIPLIER = 5;
    uint256 public constant LOCATION_BONUS_MULTIPLIER = 2;
    uint256 public constant PROXIMITY_MULTIPLIER = 10;
    uint256 public constant MAX_DISTANCE = 1000;

    uint256 public currentFrameId;
    uint256 public battleStartTime;
    uint256 public robotsEntered;

    struct RobotEntry {
        uint256 robotId;
        uint256 trybeId;
        address owner;
        uint256 score;
    }

    mapping(uint256 => RobotEntry[]) public frameRobots;
    mapping(uint256 => mapping(uint256 => bool)) public robotEntered;
    mapping(uint256 => uint256) public framePrizePool;

    //Robots.Location public targetLocation;

    event BattleStarted(uint256 indexed frameId, uint256 startTime);
    event RobotEntered(
        uint256 indexed frameId,
        uint256 indexed robotId,
        address indexed owner
    );
    event BattleEnded(
        uint256 indexed frameId,
        uint256[] winningRobotIds,
        uint256[] winningTrybeIds,
        uint256 prizePool
    );
    event TargetLocationSet(uint256 x, uint256 y);

    constructor(
        address _robotsContract,
        address _trybeContract,
        address _juiceContract
    ) ERC721() {
        robotsContract = Robots(_robotsContract);
        trybeContract = Trybe(_trybeContract);
        juiceContract = Juice(_juiceContract);
    }

    function startBattle() external {
        require(
            block.timestamp >= battleStartTime + BATTLE_DURATION,
            "Previous battle still ongoing"
        );

        currentFrameId++;
        battleStartTime = block.timestamp;
        robotsEntered = 0;

        emit BattleStarted(currentFrameId, battleStartTime);
    }

    function setTargetLocation(uint256 x, uint256 y) external onlyOwner {
        targetLocation = Robots.Location(x, y, "");
        emit TargetLocationSet(x, y);
    }

    function enterRobot(uint256 robotId) external {
        require(
            block.timestamp < battleStartTime + BATTLE_DURATION,
            "Battle has ended"
        );
        require(
            robotsContract.ownerOf(robotId) == msg.sender,
            "Not the owner of the robot"
        );
        require(
            !robotEntered[currentFrameId][robotId],
            "Robot already entered"
        );
        require(robotsEntered < MAX_ROBOTS, "Maximum robots reached");

        Robots.RobotAttributes memory attributes = robotsContract
            .getRobotAttributes(robotId);
        uint256 trybeId = attributes.trybeId;
        uint256 robotScore = attributes.level * ROBOT_SCORE_MULTIPLIER;
        uint256 trybeScore = trybeContract.tribeStats(trybeId).population *
            TRYBE_SCORE_MULTIPLIER;

        // Calculate location-based bonus
        uint256 locationBonus = 0;
        if (isSpecialLocation(attributes.location)) {
            locationBonus =
                (robotScore + trybeScore) *
                LOCATION_BONUS_MULTIPLIER;
        }

        // Calculate stat-based score
        uint256 statScore = attributes.stats.ATK +
            attributes.stats.DEF +
            attributes.stats.HP;

        // Calculate proximity score based on distance to target location
        uint256 proximityScore = calculateProximityScore(attributes.location);

        uint256 totalScore = robotScore +
            trybeScore +
            locationBonus +
            statScore +
            proximityScore;

        frameRobots[currentFrameId].push(
            RobotEntry(robotId, trybeId, msg.sender, totalScore)
        );
        robotEntered[currentFrameId][robotId] = true;
        robotsEntered++;

        emit RobotEntered(currentFrameId, robotId, msg.sender);
    }

    function endBattle() external {
        require(
            block.timestamp >= battleStartTime + BATTLE_DURATION,
            "Battle still ongoing"
        );

        RobotEntry[] memory robots = frameRobots[currentFrameId];
        uint256[] memory winningRobotIds = new uint256[](3);
        uint256[] memory winningTrybeIds = new uint256[](3);

        for (uint256 i = 0; i < robots.length; i++) {
            for (uint256 j = 0; j < 3; j++) {
                if (robots[i].score > robots[winningRobotIds[j]].score) {
                    for (uint256 k = 2; k > j; k--) {
                        winningRobotIds[k] = winningRobotIds[k - 1];
                        winningTrybeIds[k] = winningTrybeIds[k - 1];
                    }
                    winningRobotIds[j] = robots[i].robotId;
                    winningTrybeIds[j] = robots[i].trybeId;
                    break;
                }
            }
        }

        uint256 prizePool = framePrizePool[currentFrameId];
        uint256[] memory prizes = new uint256[](3);
        prizes[0] = (prizePool * 50) / 100;
        prizes[1] = (prizePool * 30) / 100;
        prizes[2] = (prizePool * 20) / 100;

        for (uint256 i = 0; i < 3; i++) {
            if (winningRobotIds[i] != 0) {
                juiceContract.transfer(
                    robotsContract.ownerOf(winningRobotIds[i]),
                    prizes[i]
                );
                robotsContract.trainRobot(winningRobotIds[i], 100, 0, 0, 0);
            }
        }

        emit BattleEnded(
            currentFrameId,
            winningRobotIds,
            winningTrybeIds,
            prizePool
        );
    }

    function addToPrizePool(uint256 amount) external {
        juiceContract.transferFrom(msg.sender, address(this), amount);
        framePrizePool[currentFrameId] += amount;
    }

    function calculateProximityScore(
        Robots.Location memory robotLocation
    ) internal view returns (uint256) {
        uint256 distance = calculateDistance(robotLocation, targetLocation);
        uint256 proximityScore = (MAX_DISTANCE - distance) *
            PROXIMITY_MULTIPLIER;
        return proximityScore;
    }

    function calculateDistance(
        Robots.Location memory location1,
        Robots.Location memory location2
    ) internal pure returns (uint256) {
        uint256 dx = location1.x > location2.x
            ? location1.x - location2.x
            : location2.x - location1.x;
        uint256 dy = location1.y > location2.y
            ? location1.y - location2.y
            : location2.y - location1.y;
        return dx + dy;
    }

    function isSpecialLocation(
        Robots.Location memory location
    ) internal pure returns (bool) {
        // Check if the robot's location is a special location that provides a bonus
        // Implement your own logic here based on the location attributes
        // For example:
        if (
            location.x >= 100 &&
            location.x <= 200 &&
            location.y >= 100 &&
            location.y <= 200
        ) {
            return true;
        }
        return false;
    }

    function name() public pure override returns (string memory) {
        return "King of the Frame";
    }

    function symbol() public pure override returns (string memory) {
        return "KOTF";
    }

    function tokenURI(
        uint256 tokenId
    ) public view override returns (string memory) {
        if (!_exists(tokenId)) revert TokenDoesNotExist();
        return
            "ipfs://bafybeidq6hpvm5ph6qycbzeerqtzwd3dogdp5hlmxt5cefbypa7i4esaae/";
    }
}

```

