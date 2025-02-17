# Ponder

Ponder is an open-source framework for blockchain application backends.
## Quickstart

### Server
1. Run `bun i`

```bash
npm install 
# or
pnpm install
# or
yarn install
# or 
bun install
```
2. Add `.env` file

```bash
cp .env.example. .env
```

3. Start the development server

```bash
npm run dev
# or
pnpm dev
# or
yarn dev
# or 
bun dev
```
4. Query the GraphQL API

Ponder automatically generates a frontend-ready GraphQL API based on your `ponder.schema.ts` file. The API serves data that you inserted in your indexing functions.

the server will run in `http://localhost:42069/`

If you want to query like the picture. You first need to know the `id` of this `depositEvent` you want to query. 
![ponder graph](https://github.com/whereissam/ponder-example/blob/main/ponder-graphql.jpeg)

You can get the `id` from log the event ID like below
```typescript
ponder.on("weth9:Deposit", async ({ event, context }) => {
  console.log(event.log.id);
  await context.db.insert(schema.depositEvent).values({
    id: event.log.id,
    account: event.args.dst,
    amount: event.args.wad,
    timestamp: Number(event.block.timestamp),
  });
});
```

It will look like this
```shell
Synced 5 logs and 5 transactions from 'base' block 26246920
0xafbbf0a6360725a42466acaf79618c6ddd999f968186bbdefc2c17ddb221ee89-0x6a
0xafbbf0a6360725a42466acaf79618c6ddd999f968186bbdefc2c17ddb221ee89-0x80
0xafbbf0a6360725a42466acaf79618c6ddd999f968186bbdefc2c17ddb221ee89-0x88
0xafbbf0a6360725a42466acaf79618c6ddd999f968186bbdefc2c17ddb221ee89-0x8d
0xafbbf0a6360725a42466acaf79618c6ddd999f968186bbdefc2c17ddb221ee89-0xa7
```

and paste it to GraphQL and you can query it.


### Frontend
1. cd to frontend folder

```bash
cd frontend
```

2. Run `bun i`

```bash
npm install 
# or
pnpm install
# or
yarn install
# or 
bun install
```

3. Start the development frontend

```bash
npm run dev
# or
pnpm dev
# or
yarn dev
# or 
bun dev
```
4. Visit `localhost:3000` in your browser


![](https://github.com/whereissam/ponder-example/blob/main/frontend.jpeg)


That's it! Visit [ponder.sh](https://ponder.sh) for documentation, guides for deploying to production, and the API reference.


Ponder is MIT-licensed open-source software.

## Understand flow and parameter

### Flow

1. Data Indexing:
```typescript=
// In your schema file (e.g., schema.ts)
export const depositEvent = onchainTable("deposit_event", (t) => ({
  id: t.text().primaryKey(),
  timestamp: t.integer().notNull(),
  amount: t.bigint().notNull(),
  account: t.hex().notNull(),
}));

// In your index.ts or similar
export const config = {
  networks: [{
    name: 'mainnet',
    chainId: 1,
    // network configuration
  }],
  contracts: [{
    name: 'YourContract',
    address: '0x...',
    abi: [...], // Your contract ABI that includes a Deposit event
    startBlock: 1234567
  }]
}

// This is where you define how to store blockchain events
ponder.on('YourContract:Deposit', async ({ event, context }) => {
  await context.db.depositEvent.create({
    id: event.log.id,          // matches schema 'id'
    timestamp: event.block.timestamp,  // matches schema 'timestamp'
    amount: event.args.amount,  // matches schema 'amount'
    account: event.args.account // matches schema 'account'
  });
});
```
2. Automatic GraphQL Generation:

When you run ponder dev, it:
- Reads your schema
- Creates a SQLite database (by default)
- Indexes blockchain data based on your event handlers
- Generates GraphQL resolvers

3. Query Flow:

```graphql=
# When you make this query in the explorer
query {
  depositEvent(id: "0x791bc75380711...") {
    id
    amount
  }
}
```
Behind the scenes:
```typescript=
// Ponder automatically creates something like this
const resolver = {
  Query: {
    depositEvent: async (_, { id }) => {
      return await database.query(
        'SELECT * FROM deposit_event WHERE id = ?',
        [id]
      );
    }
  }
}
```

### Parameter

Before you write the schema, you need to understand the parameter of the event.

Go to explorer and click the event you want to query.

1. Find the explorer URL, I use [Basescan](https://basescan.org) as an example. Type the contract address into the search bar and click the contract. I query deposit function info
   - Click the event you want to query. You can see the tab, click the `Logs` tab. You will find out like `dst` or `wad` parameter and you will know what is that mean and paste back to the schema.          
2. You can just console log it. 
```typescript=
ponder.on("weth9:Deposit", async ({ event, context }) => {
  console.log('Deposit', event);
  await context.db.insert(schema.depositEvent).values({
    id: event.log.id,
    account: event.args.dst,
    amount: event.args.wad,
    timestamp: Number(event.block.timestamp),
  });
});

```
```
Deposit {
  name: 'Deposit',
  args: {
    dst: '0x4752ba5DBc23f44D87826276BF6Fd6b1C372aD24',
    wad: 48914890000000000n
  },
  log: {
    id: '0x0be497a6014e0c9b0ba115c0a269ba83fcd61baf5b1847a63af89937a37360b7-0xed',
    address: '0x4200000000000000000000000000000000000006',
    data: '0x00000000000000000000000000000000000000000000000000adc7d552ad6400',
    logIndex: 237,
    removed: false,
    topics: [
      '0xe1fffcc4923d04b559f4d29a8bfc6cda04eb5b0d3c460751c2402c5c5cc9109c',
      '0x0000000000000000000000004752ba5dbc23f44d87826276bf6fd6b1c372ad24'
    ]
  },
  block: {
    baseFeePerGas: 2390350n,
    difficulty: 0n,
    extraData: '0x00000000fa00000002',
    gasLimit: 100000000n,
    gasUsed: 35627690n,
    hash: '0x0be497a6014e0c9b0ba115c0a269ba83fcd61baf5b1847a63af89937a37360b7',
    logsBloom: '0x1261a4040a9928908180d028fb468a62bc9012308004a71882e7968b481170525d9b5004942586645742a851472734604b71881008486e5627636ac0c6bd25808ee09a18804d06d8e347d418c0106067048518308a68659148918c4499f8296134ac302c9b4f0e62a56f9e24980a098d110294432e5c3f30c086aa54149a96c50302036089a6b00ad050feba8146064ba49396cd6c301a89740e605a1418761a0b92780391908d21cf5853572b0100196c13010035ea200e0487673185861980af14f09a6477e49546818cf68d9211c28884c84ad40c1e7862c502e3882ae127147e0e8d01adb1437090a086b1d41da1d2821760cdb8866023c8585288082e06',
    miner: '0x4200000000000000000000000000000000000011',
    mixHash: '0xa38f6dfd6f9dc148dc5dd7ab030f1f9bd4a4700d6e4119e8a51d24239d686baa',
    nonce: '0x0000000000000000',
    number: 26497584n,
    parentHash: '0x35de090e54caf49f690b4037753bbfd8853f3c4601794a308411d2d8568596c3',
    receiptsRoot: '0xa546c35152c9a0bcfd3bf6bf5a83c3fdb3641cc1649a8a1f1ad7af426be1cca1',
    sha3Uncles: '0x1dcc4de8dec75d7aab85b567b6ccd41ad312451b948a7413f0a142fd40d49347',
    size: 52024n,
    stateRoot: '0x6cc08e7ddb66e9a30e1fe0e55670fc37f8aebe64f785946863bfc4e91815b356',
    timestamp: 1739784515n,
    totalDifficulty: null,
    transactionsRoot: '0xad481cc12ae27c420e68592036c44f1a6e32feb8eb9afd7d506a450c65e0e529'
  },
  transaction: {
    from: '0x0303E43F1FE3A6a7B5Ea2d8ae2D59511d08f4E04',
    gas: 600000n,
    hash: '0x6a715a884d91f95538fb7a162647e9fa715370ad09dc79c89313cd9628d522e3',
    input: '0x7ff36ab5000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000800000000000000000000000000303e43f1fe3a6a7b5ea2d8ae2d59511d08f4e040000000000000000000000000000000000000000000000000000000067b301a50000000000000000000000000000000000000000000000000000000000000002000000000000000000000000420000000000000000000000000000000000000600000000000000000000000058895de44e9e4d61f9d96181363171b1300d141c',
    nonce: 9,
    r: '0xbb12d0c06d1a0c328eb3ee6edb4955bdbcaa8e8c37b53968ba30a07f183375c7',
    s: '0x42b68975f6dee601034b06f094a2ea1b56cab361a4b3ba7b3318bed0abca8e38',
    to: '0x4752ba5DBc23f44D87826276BF6Fd6b1C372aD24',
    transactionIndex: 93,
    value: 48914890000000000n,
    v: 16941n,
    type: 'legacy',
    gasPrice: 3394931n
  },
  transactionReceipt: undefined
}
```