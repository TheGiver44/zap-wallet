# ZAP Wallet: Private Transfers & Micropayment System

## Overview

This guide covers the implementation of completely private transfers and micropayment gateway system for ZAP Wallet, inspired by x402 protocol for low-cost transactions.

## 🚀 Features Implemented

### Private Transfer System
- **Multi-layer Privacy**: Jito bundles + Stealth addresses + Mixing pools
- **Privacy Levels**: BASIC, ADVANCED, MAXIMUM
- **Stealth Addresses**: Generated from user seed phrase with different derivation paths
- **Mixing Pools**: Multi-hop routing through random intermediaries
- **Time Delays**: Random delays for enhanced privacy
- **Cost Optimization**: Dynamic fee calculation based on privacy level

### Micropayment Gateway
- **x402-inspired**: Low-cost per-use payments for dApps and services
- **Pool Management**: Dedicated micropayment pool with auto top-up
- **Service Providers**: Register and manage payment recipients
- **Paywall Integration**: Premium content access with instant payments
- **Express.js Backend**: REST API for payment processing

## 📁 File Structure

```
src/
├── privacy/
│   ├── modules/
│   │   ├── EnhancedStealthTransfer.js    # Advanced stealth transfers
│   │   ├── JitoPrivateRPC.js            # Jito bundle integration
│   │   └── PrivacySwapRouter.js         # Private DEX swaps
│   ├── config/
│   │   └── privacyConfig.js             # Privacy configuration
│   ├── utils/
│   │   └── privacyHelpers.js            # Privacy utilities
│   └── EnhancedPrivacyManager.js        # Main privacy orchestrator
├── micropayments/
│   ├── MicropaymentGateway.js           # Micropayment system
│   ├── PaywallComponent.js              # React paywall component
│   └── backend/
│       ├── server.js                    # Express.js backend
│       └── package.json                 # Backend dependencies
└── components/
    └── Wallet.js                        # Updated wallet component
```

## 🔧 Installation & Setup

### 1. Install Dependencies

```bash
# Frontend dependencies (already in main package.json)
npm install @solana/web3.js bip39 ed25519-hd-key tweetnacl bs58

# Backend dependencies
cd src/micropayments/backend
npm install express cors helmet express-rate-limit
```

### 2. Start Backend Server

```bash
cd src/micropayments/backend
npm start
# Server runs on http://localhost:3001
```

### 3. Update Wallet Component

Replace the existing PrivacyManager import in `Wallet.js`:

```javascript
// Old import
import PrivacyManager from '../privacy/PrivacyManager.js';

// New import
import EnhancedPrivacyManager from '../privacy/EnhancedPrivacyManager.js';
```

## 🛡️ Privacy Levels Explained

### BASIC Privacy
- **Features**: Jito bundles only
- **Cost**: ~0.000006 SOL
- **Speed**: ~2 seconds
- **Use Case**: Mempool privacy for standard transfers

### ADVANCED Privacy
- **Features**: Jito + Stealth addresses + Mixing pools
- **Cost**: ~0.000012 SOL
- **Speed**: ~30 seconds
- **Use Case**: Enhanced privacy with moderate cost

### MAXIMUM Privacy
- **Features**: All features + Time delays + ZK proofs (future)
- **Cost**: ~0.000020 SOL
- **Speed**: ~60 seconds
- **Use Case**: Maximum privacy for sensitive transactions

## 💰 Micropayment System

### Pool Management
```javascript
// Initialize micropayment gateway
const gateway = new MicropaymentGateway({
  connection: connection,
  autoTopUp: true,
  minPoolBalance: 0.01, // SOL
  topUpAmount: 0.1      // SOL
});

await gateway.initialize(userWalletAddress);
```

### Service Provider Registration
```javascript
// Register a new service provider
const success = gateway.registerServiceProvider({
  id: 'my-dapp',
  name: 'My dApp Service',
  address: 'RecipientWalletAddress...',
  feeRate: 0.001,        // 0.1% fee
  minAmount: 0.000001,   // 1 microSOL
  maxAmount: 0.01,       // 0.01 SOL
  description: 'My dApp payment service'
});
```

### Processing Micropayments
```javascript
// Process a micropayment
const result = await gateway.processMicropayment({
  serviceId: 'my-dapp',
  amount: 0.001, // 0.001 SOL
  description: 'API call payment',
  metadata: { apiEndpoint: '/data' }
});
```

## 🎨 Paywall Integration

### Basic Paywall Usage
```jsx
import PaywallComponent from '../micropayments/PaywallComponent.js';

function PremiumContent() {
  return (
    <PaywallComponent
      contentId="premium-analytics"
      price={0.005} // 0.005 SOL
      accessType="temporary" // or "permanent", "subscription"
      onAccessGranted={(session) => console.log('Access granted!')}
      onAccessDenied={(error) => console.log('Access denied:', error)}
    >
      <div>
        <h2>Premium Analytics Dashboard</h2>
        <p>This content requires payment to access.</p>
      </div>
    </PaywallComponent>
  );
}
```

### Paywall with Custom Styling
```jsx
<PaywallComponent
  contentId="premium-feature"
  price={0.01}
  accessType="permanent"
  className="custom-paywall"
  style={{
    border: '2px solid #4ECDC4',
    borderRadius: '12px',
    padding: '20px'
  }}
>
  {/* Premium content */}
</PaywallComponent>
```

## 🔌 API Endpoints

### Payment Management
```bash
# Create payment session
POST /api/payments/create
{
  "serviceId": "my-service",
  "amount": 0.001,
  "metadata": { "description": "API call" }
}

# Process payment
POST /api/payments/process
{
  "sessionId": "ps_1234567890_abcdef",
  "poolAddress": "PoolWalletAddress...",
  "transactionSignature": "TransactionSignature..."
}

# Get payment status
GET /api/payments/{sessionId}
```

### Paywall Management
```bash
# Create paywall session
POST /api/paywall/create
{
  "contentId": "premium-content",
  "price": 0.005,
  "accessType": "temporary"
}

# Check access
GET /api/paywall/{sessionId}/access

# Grant access
POST /api/paywall/{sessionId}/grant
{
  "paymentSessionId": "ps_1234567890_abcdef"
}
```

### Service Provider Management
```bash
# Register provider
POST /api/providers/register
{
  "id": "my-service",
  "name": "My Service",
  "address": "RecipientAddress...",
  "feeRate": 0.001,
  "minAmount": 0.000001,
  "maxAmount": 0.01,
  "description": "Service description"
}

# Get all providers
GET /api/providers
```

## 🚀 Usage Examples

### 1. Private Transfer
```javascript
// Create private transfer
const result = await privacyManager.createPrivateTransaction({
  from: userWalletAddress,
  to: recipientAddress,
  amount: 0.1, // SOL
  privacyLevel: 'ADVANCED',
  memo: 'Private transfer'
});

if (result.success) {
  console.log('Transaction created:', result.transaction);
  console.log('Features:', result.features);
  console.log('Cost:', result.costBreakdown);
}
```

### 2. Micropayment for dApp Service
```javascript
// Process micropayment for API call
const payment = await micropaymentGateway.processMicropayment({
  serviceId: 'defi-analytics',
  amount: 0.0001, // 0.0001 SOL
  description: 'DeFi analytics query',
  metadata: { query: 'token-prices', timestamp: Date.now() }
});

if (payment.success) {
  console.log('Payment successful:', payment.transactionId);
  // Proceed with API call
  const data = await fetchAnalyticsData();
}
```

### 3. Paywall for Premium Content
```jsx
// In your React component
<PaywallComponent
  contentId="advanced-charts"
  price={0.01}
  accessType="temporary"
  onAccessGranted={() => {
    // Load premium charts
    loadAdvancedCharts();
  }}
>
  <AdvancedCharts />
</PaywallComponent>
```

## 🔒 Security Considerations

### Private Transfers
- **Stealth Addresses**: Generated from user seed with different derivation paths
- **Mixing Pools**: Random routing through multiple addresses
- **Time Delays**: Random delays to prevent timing analysis
- **Jito Bundles**: Bypass public mempool for privacy

### Micropayments
- **Pool Isolation**: Dedicated micropayment pool separate from main wallet
- **Auto Top-up**: Automatic funding when pool balance is low
- **Transaction Validation**: On-chain verification of payments
- **Rate Limiting**: API rate limiting to prevent abuse

## 📊 Performance Optimization

### Privacy Levels
- **Cost Optimization**: Dynamic fee calculation based on network conditions
- **Speed Optimization**: Parallel processing where possible
- **Pool Management**: Efficient stealth address pool management

### Micropayments
- **Batch Processing**: Group multiple small payments
- **Pool Efficiency**: Optimize pool balance management
- **Caching**: Cache service provider information

## 🧪 Testing

### Unit Tests
```bash
# Run privacy module tests
npm test -- --testPathPattern=privacy

# Run micropayment tests
npm test -- --testPathPattern=micropayments
```

### Integration Tests
```bash
# Test private transfers
npm run test:private-transfers

# Test micropayments
npm run test:micropayments
```

## 📈 Monitoring & Analytics

### Privacy Metrics
- Total transactions by privacy level
- Average confirmation times
- Cost savings compared to standard transactions
- Privacy feature usage statistics

### Micropayment Metrics
- Total micropayments processed
- Average payment amounts
- Service provider usage
- Pool balance trends

## 🔮 Future Enhancements

### Planned Features
- **Zero-Knowledge Proofs**: Enhanced privacy with ZK proofs
- **Cross-Chain Privacy**: Privacy across multiple blockchains
- **Advanced Mixing**: More sophisticated mixing algorithms
- **Privacy Analytics**: Detailed privacy metrics and insights

### Micropayment Enhancements
- **Subscription Payments**: Recurring payment support
- **Payment Splitting**: Split payments across multiple recipients
- **Dynamic Pricing**: AI-driven pricing optimization
- **Privacy-Preserving Payments**: Private micropayments

## 🆘 Troubleshooting

### Common Issues

#### Privacy Button Disabled
- **Cause**: PrivacyManager initialization failed
- **Solution**: Check connection status and retry initialization

#### Micropayment Failed
- **Cause**: Insufficient pool balance
- **Solution**: Top up micropayment pool or enable auto top-up

#### Paywall Not Working
- **Cause**: Backend server not running
- **Solution**: Start Express.js backend server

### Debug Mode
```javascript
// Enable debug logging
localStorage.setItem('zap-debug', 'true');

// Check privacy status
console.log(privacyManager.getPrivacyStatus());

// Check micropayment status
console.log(micropaymentGateway.getPoolStatus());
```

## 📞 Support

For technical support or questions:
- **GitHub Issues**: Create an issue in the repository
- **Documentation**: Check this guide and inline code comments
- **Community**: Join the ZAP Wallet community discussions

---

**Note**: This system is designed for educational and development purposes. For production use, ensure proper security audits and testing.
