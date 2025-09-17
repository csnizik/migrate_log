# Changelog

All notable changes to the Migrate Log module will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [2.0.0] - 2024-12-17

### Changed
- **BREAKING**: Renamed module from "Sheepdog" to "Migrate Log" for more general-purpose use
- Updated module machine name from `sheepdog` to `migrate_log`
- Updated all namespaces from `Drupal\sheepdog` to `Drupal\migrate_log`
- Updated class names: `SheepdogCommands` → `MigrateLogCommands`, `SheepdogEventSubscriber` → `MigrateLogEventSubscriber`
- Updated Drush command names: `drush sheepdog` → `drush migrate-log`
- Updated command aliases: `sle,sla,slt` → `mle,mla,mlt`
- Updated default log channels: `sheepdog/sheepdog_edits` → `migrate_log/migrate_log_edits`
- Updated configuration keys: `sheepdog.settings` → `migrate_log.settings`
- Removed all dog emojis and dog-related language from code and documentation
- Updated documentation URLs and references
- Updated installation instructions and examples

## [1.0.0] - 2024-01-15

### Added
- Initial release of Migrate Log migration monitoring module
- Event-driven migration logging with detailed change tracking
- Configurable ID field mapping for flexible entity matching
- Support for any Drupal entity type and migration source
- Separate log channels for general events and detailed edits
- Real-time migration monitoring capabilities
- Comprehensive Drush commands for log analysis:
  - `migrate-log logs:export` - Export logs in JSON, NDJSON, or CSV formats
  - `migrate-log logs:analyze` - Statistical analysis of migration performance
  - `migrate-log logs:tail` - Real-time log monitoring
- ELK stack compatible structured logging output
- Configurable logging levels and field tracking
- Skip detection for empty/invalid source rows
- Migration-agnostic design works with any migration module
- Comprehensive documentation and README
- Installation hooks with status messages
- Requirements checking for dependencies

### Features
- **Smart Entity Matching**: Multiple strategies for finding existing entities
- **Detailed Change Tracking**: Field-by-field diff generation
- **Performance Optimizations**: Memory management and lazy loading
- **Flexible Configuration**: Fine-grained control over logging behavior
- **Developer Friendly**: Easy to extend and customize
- **Production Ready**: Comprehensive error handling and logging

### Technical
- Compatible with Drupal 10.0+ and 11.0+
- Requires PHP 8.1+
- Full Drupal coding standards compliance
- Comprehensive PHPDoc documentation
- Event subscriber architecture for migration hooks
- Service-based dependency injection
- Configuration API integration