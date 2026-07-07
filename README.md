[![MseeP.ai Security Assessment Badge](https://mseep.net/pr/brcreactor9-solanamcpservice-badge.png)](https://mseep.ai/app/brcreactor9-solanamcpservice)

# Solana MCP (Model Context Protocol) Server

This is an MCP server that allows large language models (LLMs) like Claude to interact with the Solana blockchain. The service provides a set of APIs and WebSocket interfaces that enable AI models to perform operations such as querying balances, sending tokens, retrieving transaction history, creating custom PumpFun tokens, and more.

**Live Service:** The MCP service is now live and accessible at [https://mcp.so/](https://mcp.so/)

## Features

- Provides REST API endpoints for Solana blockchain interaction
- Supports WebSocket connections for real-time updates
- Includes handlers specifically designed for MCP
- Built-in HTML test interface for easy testing of all features
- Secure handling of blockchain operations
- Comprehensive error handling and logging
- Custom PumpFun token creation capability

## Quick Start

### Using the Deployed Service

The easiest way to get started is to use our deployed MCP service:

1. Access the service at [https://mcp.so/](https://mcp.so/)
2. Use the test interface to try out the available features
3. Integrate with your AI applications using the API endpoints

### Self-Hosting

### Prerequisites

- Node.js (v14+)
- npm or yarn
- Solana CLI tools (optional, for advanced testing)

### Installation

1. Clone this repository and install dependencies:

```bash
# Install dependencies
npm install
```

2. Configure environment variables:

```bash
# Copy the example configuration file
copy .env.example .env   # For Windows
# cp .env.example .env   # For Linux

# Edit the .env file to set your configuration
```

3. Start the server:

```bash
npm start
```

The server will run on http://localhost:3000 by default. You can visit this address to view the test interface.

## Project Structure

```
├── src/
│   ├── controllers/    # API endpoint handlers
│   ├── services/       # Business logic
│   ├── models/         # Data models
│   ├── utils/          # Helper functions
│   ├── middleware/     # Express middleware
│   ├── mcp/            # MCP-specific handlers
│   └── index.js        # Entry point
├── public/             # Static assets
├── tests/              # Test files
└── .env                # Environment configuration
```

## API Endpoints

### Network Information

```
GET /api/network
```

Get the current Solana network status.

**Response:**
```json
{
  "status": "online",
  "currentSlot": 123456789,
  "network": "devnet"
}
```

### Balance Query

```
GET /api/balance/:address
```

Get the SOL balance for a specified address.

**Response:**
```json
{
  "address": "B4QLphqm5gwfzpo8ibW1EmpoyQwoQybj5v7vJRvXoZn6",
  "balance": 12.34,
  "unit": "SOL"
}
```

### Transaction History

```
GET /api/transactions/:address
```

Get recent transaction history for a specified address.

**Response:**
```json
{
  "transactions": [
    {
      "signature": "xxxxxxxxxxxxxxxxxxxxxx",
      "timestamp": "2023-04-15T14:23:45Z",
      "type": "transfer",
      "amount": 1.5,
      "status": "confirmed"
    }
  ]
}
```

### Create Wallet

```
POST /api/wallet/create
```

Create a new Solana wallet (including public and private keys).

**Response:**
```json
{
  "publicKey": "B4QLphqm5gwfzpo8ibW1EmpoyQwoQybj5v7vJRvXoZn6",
  "secretKey": "[base58 encoded private key]"
}
```

### Transfer SOL

```
POST /api/transfer
```

Send SOL from one address to another.

**Request Body:**
```json
{
  "fromSecretKey": "[base58 encoded private key]",
  "toAddress": "B4QLphqm5gwfzpo8ibW1EmpoyQwoQybj5v7vJRvXoZn6",
  "amount": 0.05
}
```

**Response:**
```json
{
  "transactionId": "xxxxxxxxxxxxxxxxxxxxxx",
  "status": "confirmed"
}
```

### Account Information

```
GET /api/account/:address
```

Get detailed account information for an address.

**Response:**
```json
{
  "address": "B4QLphqm5gwfzpo8ibW1EmpoyQwoQybj5v7vJRvXoZn6",
  "lamports": 12340000000,
  "executable": false,
  "owner": "11111111111111111111111111111111",
  "rentEpoch": 123
}
```

### Create PumpFun Token

```
POST /api/mcp/execute
```

Create a custom PumpFun token on the Solana blockchain.

**Request Body:**
```json
{
  "tool": "createPumpFunToken",
  "params": {
    "name": "My Token Name",
    "symbol": "MTN",
    "description": "This is my custom token",
    "twitter": "https://x.com/xxx",
    "telegram": "https://t.me/xxx",
    "website": "https://mywebsite.com",
    "amount": "0.1",
    "slippage": "10",
    "priorityFee": "0.0005"
  }
}
```

**Response:**
```json
{
  "success": true,
  "transaction": "transaction_signature_hash",
  "mint": "token_mint_address",
  "metadata": "metadata_address"
}
```

### MCP-Specific

```
POST /api/mcp/execute
```

Execute MCP-specific operations such as getting balances, retrieving transaction records, sending SOL, etc.

## MCP Integration

This service is specifically designed to support AI models like Claude in interacting with the Solana blockchain. The following MCP operations are supported:

- `getNetworkStatus` - Get network status
- `getBalance` - Query balance
- `getTransactions` - Get transaction history
- `createWallet` - Create a new wallet
- `transferSol` - Transfer SOL
- `getAccountInfo` - Get account information
- `createPumpFunToken` - Create a custom PumpFun token

### Example MCP Request

```json
{
  "action": "getBalance",
  "parameters": {
    "address": "B4QLphqm5gwfzpo8ibW1EmpoyQwoQybj5v7vJRvXoZn6"
  }
}
```

## WebSocket Support

The server supports real-time updates and command sending via WebSocket connections. The WebSocket service runs on the same port as the HTTP server.

Connect to WebSocket at: `ws://localhost:3000/ws`

### WebSocket Events

- `connect` - Connection established
- `balance` - Balance updates
- `transaction` - New transaction notifications
- `error` - Error notifications

## Deployment

### Recommended Settings for Production Environment

- Use PM2 or Docker for deployment
- Set appropriate environment variables
- Configure HTTPS (recommended for production)
- Set up proper logging
- Implement rate limiting for public-facing APIs

### Docker Deployment

```bash
# Build the image
docker build -t solana-mcp-server .

# Run the container
docker run -p 3000:3000 -d solana-mcp-server
```

### Linux Server Deployment

```bash
# Clone the repository
git clone [repository-url] solana-mcp-server
cd solana-mcp-server

# Install dependencies
npm install --production

# Configure environment
cp .env.example .env
nano .env  # Edit as needed

# Start with PM2 (recommended for production)
npm install -g pm2
pm2 start ecosystem.config.js

# Configure PM2 to start on boot
pm2 startup
pm2 save
```

## Troubleshooting

### Common Issues

1. **Connection errors** - Check if Solana network is reachable and verify your RPC endpoint
2. **Transaction failures** - Ensure you have sufficient SOL for transaction fees
3. **API rate limiting** - Check if you're hitting rate limits on the Solana RPC endpoints
4. **WebSocket disconnections** - Implement proper reconnection logic in your client

### Logs

Check the logs for detailed error information:

```bash
# If using PM2
pm2 logs

# If using Docker
docker logs [container-id]
```

## Security Considerations

- Private key data should only be handled on the client side and should not be stored on the server
- Use HTTPS in production environments
- Consider adding appropriate authentication mechanisms
- Configure reasonable rate limits
- Regularly update dependencies to patch security vulnerabilities
- Use environment variables for sensitive configuration

## Contributing

Issues and pull requests are welcome. Please follow these steps to contribute:

1. Fork the repository
2. Create a feature branch
3. Commit your changes
4. Push to your branch
5. Create a pull request

## License

MIT
