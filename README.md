# Migrate Log

**Migration logging and monitoring tool for Drupal**

Keep your data migrations on track with detailed logging, real-time monitoring, and comprehensive analysis tools. This module monitors your migrations and keeps everything organized.

## Features

📋 **Smart Migration Tracking**
- Automatic detection of entity changes during migration
- Detailed field-by-field diff logging
- Configurable ID field mapping for flexible entity matching
- Support for any entity type and migration source

📊 **Comprehensive Logging**
- Separate log channels for general events and detailed edits
- Configurable logging levels and options
- Real-time migration monitoring
- Skip detection for empty/invalid source rows

🔧 **Powerful Drush Commands**
- Export logs to JSON, NDJSON, or CSV formats
- Real-time log tailing with pretty formatting
- Statistical analysis of migration performance
- ELK stack compatible output for enterprise monitoring

⚙️ **Highly Configurable**
- Customizable log channels and field mappings
- Fine-grained control over what gets logged
- Designed to work with any migration

## Requirements

- Drupal 10.0+ or Drupal 11.0+
- PHP 8.1+
- Migrate API (core)
- Drush 12+ (for command functionality)

## Installation

### Via Composer (Recommended for contrib use)

```bash
composer require drupal/migrate_log
drush en migrate_log
```

### Manual Installation

1. Download and extract to `web/modules/contrib/migrate_log`
2. Enable the module: `drush en migrate_log`

## Configuration

After installation, configure Migrate Log at `admin/config/development/migrate_log` or by editing the configuration:

```yaml
# config/sync/migrate_log.settings.yml
logging:
  main_channel: 'migrate_log'
  edits_channel: 'migrate_log_edits'
  log_new_entities: true
  log_updated_entities: true
  log_unchanged_entities: true
  log_skipped_rows: false
tracking:
  id_fields:
    - 'id'
    - 'source_id'
    - 'ID10'
  primary_id_field: 'ID10'  # Leave empty for auto-detection
  entity_label_field: 'field_s_id10'  # Field to use for entity lookups
monitoring:
  enable_real_time: false
  log_level: 'info'
```

### Key Configuration Options

**Logging Settings:**
- `main_channel`: Primary log channel name
- `edits_channel`: Separate channel for detailed change logs
- `log_*_entities`: Control what operations get logged

**Tracking Settings:**
- `id_fields`: List of source fields to check for entity IDs (in priority order)
- `primary_id_field`: Specific field to use as primary identifier
- `entity_label_field`: Field name in destination entities for ID lookups

## Usage

### Automatic Migration Logging

Once enabled, Migrate Log automatically logs all migration activity. It will:

1. **Pre-Migration**: Capture current entity state before changes
2. **Post-Migration**: Compare and log detailed field changes
3. **Smart Matching**: Use configured ID fields to find existing entities
4. **Flexible Logging**: Log to separate channels based on operation type

### Drush Commands

#### Export Migration Logs

```bash
# Export all logs from last 24 hours
drush migrate-log logs:export

# Export specific timeframe with pretty formatting
drush mle --hours=2 --update --pretty

# Export to CSV for spreadsheet analysis
drush mle --csv --output=migration-analysis.csv

# Export for ELK stack ingestion
drush mle --ndjson --output=logs-for-elk.ndjson
```

#### Analyze Migration Performance

```bash
# Quick analysis of recent migrations
drush migrate-log logs:analyze

# Analyze specific timeframe
drush mla --hours=1

# Focus on specific migration
drush mla --migration=my_content_migration
```

#### Real-Time Log Monitoring

```bash
# Pretty formatted real-time logs
drush migrate-log logs:tail

# JSON output for processing
drush mlt --json
```

### Log Structure

Migrate Log creates structured log entries compatible with modern log analysis tools:

```json
{
  "@timestamp": "2024-01-15T10:30:45+00:00",
  "level": "info",
  "message": "Migrate Log: Updated entity 123 for my_migration. Source: id:456",
  "migration": {
    "id": "my_migration",
    "primary_id": "456"
  },
  "entity": {
    "id": 123,
    "type": "node"
  },
  "operation": "update",
  "diff": {
    "fields": [
      {
        "field": "title",
        "operation": "changed",
        "value": "Old Title → New Title"
      }
    ]
  }
}
```

## Migration Integration

Migrate Log works automatically with any Drupal migration. For optimal results:

### Source Configuration

Ensure your migration source defines ID fields clearly:

```yaml
source:
  plugin: csv
  path: 'data.csv'
  ids:
    source_id: { type: string }  # Primary identifier
    secondary_id: { type: string }  # Optional secondary ID
```

### Entity Mapping

For content entity migrations, Migrate Log will automatically detect:
- Entity type from destination plugin
- ID fields from source configuration
- Field changes through entity comparison

## Troubleshooting

### Common Issues

**No logs appearing:**
- Verify the module is enabled: `drush pml | grep migrate_log`
- Check log channels: `drush watchdog:show migrate_log`
- Review configuration: `drush config:get migrate_log.settings`

**Entity matching issues:**
- Configure `entity_label_field` to match your entity structure
- Add relevant ID fields to `tracking.id_fields`
- Check source data has valid ID values

**Performance concerns:**
- Disable `log_unchanged_entities` for high-volume migrations
- Use `log_skipped_rows: false` to reduce noise
- Consider separate log channels for different migration types

### Debug Mode

Enable verbose logging for troubleshooting:

```bash
drush config:set migrate_log.settings monitoring.log_level debug
drush config:set migrate_log.settings logging.log_skipped_rows true
```

## Development

### Contributing

Migrate Log welcomes contributions! Please:

1. Fork the repository
2. Create a feature branch
3. Follow Drupal coding standards
4. Include tests for new functionality
5. Submit a pull request

### Extending Migrate Log

#### Custom Event Subscribers

Create custom migration logging by extending the event subscriber:

```php
use Drupal\migrate_log\EventSubscriber\MigrateLogEventSubscriber;

class MyCustomSubscriber extends MigrateLogEventSubscriber {

  protected function extractEntityValues(ContentEntityInterface $entity): array {
    $values = parent::extractEntityValues($entity);

    // Add custom field processing
    if ($entity->hasField('my_custom_field')) {
      $values['my_custom_field'] = $this->processCustomField($entity->get('my_custom_field'));
    }

    return $values;
  }
}
```

#### Custom Drush Commands

Extend the Drush commands for project-specific analysis:

```php
use Drupal\migrate_log\Drush\Commands\MigrateLogCommands;

class MyProjectMigrateLogCommands extends MigrateLogCommands {

  #[CLI\Command(name: 'myproject:migration:validate')]
  public function validateMigrationData(): void {
    // Custom validation logic using Migrate Log's log analysis
  }
}
```

## Architecture

### Event-Driven Design

Migrate Log uses Drupal's event system to hook into migration processes:

- `MigrateEvents::PRE_ROW_SAVE`: Capture entity state before changes
- `MigrateEvents::POST_ROW_SAVE`: Compare and log changes after processing

### Configurable Components

- **Logger Channels**: Separate logging streams for different purposes
- **ID Field Mapping**: Flexible source-to-entity ID resolution
- **Entity Matching**: Configurable strategies for finding existing entities

### Performance Considerations

- **Lazy Loading**: Entity lookups only when needed
- **Memory Management**: Cleanup of stored entity states after processing
- **Configurable Logging**: Fine-grained control over what gets logged

## License

GPL-2.0-or-later

## Maintainers

- Original Author: [Your Name]
- Current Maintainer: [Maintainer Name]

## Support

- Issue Queue: https://www.drupal.org/project/migrate_log/issues
- Documentation: https://www.drupal.org/docs/contributed-modules/migrate_log
- Community Support: #migrate channel in Drupal Slack

---

*Keep those migrations organized and on track!*
