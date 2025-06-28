# 🌊 WETH9 Deposit Tracker - Full Stack Blockchain Indexing Application

A complete production-ready Ponder.js application that indexes WETH9 (Wrapped Ethereum) deposit events on Base network and provides both GraphQL API and React frontend for real-time data visualization.

## 📊 Project Overview

This project demonstrates enterprise-grade blockchain data indexing using modern web technologies. It continuously tracks WETH9 deposit events on Base network (Ethereum L2) and provides multiple ways to access the indexed data through APIs and a responsive web interface.

### 🏗️ Architecture

```
┌─────────────────┐    ┌──────────────────┐    ┌─────────────────┐
│   Base Network  │────│  Ponder Indexer  │────│   PGlite DB     │
│ (WETH9 Contract)│    │  (Event Handler) │    │  (PostgreSQL)   │
└─────────────────┘    └──────────────────┘    └─────────────────┘
                                │
                    ┌───────────┼───────────┐
                    │                       │
            ┌───────▼──────┐       ┌────────▼──────┐
            │ GraphQL API  │       │  React Frontend│
            │   (Port 42069)│       │  (Port 3000)   │
            └──────────────┘       └───────────────┘
```

**Tech Stack:**
- **Backend**: Ponder.js v0.9.28 (Blockchain indexer)
- **Frontend**: Next.js 15 with React 19 and TanStack Query
- **Database**: PGlite (PostgreSQL-compatible embedded database)
- **Blockchain**: Base network (Ethereum L2, Chain ID: 8453)
- **Contract**: WETH9 at `0x4200000000000000000000000000000000000006`
- **Styling**: Tailwind CSS with responsive design
- **Type Safety**: TypeScript throughout the entire stack

## ✨ Features

### Backend Features
- ✅ **Real-time blockchain event indexing** with automatic chain reorganization handling
- ✅ **GraphQL API** with automatic schema generation from database models
- ✅ **SQL Query API** for direct database access (via Ponder client)
- ✅ **Prometheus metrics** at `/metrics` endpoint for monitoring
- ✅ **Health checks** at `/health` endpoint
- ✅ **Type-safe database operations** with Drizzle ORM
- ✅ **Automatic data persistence** with PostgreSQL-compatible storage

### Frontend Features
- ✅ **Live data visualization** showing the 10 latest WETH deposits
- ✅ **Real-time updates** using TanStack Query with automatic refetching
- ✅ **Responsive design** that works on mobile, tablet, and desktop
- ✅ **Interactive elements** including clickable addresses linking to Basescan
- ✅ **Animated number counters** using react-countup for deposit amounts
- ✅ **Loading and error states** with proper UX feedback
- ✅ **Developer tools** integration with React Query Devtools

### Data Features
- ✅ **Indexed 4,065+ deposit events** (as of last run)
- ✅ **Real-time synchronization** with Base network
- ✅ **Automatic ETH amount conversion** from wei to readable format
- ✅ **Timestamp formatting** with locale-aware date display
- ✅ **Truncated address display** for better UX
- ✅ **External link integration** to Base blockchain explorer

## 🚀 Quick Start

### Prerequisites

- **Node.js** 18.14 or higher
- **Package manager**: npm, pnpm, yarn, or bun
- **Git** for cloning the repository

### 1. Clone and Install Backend

```bash
# Clone the repository
git clone <your-repo-url>
cd ponder-example

# Install backend dependencies
npm install
# or: pnpm install / yarn install / bun install
```

### 2. Environment Setup (Optional but Recommended)

Create a `.env` file for better performance:

```bash
# .env file (optional)
# Add your own RPC URL for better rate limits and reliability
PONDER_RPC_URL_8453=https://your-base-rpc-url-here

# Optional: Disable telemetry
PONDER_TELEMETRY_DISABLED=true

# Optional: Database URL for production
# DATABASE_URL=postgresql://user:password@host:port/database
```

**RPC Provider Options:**
- [Alchemy](https://alchemy.com/) - `https://base-mainnet.g.alchemy.com/v2/YOUR_API_KEY`
- [Infura](https://infura.io/) - `https://base-mainnet.infura.io/v3/YOUR_PROJECT_ID`
- [QuickNode](https://quicknode.com/) - Custom Base endpoint
- [Public RPC](https://chainlist.org/chain/8453) - Free but rate limited

### 3. Start the Blockchain Indexer

```bash
npm run dev
# or: pnpm dev / yarn dev / bun dev
```

**What happens when you start the indexer:**
1. 🔗 Connects to Base network RPC endpoint
2. 📊 Creates PGlite database and required tables
3. 🔍 Starts indexing WETH9 deposit events from latest blocks
4. 🌐 Launches GraphQL API server at `http://localhost:42069`
5. 📈 Provides real-time indexing progress and metrics

**Expected Output:**
```bash
✓ Server live at http://localhost:42069
✓ Created tables [deposit_event]
✓ Synced 4,065 events from 'base' network
✓ Progress: ████████████████████████████████████ 100%
```

### 4. Start the Frontend Application

Open a new terminal window:

```bash
# Navigate to frontend directory
cd frontend

# Install frontend dependencies
npm install && npm run dev
# or: pnpm install && pnpm dev
# or: yarn install && yarn dev
# or: bun install && bun dev
```

**Frontend will be available at:** `http://localhost:3000`

### 5. Verify Everything is Working

1. **Backend API Test:**
   ```bash
   curl http://localhost:42069/health
   # Should return: OK
   ```

2. **GraphQL Test:**
   ```bash
   curl -X POST http://localhost:42069/ \
     -H "Content-Type: application/json" \
     -d '{"query":"{ depositEvents { items { id account amount timestamp } } }"}'
   ```

3. **Frontend Test:** Visit `http://localhost:3000` to see the live deposit data

## 📡 API Documentation

### GraphQL API

**Base URL:** `http://localhost:42069/`

The GraphQL API is automatically generated from your database schema and provides type-safe queries.

#### Available Queries

**1. Get All Deposit Events (Paginated)**
```graphql
query GetAllDeposits {
  depositEvents {
    items {
      id
      account
      amount
      timestamp
    }
    pageInfo {
      hasNextPage
      hasPreviousPage
    }
  }
}
```

**2. Get Single Deposit Event**
```graphql
query GetSingleDeposit($id: String!) {
  depositEvent(id: $id) {
    id
    account
    amount
    timestamp
  }
}
```

**3. Get Deposits with Filtering**
```graphql
query GetFilteredDeposits {
  depositEvents(
    where: { 
      amount: { gt: "1000000000000000000" }  # Greater than 1 ETH (in wei)
    }
    orderBy: { timestamp: desc }
    first: 10
  ) {
    items {
      id
      account
      amount
      timestamp
    }
  }
}
```

#### Example Responses

**Successful Response:**
```json
{
  "data": {
    "depositEvents": {
      "items": [
        {
          "id": "0x00203877d2a2e50b4c6eb14f84c9242b7b77b4c09bc1a0790c7e992c1308089f-0x13",
          "account": "0xcaf2da315f5a5499299a312b8a86faafe4bad959",
          "amount": "36368220000000000",
          "timestamp": 1751144103
        }
      ]
    }
  }
}
```

### cURL Examples

**Basic Query:**
```bash
curl -X POST http://localhost:42069/ \
  -H "Content-Type: application/json" \
  -d '{
    "query": "{ depositEvents { items { id account amount timestamp } } }"
  }'
```

**Query with Variables:**
```bash
curl -X POST http://localhost:42069/ \
  -H "Content-Type: application/json" \
  -d '{
    "query": "query($id: String!) { depositEvent(id: $id) { id account amount timestamp } }",
    "variables": { "id": "0x00203877d2a2e50b4c6eb14f84c9242b7b77b4c09bc1a0790c7e992c1308089f-0x13" }
  }'
```

### Monitoring Endpoints

**Health Check:**
```bash
curl http://localhost:42069/health
# Returns: OK
```

**Prometheus Metrics:**
```bash
curl http://localhost:42069/metrics
# Returns: Detailed metrics in Prometheus format
```

**Key Metrics Available:**
- `ponder_indexing_completed_events` - Total events processed
- `ponder_sync_block` - Current synced block number
- `ponder_http_server_active_requests` - Active API requests
- `ponder_realtime_latency` - Indexing latency metrics

## 🗃️ Data Schema

### DepositEvent Table

| Field | Type | Description | Example |
|-------|------|-------------|---------|
| **id** | `text` (Primary Key) | Unique event identifier: `${transactionHash}-${logIndex}` | `0x00203877...089f-0x13` |
| **account** | `hex` | Ethereum address that deposited ETH | `0xcaf2da315f5a5499299a312b8a86faafe4bad959` |
| **amount** | `bigint` | Amount of ETH deposited in wei | `36368220000000000` (0.036 ETH) |
| **timestamp** | `integer` | Unix timestamp when deposit occurred | `1751144103` |

### Understanding the Data

**Event ID Format:** `{transactionHash}-{logIndex}`
- Transaction hash: Unique identifier for the blockchain transaction
- Log index: Position of the event within the transaction's event logs

**Amount Field:** Stored in wei (smallest ETH unit)
- 1 ETH = 1,000,000,000,000,000,000 wei
- Frontend automatically converts to readable ETH format
- Use `formatEther()` from viem for manual conversion

**Account Field:** Ethereum address of the depositor
- Always 42 characters (0x + 40 hex characters)
- Links to Base network blockchain explorer in frontend

## 🎨 Frontend Architecture

### Component Structure

```
frontend/
├── app/
│   ├── layout.tsx          # Root layout with providers
│   ├── page.tsx            # Main page with data fetching
│   ├── providers.tsx       # React Query & Ponder providers
│   └── globals.css         # Global styles
├── components/
│   └── deposits-table.tsx  # Main data display component
├── lib/
│   └── ponder.ts          # Ponder client configuration
└── ponder.schema.ts       # Shared schema with backend
```

### Key Features Explained

**1. Real-time Data Updates**
```typescript
// lib/ponder.ts - Client configuration
const client = createClient("http://localhost:42069/sql", { schema });

const depositsQueryOptions = getPonderQueryOptions(client, (db) =>
  db
    .select()
    .from(schema.depositEvent)
    .orderBy(desc(schema.depositEvent.timestamp))
    .limit(10)
);
```

**2. Responsive Design**
```tsx
// Grid system that adapts to screen size
<li className="grid grid-cols-2 py-2 w-full text-lg font-semibold sm:grid-cols-3">
  <a href={`https://basescan.org/address/${account}`}>
    {account.slice(0, 6)}...{account.slice(38)}
  </a>
  <CountUp end={Number(formatEther(amount))} decimals={5} />
  <p className="hidden text-sm sm:flex">
    {new Date(timestamp * 1000).toLocaleString()}
  </p>
</li>
```

**3. Animated Number Display**
```tsx
<CountUp
  start={0}
  end={Number(formatEther(amount))}
  duration={2.5}
  decimals={5}
  separator=","
  className="text-sm font-semibold"
/>
```

### State Management

- **TanStack Query** for server state management and caching
- **Ponder React** for seamless blockchain data integration
- **React Query Devtools** for debugging and monitoring
- **Automatic refetching** for real-time updates

## 🛠️ Development Guide

### Project Structure Deep Dive

```
ponder-example/
├── src/
│   ├── index.ts              # Event indexing logic & handlers
│   └── api/
│       └── index.ts          # API route configuration (Hono + Ponder)
├── abis/
│   └── Weth9Abi.ts          # WETH9 contract ABI definitions
├── ponder.config.ts          # Ponder configuration (networks, contracts)
├── ponder.schema.ts          # Database schema definitions
├── tsconfig.json            # TypeScript configuration
├── package.json             # Dependencies and scripts
└── frontend/                # React application
    ├── app/                 # Next.js 15 app router
    ├── components/          # Reusable React components
    ├── lib/                 # Utility functions and configurations
    └── ponder.schema.ts     # Schema shared with backend
```

### Adding New Events

To index additional WETH9 events (Withdrawal, Transfer, etc.):

**1. Update the ABI:**
```typescript
// abis/Weth9Abi.ts
export const weth9Abi = [
  // ... existing Deposit event
  {
    type: 'event',
    name: 'Withdrawal',
    inputs: [
      { name: 'src', type: 'address', indexed: true },
      { name: 'wad', type: 'uint256' }
    ]
  }
] as const;
```

**2. Extend the Schema:**
```typescript
// ponder.schema.ts
export const withdrawalEvent = onchainTable("withdrawal_event", (t) => ({
  id: t.text().primaryKey(),
  timestamp: t.integer().notNull(),
  amount: t.bigint().notNull(),
  account: t.hex().notNull(),
}));
```

**3. Add Event Handler:**
```typescript
// src/index.ts
ponder.on("weth9:Withdrawal", async ({ event, context }) => {
  await context.db.insert(schema.withdrawalEvent).values({
    id: event.log.id,
    account: event.args.src,  // Note: 'src' for withdrawals vs 'dst' for deposits
    amount: event.args.wad,
    timestamp: Number(event.block.timestamp),
  });
});
```

**4. Update Frontend:**
```typescript
// frontend/lib/ponder.ts
const withdrawalsQueryOptions = getPonderQueryOptions(client, (db) =>
  db
    .select()
    .from(schema.withdrawalEvent)
    .orderBy(desc(schema.withdrawalEvent.timestamp))
    .limit(10)
);
```

### Understanding Event Data Structure

When you log an event in your handler, you'll see:

```typescript
console.log('Full Event Object:', event);
```

**Output Structure:**
```javascript
{
  name: 'Deposit',
  args: {
    dst: '0x4752ba5DBc23f44D87826276BF6Fd6b1C372aD24',  // Depositor address
    wad: 48914890000000000n                              // Amount in wei (bigint)
  },
  log: {
    id: '0x...ed',           // Unique event ID
    address: '0x4200...',    // WETH9 contract address
    logIndex: 237,           // Position in transaction logs
    topics: [...],           // Indexed event parameters
    data: '0x...'           // Non-indexed event data
  },
  block: {
    number: 26497584n,       // Block number
    timestamp: 1739784515n,  // Unix timestamp
    hash: '0x...',          // Block hash
    // ... other block metadata
  },
  transaction: {
    hash: '0x...',          // Transaction hash
    from: '0x...',          // Transaction sender
    to: '0x...',            // Transaction recipient
    value: 48914890000000000n, // ETH value sent
    // ... other transaction data
  }
}
```

### Testing and Debugging

**1. Verify Event Processing:**
```bash
# Check logs for event IDs
npm run dev
# Look for output like: 0xafbbf0a6360725a42466acaf79618c6ddd999f968186bbdefc2c17ddb221ee89-0x6a
```

**2. Test GraphQL Queries:**
Visit `http://localhost:42069` in your browser for the GraphQL playground.

**3. Monitor Performance:**
```bash
curl http://localhost:42069/metrics | grep ponder_indexing
```

**4. Frontend Development:**
```bash
cd frontend
npm run dev
# React Query Devtools available in browser
```

### Common Development Patterns

**Error Handling in Event Handlers:**
```typescript
ponder.on("weth9:Deposit", async ({ event, context }) => {
  try {
    // Validate data before insertion
    if (!event.args.dst || !event.args.wad) {
      console.warn('Invalid deposit event:', event.log.id);
      return;
    }

    await context.db.insert(schema.depositEvent).values({
      id: event.log.id,
      account: event.args.dst,
      amount: event.args.wad,
      timestamp: Number(event.block.timestamp),
    });
  } catch (error) {
    console.error('Failed to process deposit:', error);
    // Ponder will retry the event automatically
  }
});
```

**Custom Data Transformations:**
```typescript
ponder.on("weth9:Deposit", async ({ event, context }) => {
  const amountInEth = Number(formatEther(event.args.wad));
  
  await context.db.insert(schema.depositEvent).values({
    id: event.log.id,
    account: event.args.dst,
    amount: event.args.wad,
    amountEth: amountInEth,  // Store converted amount for easier querying
    timestamp: Number(event.block.timestamp),
    blockNumber: Number(event.block.number),
  });
});
```

## 🚀 Production Deployment

### Environment Configuration

**Production Environment Variables:**
```bash
# .env.production
# Required: Production database
DATABASE_URL=postgresql://username:password@host:port/database

# Required: Dedicated RPC endpoint
PONDER_RPC_URL_8453=https://your-production-rpc-url

# Security: Disable telemetry
PONDER_TELEMETRY_DISABLED=true

# Performance: Optimize for production
NODE_ENV=production
PONDER_LOG_LEVEL=info

# Optional: Custom port
PORT=3001
```

### Database Setup

**For Production with PostgreSQL:**

1. **Set up PostgreSQL database:**
   ```sql
   CREATE DATABASE ponder_weth9_tracker;
   CREATE USER ponder_user WITH PASSWORD 'secure_password';
   GRANT ALL PRIVILEGES ON DATABASE ponder_weth9_tracker TO ponder_user;
   ```

2. **Configure connection:**
   ```bash
   DATABASE_URL=postgresql://ponder_user:secure_password@localhost:5432/ponder_weth9_tracker
   ```

### Deployment Platforms

**1. Railway (Recommended for Ponder)**
```bash
# Install Railway CLI
npm install -g @railway/cli

# Deploy
railway login
railway init
railway up
```

**2. Vercel (Frontend) + Railway (Backend)**
```bash
# Deploy frontend to Vercel
cd frontend
vercel --prod

# Deploy backend to Railway
cd ..
railway up
```

**3. Docker Deployment**
```dockerfile
# Dockerfile
FROM node:18-alpine
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production
COPY . .
EXPOSE 42069
CMD ["npm", "start"]
```

### Performance Optimization

**1. Database Indexing:**
```sql
-- Add indexes for common queries
CREATE INDEX idx_deposit_event_timestamp ON deposit_event(timestamp DESC);
CREATE INDEX idx_deposit_event_account ON deposit_event(account);
CREATE INDEX idx_deposit_event_amount ON deposit_event(amount);
```

**2. RPC Optimization:**
- Use dedicated RPC providers (Alchemy, Infura, QuickNode)
- Enable RPC caching where available
- Monitor rate limits and upgrade plans as needed

**3. Monitoring Setup:**
```bash
# Prometheus metrics scraping
curl http://your-domain.com:42069/metrics

# Health check endpoint
curl http://your-domain.com:42069/health
```

### Scaling Considerations

**Horizontal Scaling:**
- Multiple Ponder instances can read from the same database
- Use load balancers for API requests
- Consider read replicas for heavy query workloads

**Data Retention:**
```typescript
// Optional: Add data cleanup for old events
ponder.on("weth9:Deposit", async ({ event, context }) => {
  // Insert new event
  await context.db.insert(schema.depositEvent).values({...});
  
  // Optional: Clean up events older than 30 days
  const thirtyDaysAgo = Math.floor(Date.now() / 1000) - (30 * 24 * 60 * 60);
  await context.db
    .delete(schema.depositEvent)
    .where(lt(schema.depositEvent.timestamp, thirtyDaysAgo));
});
```

## 🔧 Troubleshooting

### Common Issues and Solutions

**1. RPC Rate Limiting**
```
Error: Rate limit exceeded
```
**Solution:** 
- Switch to a dedicated RPC provider (Alchemy, Infura)
- Add `PONDER_RPC_URL_8453` to your environment variables
- Monitor usage and upgrade plans as needed

**2. Database Connection Issues**
```
Error: Database connection failed
```
**Solution:**
- Check `DATABASE_URL` format: `postgresql://user:pass@host:port/db`
- Ensure database server is running and accessible
- Verify network connectivity and firewall rules

**3. Frontend API Connection Issues**
```
Network Error: Could not connect to localhost:42069
```
**Solution:**
- Ensure Ponder backend is running (`npm run dev`)
- Check if port 42069 is available
- Verify CORS settings in production

**4. Event Processing Delays**
```
Warning: Indexing is behind by X blocks
```
**Solution:**
- Monitor network congestion on Base
- Increase RPC rate limits
- Check for complex event handler logic

**5. Memory Usage in Production**
```
Out of memory errors
```
**Solution:**
- Increase container memory limits
- Implement data cleanup for old events
- Use database connection pooling

### Performance Monitoring

**Key Metrics to Track:**
- Indexing latency: `ponder_realtime_latency`
- Event processing rate: `ponder_indexing_completed_events`
- API response times: `ponder_http_server_request_duration_ms`
- Database query performance: `ponder_database_method_duration`

**Health Check Script:**
```bash
#!/bin/bash
# health-check.sh
API_URL="http://localhost:42069"

# Check API health
health=$(curl -s "$API_URL/health")
if [ "$health" != "OK" ]; then
  echo "API health check failed"
  exit 1
fi

# Check latest events
events=$(curl -s -X POST "$API_URL/" \
  -H "Content-Type: application/json" \
  -d '{"query":"{ depositEvents { items { id } } }"}' | \
  jq '.data.depositEvents.items | length')

if [ "$events" -lt 1 ]; then
  echo "No events found"
  exit 1
fi

echo "Health check passed: $events events available"
```

### Development Tips

**1. Faster Development Iterations:**
```bash
# Use latest block instead of historical sync
# ponder.config.ts
startBlock: "latest"  // Start from current block
```

**2. Debug Event Processing:**
```typescript
ponder.on("weth9:Deposit", async ({ event, context }) => {
  console.log('Processing event:', {
    id: event.log.id,
    account: event.args.dst,
    amount: event.args.wad.toString(),
    block: event.block.number.toString()
  });
  // ... rest of handler
});
```

**3. Query Optimization:**
```typescript
// Add database indexes for better performance
export const depositEvent = onchainTable("deposit_event", (t) => ({
  id: t.text().primaryKey(),
  timestamp: t.integer().notNull(),
  amount: t.bigint().notNull(),
  account: t.hex().notNull(),
}), (table) => ({
  timestampIdx: index("timestamp_idx").on(table.timestamp),
  accountIdx: index("account_idx").on(table.account),
}));
```

## 📚 Additional Resources

### Official Documentation
- **[Ponder Documentation](https://ponder.sh/docs)** - Complete Ponder.js guide
- **[Base Network Documentation](https://docs.base.org/)** - Base L2 network details
- **[WETH9 Contract](https://basescan.org/address/0x4200000000000000000000000000000000000006)** - Contract on Basescan
- **[GraphQL Playground](http://localhost:42069/)** - Interactive API explorer (when running locally)

### Technology Documentation
- **[TanStack Query](https://tanstack.com/query/latest)** - Powerful data synchronization
- **[Next.js 15](https://nextjs.org/docs)** - React framework documentation
- **[Tailwind CSS](https://tailwindcss.com/docs)** - Utility-first CSS framework
- **[Viem](https://viem.sh/)** - TypeScript Ethereum library

### Community and Support
- **[Ponder Discord](https://discord.gg/ponder)** - Community support and discussions
- **[Base Discord](https://discord.gg/base)** - Base network community
- **[GitHub Issues](https://github.com/ponder-sh/ponder/issues)** - Bug reports and feature requests

### Example Projects
- **[Ponder Examples](https://github.com/ponder-sh/ponder/tree/main/examples)** - Official example projects
- **[Base Ecosystem](https://base.org/ecosystem)** - Other Base network applications
- **[DeFi Examples](https://ponder.sh/docs/guides/add-contracts)** - Additional DeFi indexing patterns

## 📄 License

MIT License - see [LICENSE](LICENSE) file for details.

---

## 🙏 Contributing

Contributions are welcome! Please feel free to submit a Pull Request. For major changes, please open an issue first to discuss what you would like to change.

### Development Workflow

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Make your changes
4. Add tests if applicable
5. Commit your changes (`git commit -m 'Add amazing feature'`)
6. Push to the branch (`git push origin feature/amazing-feature`)
7. Open a Pull Request

**Built with ❤️ by the blockchain development community**