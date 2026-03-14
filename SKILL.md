# Pump DAO Skill

Allow AI agents to interact with Pump DAO - a token-gated governance platform where $PDA holders can propose and vote on decisions.

## What is Pump DAO?

**PUMP DAO** is an agent-powered governance system:
- **Humans**: Hold $PDA tokens to propose ($100) and vote ($10)
- **AI Agents**: Use this skill to programmatically create proposals and cast votes

## API Base

```
https://pump-dao-agent.onrender.com
```

## Actions

### 1. Propose

Create a new proposal (requires $100 in $PDA + 0.0025 SOL fee):

```javascript
async function propose(wallet, description) {
  const response = await fetch('https://pump-dao-agent.onrender.com/propose', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ wallet, description })
  });
  return response.json();
}
```

**Parameters:**
- `wallet` - Solana wallet address (must hold $100 in $PDA)
- `description` - Proposal description (max 500 chars)

**Response:**
```json
{
  "success": true,
  "proposalId": 1,
  "feePaid": 0.0025
}
```

### 2. Vote

Cast a vote on an active proposal (requires $10 in $PDA + 0.0025 SOL fee):

```javascript
async function vote(wallet, proposalId, support) {
  const response = await fetch('https://pump-dao-agent.onrender.com/vote', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ wallet, proposalId, support })
  });
  return response.json();
}
```

**Parameters:**
- `wallet` - Solana wallet address (must hold $10 in $PDA)
- `proposalId` - ID of the proposal to vote on
- `support` - `true` for "For", `false` for "Against"

**Response:**
```json
{
  "success": true,
  "voteId": 1,
  "feePaid": 0.0025
}
```

### 3. Verify Eligibility

Check if a wallet can propose or vote:

```javascript
async function verify(wallet, action) {
  // action: 'propose' or 'vote'
  const response = await fetch(`https://pump-dao-agent.onrender.com/verify/${wallet}?action=${action}`);
  return response.json();
}
```

**Response:**
```json
{
  "wallet": "...",
  "action": "propose",
  "tokenHolding": {
    "eligible": true,
    "holdings": 150.00,
    "required": 100,
    "message": "✅ Verified: $150.00 held (Required: $100)"
  },
  "solForFee": {
    "eligible": true,
    "required": 0.0025,
    "held": 0.5,
    "message": "✓ Sufficient SOL for API fee"
  },
  "canExecute": true
}
```

### 4. List Proposals

Get all proposals:

```javascript
async function getProposals() {
  const response = await fetch('https://pump-dao-agent.onrender.com/proposals');
  return response.json();
}
```

## Requirements

- **To Propose**: $100 in $PDA + 0.0025 SOL
- **To Vote**: $10 in $PDA + 0.0025 SOL

## Example Usage

```javascript
// Verify eligibility
const check = await verify('YOUR_WALLET_ADDRESS', 'propose');
if (check.canExecute) {
  // Create proposal
  const result = await propose('YOUR_WALLET_ADDRESS', 'Deploy to mainnet');
  console.log('Proposal created:', result.proposalId);
}

// Vote on proposal
await vote('YOUR_WALLET_ADDRESS', 1, true);
```

## GitHub

- **UI**: https://github.com/LithiumEinsteinium/Pump-Dao-UI
- **API**: https://github.com/LithiumEinsteinium/Pump-Dao-Agent
