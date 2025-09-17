# Sheepdog Migration Logger Module

**Drupal 10/11 module for migration logging and monitoring**

Always reference these instructions first and fallback to search or bash commands only when you encounter unexpected information that does not match the info here.

## Working Effectively

### Initial Setup and Requirements
- **CRITICAL**: This is a Drupal 10/11 module, NOT a standalone application
- Requires PHP 8.1+ and Drupal 10.0+ or 11.0+
- Requires the Migrate API (drupal:migrate) - already part of Drupal core
- Module must be placed in a Drupal installation under `web/modules/contrib/sheepdog` or `web/modules/custom/sheepdog`

### Installing and Testing the Module
- **NEVER try to build this module standalone** - it requires a Drupal environment
- Set up a Drupal installation first:
  - `composer create-project drupal/recommended-project:^10.0 drupal_test --no-interaction` -- takes 10-15 minutes. NEVER CANCEL. Set timeout to 30+ minutes.
  - `cd drupal_test && composer require drush/drush` -- takes 3-5 minutes. NEVER CANCEL. Set timeout to 10+ minutes.
  - **NOTE**: Network access required for Composer operations. If behind firewall, installation may fail.
- Copy module: `mkdir -p web/modules/custom && cp -r /path/to/sheepdog web/modules/custom/sheepdog`
- Enable module: `vendor/bin/drush en sheepdog` (if Drush available) or via Drupal admin UI at `/admin/modules`
- **VALIDATION**: Check module status with `vendor/bin/drush pml | grep sheepdog` or admin UI

### Basic Validation Steps
- **PHP Syntax Check**: `php -l src/Drush/Commands/SheepdogCommands.php && php -l src/EventSubscriber/SheepdogEventSubscriber.php` -- Takes seconds
- **Module Info Check**: `cat sheepdog.info.yml` should show correct dependencies
- **Module Detection**: `vendor/bin/drush pml | grep sheepdog` (if Drush available) should show the module
- **Configuration Check**: `cat config/install/sheepdog.settings.yml` shows default configuration

### Testing Drush Commands (Requires Full Drupal Installation)
- **Main Help**: `vendor/bin/drush sheepdog --help` - Shows all available commands and aliases
- **Logs Help**: `vendor/bin/drush sheepdog logs --help` - Shows log-specific commands  
- **Export Examples**:
  - `vendor/bin/drush sheepdog logs:export --hours=2 --pretty` or `vendor/bin/drush sle --hours=2 --pretty`
  - `vendor/bin/drush sle --csv --output=migration-analysis.csv`
- **Analysis Examples**:
  - `vendor/bin/drush sheepdog logs:analyze --hours=1` or `vendor/bin/drush sla --hours=1`
- **Real-time Monitoring**: 
  - `vendor/bin/drush sheepdog logs:tail --json` or `vendor/bin/drush slt --json`
- **Alternative if Drush unavailable**: Use Drupal admin interface at `/admin/modules` to enable

### Validated Commands That Always Work
- **PHP Syntax**: `php -l src/Drush/Commands/SheepdogCommands.php && php -l src/EventSubscriber/SheepdogEventSubscriber.php` ✓ TESTED
- **Module Info**: `cat sheepdog.info.yml` ✓ TESTED  
- **Config Check**: `cat config/install/sheepdog.settings.yml` ✓ TESTED
- **File Structure**: `ls -la src/` and `ls -la config/install/` ✓ TESTED

### Available Drush Commands and Aliases

**Main Commands:**
- `drush sheepdog` or `drush sheepdog --help` - Main help and command overview
- `drush sheepdog logs` or `drush sheepdog logs --help` - Log-specific help

**Log Management Commands:**
- `drush sheepdog logs:export` or `drush sle` - Export logs to JSON/CSV/NDJSON
- `drush sheepdog logs:analyze` or `drush sla` - Statistical analysis of migration logs  
- `drush sheepdog logs:tail` or `drush slt` - Real-time log monitoring

**Export Options (all formats validated in source code):**
- `--json` - JSON format output
- `--ndjson` - Newline-delimited JSON (for ELK stack)
- `--csv` - CSV format for spreadsheet analysis
- `--pretty` - Pretty-formatted JSON output
- `--output=filename` - Specify output file path
- `--hours=N` - Filter logs from last N hours (default: 24)
- **NEVER CANCEL**: Drupal installation takes 10-15 minutes
- **NEVER CANCEL**: Drush installation takes 3-5 minutes  
- **NEVER CANCEL**: Module installation takes 1-2 minutes
- PHP syntax checking takes seconds
- Drush commands execute quickly (under 30 seconds) unless processing large log datasets

### Common Commands and Timing
- **NEVER CANCEL**: Drupal installation takes 10-15 minutes
- **NEVER CANCEL**: Drush installation takes 3-5 minutes  
- **NEVER CANCEL**: Module installation takes 1-2 minutes
- PHP syntax checking takes seconds
- Drush commands execute quickly (under 30 seconds) unless processing large log datasets

### After Making Code Changes
1. **Always test PHP syntax first**: `php -l` on any PHP files you modified
2. **Test module installation**: `vendor/bin/drush en sheepdog` should complete without errors
3. **Test Drush commands**: Run `vendor/bin/drush sheepdog --help` to verify commands are detected
4. **Test specific functionality**: If you modified export functionality, run `vendor/bin/drush sle --help` and test basic export

### Manual Testing Scenarios
- **Configuration Test**: Modify `config/install/sheepdog.settings.yml` and verify changes with `vendor/bin/drush config:get sheepdog.settings`
- **Event Subscriber Test**: The module hooks into migration events - test requires actual Drupal migration running
- **Log Analysis**: Test log export/analysis commands - may show empty results if no migrations have run
- **Real Migration Test**: To fully validate, you need to run actual Drupal migrations and verify Sheepdog logs them

## File Structure and Navigation

### Key Files
- `sheepdog.info.yml` - Module definition file
- `src/EventSubscriber/SheepdogEventSubscriber.php` - Core migration event logging
- `src/Drush/Commands/SheepdogCommands.php` - All Drush command implementations
- `config/install/sheepdog.settings.yml` - Default configuration
- `sheepdog.services.yml` - Service definitions

### Common File Locations
```
sheepdog/
├── sheepdog.info.yml           # Module metadata
├── sheepdog.install            # Install/uninstall hooks  
├── sheepdog.services.yml       # Service container definitions
├── config/install/             # Default configuration
│   └── sheepdog.settings.yml
└── src/
    ├── EventSubscriber/        # Migration event handling
    │   └── SheepdogEventSubscriber.php
    └── Drush/Commands/         # Command line tools
        └── SheepdogCommands.php
```

### Configuration Options
- **Logging channels**: `main_channel` (default: 'sheepdog'), `edits_channel` (default: 'sheepdog_edits')
- **Entity tracking**: Configure what entity operations to log
- **ID field mapping**: Configure how to match source data to entities
- **Performance settings**: Control logging verbosity for large migrations

## Expected Command Outputs

### PHP Syntax Check (Always run this first)
```bash
$ php -l src/Drush/Commands/SheepdogCommands.php && php -l src/EventSubscriber/SheepdogEventSubscriber.php
No syntax errors detected in src/Drush/Commands/SheepdogCommands.php
No syntax errors detected in src/EventSubscriber/SheepdogEventSubscriber.php
```

### Module Info Check
```bash
$ cat sheepdog.info.yml
name: 'Sheepdog'
type: module
description: 'Migration logging and monitoring watchdog. Keep your data migrations on track with detailed logging, real-time monitoring, and analysis tools.'
package: Migration
core_version_requirement: ^10 || ^11
dependencies:
  - drupal:migrate
```

### Configuration Check
```bash
$ cat config/install/sheepdog.settings.yml
logging:
  main_channel: 'sheepdog'
  edits_channel: 'sheepdog_edits'
  log_new_entities: true
  log_updated_entities: true
  log_unchanged_entities: true
  log_skipped_rows: false
tracking:
  id_fields:
    - 'id'
    - 'source_id'
    - 'ID10'
  primary_id_field: ''
  entity_label_field: ''
monitoring:
  enable_real_time: false
  log_level: 'info'
```

### Adding New Drush Commands
- Edit `src/Drush/Commands/SheepdogCommands.php`
- Use `#[CLI\Command(name: 'sheepdog your:command')]` attribute
- Follow existing pattern with aliases like `[aliases: ['alias']]`
- Always add usage examples with `#[CLI\Usage()]`

### Modifying Event Subscriber
- Edit `src/EventSubscriber/SheepdogEventSubscriber.php` 
- Core logic is in `preRowSave()` and `postRowSave()` methods
- **After changes to event subscriber**: Test with `vendor/bin/drush en sheepdog` to verify no service container errors

### Configuration Changes
- Modify `config/install/sheepdog.settings.yml` for defaults
- **After config changes**: Use `vendor/bin/drush config:import` if testing configuration updates
- Test with: `vendor/bin/drush config:get sheepdog.settings`

### When Working on Log Analysis
- The module logs to Drupal's watchdog system
- Test log viewing: `vendor/bin/drush watchdog:show sheepdog`
- Export formats: JSON, NDJSON, CSV via boolean flags

## Troubleshooting

### Module Won't Enable
- Check `vendor/bin/drush pml` shows module in correct location
- Verify `sheepdog.info.yml` has correct dependencies: `drupal:migrate`
- Check PHP syntax: `php -l src/EventSubscriber/SheepdogEventSubscriber.php`

### Drush Commands Not Found  
- Module must be enabled: `vendor/bin/drush en sheepdog`
- Clear Drush cache: `vendor/bin/drush cache:rebuild`
- Check command registration in `SheepdogCommands.php`

### No Migration Logs
- Module only logs when actual Drupal migrations run
- Check configuration: `vendor/bin/drush config:get sheepdog.settings`
- Verify migrate module enabled: `vendor/bin/drush pml | grep migrate`

## Important Development Notes

- **DO NOT attempt to run this as a standalone PHP application** - it's a Drupal module
- **DO NOT try to use npm, webpack, or other frontend build tools** - this is server-side only
- **ALWAYS work within a Drupal installation context**
- **Module follows Drupal coding standards** - use proper dependency injection and service patterns
- **Configuration follows Drupal Configuration API** - use config entities, not direct file editing
- **All timing depends on having network access** for Composer operations

## Dependencies and Architecture
- Built on Symfony EventDispatcher for migration events
- Uses Drupal's Configuration API for settings
- Integrates with Drupal's logging system (watchdog)
- Drush integration via modern attributes-based command definition
- No frontend dependencies - purely backend/CLI functionality