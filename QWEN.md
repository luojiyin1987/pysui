# QWEN Project Context

## Project Overview
**pysui** is a Python Client SDK for the Sui blockchain. It provides comprehensive functionality to interact with the Sui blockchain through multiple API interfaces: JSON RPC, GraphQL (Alpha), and gRPC (Beta). The project enables Python developers to build applications that interact with the Sui blockchain using familiar Python patterns and conventions.

### Key Features
- **Multi-API Support**: Supports JSON RPC, GraphQL, and gRPC interfaces to Sui blockchain
- **Async/Sync Clients**: Both asynchronous and synchronous client implementations
- **Transaction Builder**: Programmable Transaction Builder (PTB) for constructing complex transactions
- **BCS Support**: Built-in support for Binary Canonical Serialization (BCS)
- **Address Management**: Comprehensive address and keypair management
- **Multiple Network Support**: Devnet, testnet, and mainnet configurations

### Project Structure
```
pysui/
├── pysui/                 # Main SDK package
│   ├── abstracts/         # Abstract base classes
│   ├── sui/               # Core Sui SDK implementation
│   │   ├── sui_bcs/       # BCS serialization
│   │   ├── sui_builders/  # Transaction builders
│   │   ├── sui_clients/   # API clients (JSON RPC, GraphQL, gRPC)
│   │   ├── sui_common/    # Common utilities
│   │   ├── sui_grpc/      # gRPC client implementation
│   │   ├── sui_pgql/      # GraphQL client implementation
│   │   ├── sui_txn/       # Transaction utilities
│   │   ├── sui_txresults/ # Transaction result types
│   │   └── sui_types/     # Type definitions
├── samples/              # Example applications and utilities
├── tests/                # Test suite
├── pyproject.toml        # Project configuration
├── README.md             # Main documentation
├── DEVELOP.md            # Development guide
└── ...
```

## Building and Running

### Prerequisites
- Python 3.10 or greater
- Rust (stable) with rustup and cargo
- pkg-config
- libtool
- git
- Sui binaries (for publish function)
- protoc for protobuf generation

### Installation
The package can be installed via pip:
```bash
pip install pysui
```

### Development Setup
1. Clone the repository:
   ```bash
   git clone git@github.com:FrankC01/pysui.git
   ```

2. Set up virtual environment:
   ```bash
   python3 -m venv env
   source env/bin/activate
   ```

3. Install dependencies:
   ```bash
   pip install -r requirements.txt
   pip install -r requirements-dev.txt
   ```

4. Build the distribution (optional for development):
   ```bash
   bin/package-build.sh
   ```

### API Support Status
- **JSON RPC**: Stable (full support)
- **GraphQL**: Alpha (limited support, no subscriptions)
- **gRPC**: Beta (full support, including subscriptions)

## Development Conventions

### Python Version
- Minimum Python version: 3.10
- Supports Python 3.10, 3.11, 3.12, 3.13

### Code Organization
- Core functionality in `pysui/sui/` directory
- Client implementations separated by API type (GraphQL, gRPC, JSON RPC)
- Type definitions and builders in dedicated modules
- Abstract base classes for common interfaces

### Testing
- Test suite located in `tests/` directory
- Includes both synchronous and asynchronous tests
- Integration tests available in `tests/integration_sync`

### Dependencies
Key dependencies include:
- `betterproto2[grpclib]` for gRPC support
- `gql[httpx,websockets]` for GraphQL support
- `httpx` for HTTP client functionality
- `pysui-fastcrypto` for cryptographic operations

## Major Components

### Client Types
1. **SyncClient/AsyncClient**: JSON RPC clients
2. **SyncGqlClient/AsyncGqlClient**: GraphQL clients
3. **SuiGrpcClient**: gRPC client

### Key Classes
- `SuiConfig`: Configuration management
- `SuiAddress`: Address type representation
- `ObjectID`: Object identifier type
- `PreExecutionResult`, `SuiRpcResult`: Result wrappers
- `SuiTransaction`: Transaction builder interface

### Available Tools
The SDK comes with several command-line utilities:
- `wallet`: Full-featured wallet implementation
- `async-gas`: Report SUI coins
- `sgqls`: Write GraphQL schema
- `smash`: Merge SUI coins
- `splay`: Spread coins to multiple addresses
- `jtobcs`: Convert JSON to BCS module
- `bcstoj`: Convert BCS module to JSON
- `mtobcs`: Generate BCS from Move structures

### Sample Applications
Located in the `samples/` directory:
- `pgql_a_example.py`: Asynchronous GraphQL examples
- `pgql_s_example.py`: Synchronous GraphQL examples
- `pgrpc_a_example.py`: Asynchronous gRPC examples
- `walletg.py`: Full wallet implementation

## Architecture Notes
- The SDK follows a layered architecture with clients at the highest level
- GraphQL and gRPC implementations are newer additions alongside the original JSON RPC client
- BCS support is built-in for complex serialization needs
- Configuration is managed via `SuiConfig` class with profiles for different networks
- Type safety is enforced through custom type definitions in `sui_types/`

## Environment Support
- Supports devnet, testnet, and mainnet deployments
- Uses configuration profiles to manage different network settings
- Local node support available through suibase integration (JSON RPC only)

## Testing and Validation
- Multiple test directories for different test types
- Both synchronous and asynchronous testing patterns
- Integration with various Sui network environments