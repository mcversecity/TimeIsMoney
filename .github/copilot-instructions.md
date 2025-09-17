# TimeIsMoney AI Instructions

This guide helps AI coding agents understand and work with the TimeIsMoney Spigot plugin codebase.

## Project Overview

TimeIsMoney is a Spigot plugin that rewards players with in-game currency based on their online time. Key features:

- Configurable time-based payouts with permission levels
- AFK detection and customizable AFK payout rates
- ATM system for banking earned money
- Multi-world support with configurable disabled worlds
- MySQL/YAML data storage options
- PlaceholderAPI integration

## Core Architecture

### Main Components

- `Main.java`: Core plugin class handling initialization and payout logic
- `Payout.java`: Represents a single payout configuration
- `data/PluginData.java`: Abstract data storage interface
  - `MySQLPluginData.java`: MySQL implementation
  - `YamlPluginData.java`: YAML file implementation
- `ATM.java`: Handles ATM sign functionality and bank operations

### Key Patterns

1. **Payout Processing Flow**:

   ```java
   // Main.java handles payout timing and validation
   startPlaytimeWatcher() -> getApplicablePayoutsForPlayer() -> pay()
   ```

2. **Data Layer Abstraction**:
   ```java
   // Always use PluginData interface for data operations
   PluginData pluginData = config.contains("mysql")
     ? new MySQLPluginData(...)
     : new YamlPluginData(this);
   ```

## Development Workflows

### Building

- Maven-based project structure
- Build the main plugin and tools modules:

```powershell
mvn clean package
```

### Configuration

1. **Payout Definition**:

```yaml
payouts:
  1: # Unique payout ID
    payout_amount: 50
    max_payout_per_day: 1000
    permission: "" # Empty for all players
    interval: "10m" # Optional custom interval
```

2. **ATM Setup**:

```yaml
enable_atm: true
atm_sign_label: "&cATM"
store-money-in-bank: true # Enable bank storage
```

### Integration Points

1. **Vault Economy**:

   - Required for all money transactions
   - Configured in `setupEconomy()`

2. **PlaceholderAPI**:

   - Optional integration via `NamePlaceholder`
   - Auto-registers if PlaceholderAPI is present

3. **Essentials**:
   - Optional AFK detection integration
   - Configured via `afk_use_essentials: true`

## Common Tasks

### Adding New Payout Features

1. Add configuration options in `config.yml`
2. Update `Payout.java` with new fields
3. Modify `loadPayouts()` in `Main.java` to read new config
4. Update `pay()` method to handle new payout logic

### Modifying Storage

1. Create new implementation of `PluginData` interface
2. Implement required methods for player data management
3. Add configuration for new storage type
4. Update `onEnable()` to initialize new storage

## Project-Specific Conventions

1. **Message Formatting**:

   - Use `Utils.CC()` for color code processing
   - Support both `&` color codes and hex RGB (`#RRGGBB`)

2. **Config Version Management**:

   ```java
   // Main.java enforces config updates
   private static final int CFG_VERSION = 12;
   ```

3. **Commands and Permissions**:

   - Prefix permissions with `tim.` (e.g., `tim.afkbypass`)
   - Command handlers in `Cmd.java`

4. **Error Handling**:
   - Use plugin logger for errors/debug: `logger.warning()`, `logger.info()`
   - Enable debug logging via `debug-log: true` in config

## Best Practices

1. Always validate economy provider availability before money operations
2. Use the scheduler wrapper for cross-version compatibility
3. Prefer configuration-based features over hardcoded values
4. Handle AFK states consistently using configured detection method

## Common Pitfalls

1. Avoid direct economy manipulation - always use Vault API
2. Don't modify payout data directly - use `PluginData` interface
3. Check world blacklist before processing payouts
4. Consider multi-account restrictions when implementing features
