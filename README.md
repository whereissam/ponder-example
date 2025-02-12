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

[ci-badge]: https://github.com/ponder-sh/ponder/actions/workflows/main.yml/badge.svg
[ci-url]: https://github.com/ponder-sh/ponder/actions/workflows/main.yml
[tg-badge]: https://img.shields.io/endpoint?color=neon&logo=telegram&label=Chat&url=https%3A%2F%2Fmogyo.ro%2Fquart-apis%2Ftgmembercount%3Fchat_id%3Dponder_sh
[tg-url]: https://t.me/ponder_sh
[license-badge]: https://img.shields.io/npm/l/ponder?label=License
[license-url]: https://github.com/ponder-sh/ponder/blob/main/LICENSE
[version-badge]: https://img.shields.io/npm/v/ponder
[version-url]: https://github.com/ponder-sh/ponder/releases
