Sure! Here's the TypeScript version of the CryptoClock package:

```typescript
import axios from 'axios';
import moment from 'moment-timezone';

class CryptoClock {
  private ethBlockTime: number;

  constructor() {
    this.ethBlockTime = 13.5; // Average Ethereum block time in seconds
  }

  async getEthBlockNumber(): Promise<number> {
    try {
      const response = await axios.get('https://api.etherscan.io/api?module=proxy&action=eth_blockNumber');
      return parseInt(response.data.result, 16);
    } catch (error) {
      throw new Error('Failed to fetch Ethereum block number');
    }
  }

  async convertTimeToEthBlock(time: string, timeZone: string): Promise<number> {
    const currentTime = moment().tz(timeZone);
    const targetTime = moment.tz(time, timeZone);
    const timeDiff = targetTime.diff(currentTime, 'seconds');
    const blockDiff = Math.floor(timeDiff / this.ethBlockTime);
    const currentBlock = await this.getEthBlockNumber();
    const targetBlock = currentBlock + blockDiff;
    return targetBlock;
  }

  convertEthBlocksToTime(ethBlocks: number, timeZone: string): string {
    const ethSeconds = ethBlocks * this.ethBlockTime;
    const currentTime = moment().tz(timeZone);
    const targetTime = currentTime.add(ethSeconds, 'seconds');
    return targetTime.format();
  }
}

export default CryptoClock;
```

Usage example:

```typescript
import CryptoClock from './CryptoClock';

async function exampleUsage() {
  const clock = new CryptoClock();

  // Convert time to Ethereum block
  const time = '2023-03-23 15:30:00';
  const timeZone = 'America/New_York';
  const ethBlock = await clock.convertTimeToEthBlock(time, timeZone);
  console.log(`Time ${time} (${timeZone}) corresponds to Ethereum block ${ethBlock}`);

  // Convert Ethereum blocks to time
  const ethBlocks = 100;
  const targetTime = clock.convertEthBlocksToTime(ethBlocks, timeZone);
  console.log(`${ethBlocks} Ethereum blocks from now is ${targetTime} (${timeZone})`);
}

exampleUsage();
```

In this TypeScript version:

- The `CryptoClock` class is defined with type annotations for the class properties and method parameters.
- The `ethBlockTime` property is marked as `private` to restrict direct access from outside the class.
- The `getEthBlockNumber` method returns a `Promise<number>` indicating that it is an asynchronous method that resolves to a number.
- The `convertTimeToEthBlock` method takes `time` and `timeZone` parameters of type `string` and returns a `Promise<number>` indicating that it is an asynchronous method that resolves to a number.
- The `convertEthBlocksToTime` method takes `ethBlocks` parameter of type `number` and `timeZone` parameter of type `string` and returns a `string`.
- The package uses the `axios` library for making HTTP requests and the `moment-timezone` library for handling time and time zones.

Make sure to install the required dependencies (`axios` and `moment-timezone`) and have TypeScript set up in your project before running the package.