# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

PySui is a Python SDK for the Sui blockchain, providing clients for JSON-RPC, GraphQL, and gRPC interfaces. The SDK supports both synchronous and asynchronous operations across multiple network environments (mainnet, testnet, devnet, localnet).

## Development Commands

### Testing

```bash
# Run all tests (configured in pyproject.toml)
pytest

# Run with verbose output
pytest -v

# Run specific test categories
pytest tests/sync_tests/
pytest tests/async_tests/

# Run specific test files
pytest tests/sync_tests/test_argprep_sync.py
pytest tests/async_tests/test_argprep_async.py

# Run with coverage
pytest --cov=pysui
pytest --cov=pysui --cov-report=html
```

### Building

```bash
# Build package using build script (includes validation)
./bin/package-build.sh

# Build package directly
python -m build . --wheel

# Install in development mode
pip install -e .

# Install from requirements.txt
pip install -r requirements.txt

# Install with Rust support (requires Rust on machine)
pip install pysui
```

### Publishing

```bash
# Build and validate package
./bin/package-build.sh

# Publish to PyPI
./bin/package-publish.sh

# Publish to TestPyPI
./bin/package-publish-test.sh
```

### Code Quality

Note: No formal linting or type checking is currently configured in the project. The following tools are commonly used but would need setup:

```bash
# Type checking (if mypy is added)
mypy pysui/

# Linting (if flake8 or similar is added)
flake8 pysui/
```

## Architecture

### Core Components

**Abstract Layer:**
- `pysui/abstracts/` - Abstract base classes and interfaces
  - `client_config.py` - Abstract configuration classes
  - `client_keypair.py` - Abstract keypair interfaces
  - `client_rpc.py` - Abstract RPC interfaces
  - `client_types.py` - Abstract type definitions

**Client Architecture:**
- `pysui/sui/sui_clients/` - Core client implementations
  - `sync_client.py` - Synchronous JSON-RPC client
  - `async_client.py` - Asynchronous JSON-RPC client
  - `common.py` - Shared client utilities and result handling

**Protocol Support:**
- `pysui/sui/sui_pgql/` - GraphQL client implementation
- `pysui/sui/sui_grpc/` - gRPC client implementation with protobuf generated code

**Configuration:**
- `pysui/sui/sui_config.py` - Main configuration class for network settings
- `pysui/sui/sui_common/config/` - Configuration management and legacy support

**Transaction Building:**
- `pysui/sui/sui_txn/` - Transaction construction and signing
- `pysui/sui/sui_txn_validator.py` - Transaction validation utilities
- `pysui/sui/sui_builders/` - High-level builders for common operations

**Data Types:**
- `pysui/sui/sui_types/` - Sui-specific data types, scalars, address handling
- `pysui/sui/sui_bcs/` - Binary Canonical Serialization implementation
- `pysui/sui/sui_txresults/` - Transaction result handling

### Key Patterns

**Client Configuration:**
- Uses `SuiConfig` for basic configuration
- Uses `PysuiConfiguration` for advanced configuration (recommended)
- Supports multiple network environments through configuration groups
- Integrates with suibase for local node management

**Result Handling:**
- All client operations return `SuiRpcResult` objects
- Use `handle_result()` helper for processing results
- Results include both data and error information

**Transaction Flow:**
1. Create transaction using builders or direct construction
2. Sign transaction using appropriate keypair
3. Execute transaction and handle results
4. Process transaction effects and status

**Async/Sync Parity:**
- Most functionality available in both sync and async versions
- Async clients follow Python async/await patterns
- Sync clients provide blocking operations

## Configuration

### Environment Variables

- `PYSUI_CLIENT_CONFIG_ENV` - Path to configuration file
- `PYSUI_EXEC_ENV` - Execution environment

### Network Support

- **Mainnet**: Production network
- **Testnet**: Testing network with faucet
- **Devnet**: Development network with faucet
- **Localnet**: Local development via suibase integration

### Configuration Files

- Supports YAML configuration files
- Legacy `.sui/sui_config` support
- Multiple profile support within single configuration

## Protocol Specifics

### GraphQL (Alpha)

- Location: `pysui/sui/sui_pgql/`
- Supports queries and mutations
- No subscription support currently
- Examples in `pgql_s_example.py`, `pgql_a_example.py`

### gRPC (Beta)

- Location: `pysui/sui/sui_grpc/`
- Supports queries, programmable transactions, subscriptions and transaction execution
- Requires `pysui-fastcrypto` dependency
- Examples in `pgrpc_a_example.py`

### JSON-RPC

- Original protocol support
- Both sync and async clients available
- Most mature implementation

## Testing

### Test Structure

- `tests/sync_tests/` - Synchronous client tests
- `tests/async_tests/` - Asynchronous client tests
- `tests/test_utils.py` - Shared test utilities
- Test paths configured in `pyproject.toml`: `["tests", "tests/sync_tests", "tests/integration_sync"]`

### Test Requirements

- Tests require network access to Sui nodes
- Configuration files needed for integration tests
- Some tests may require faucet access for testnet/devnet

## Dependencies

### Core Dependencies

From `pyproject.toml` and `requirements.txt`:
- `betterproto2[grpclib] < 1.0.0, >= 0.9.0` - Protocol buffer support with gRPC
- `dataclasses_json < 0.7.0, >= 0.6.6` - JSON serialization for dataclasses
- `PyYAML < 6.2, >= 6.0.1` - YAML configuration support
- `httpx < 0.29, >=0.28.1` - HTTP client for RPC calls
- `h2 < 5.0, >= 4.2.0` - HTTP/2 support
- `jsonschema < 4.30, >= 4.23.0` - JSON schema validation
- `websockets < 16.0.0, >=15.0.1` - WebSocket support for subscriptions
- `gql[httpx,websockets] >= 4.0.0` - GraphQL client library
- `pysui-fastcrypto >= 0.7.0` - Cryptographic operations
- `canoser==0.8.2` - Canonical serialization
- `base58 < 2.2.0, >=2.1.1` - Base58 encoding/decoding
- `typing_utils < 0.2.0, >=0.1.0` - Type utilities
- `Deprecated < 1.3.0, >=1.2.14` - Deprecation utilities

### Optional Dependencies

- Rust toolchain (for some cryptographic operations)
- suibase (for local node support)

### Build Dependencies

- `setuptools>=61.0`, `wheel`, `setuptools-scm>=8.0` - Build system
- `twine` - Package publishing (used by build scripts)

## Development Notes

### Version Support

- Requires Python 3.10+
- Tested on Python 3.10, 3.11, 3.12, 3.13
- Follows semantic versioning

### Code Style

- Follows PEP 8 guidelines
- Type hints used throughout
- Docstrings following Google style

### Sample Scripts

The project includes several sample utility scripts (available as command-line tools via pyproject.toml):

- `wallet` - Wallet management operations (`samples.walletg:main`)
- `async-gas` - Asynchronous gas management (`samples.async_gasg:main`)
- `sgqls` - GraphQL schema utilities (`samples.sgqls:main`)
- `smash` - Sui management and operations (`samples.smash:main`)
- `splay` - Sui playback utilities (`samples.splay:main`)
- `jtobcs` - JSON to BCS conversion (`samples.jtobcs:main`)
- `bcstoj` - BCS to JSON conversion (`samples.bcstoj:main`)
- `mtobcs` - Move to BCS conversion (`samples.mtobcs:main`)

These scripts are located in the `samples/` directory and serve as practical examples of SDK usage. Most use GraphQL for implementation.

### Key Files to Understand

- `pyproject.toml` - Project configuration, dependencies, and script definitions
- `pysui/__init__.py` - Main package imports and convenience exports
- `pysui/sui/sui_config.py` - Configuration management
- `pysui/sui/sui_clients/common.py` - Result handling patterns
- `pysui/abstracts/` - Abstract base classes and interfaces
- `pysui/sui/sui_constants.py` - Sui network constants and defaults
- `pysui/sui/sui_txn_validator.py` - Transaction validation
- `requirements.txt` - Runtime dependencies (should match pyproject.toml)
- `samples/` - Sample utility scripts and examples
- `bin/` - Build and publishing scripts