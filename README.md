# Code base for kri-com

## RECONSTRUCTING KYI-COM

### D7 Preparation
- [x] Install upgrade_status module which will add a tab under Reports > Available updates which will report upgrade status of modules installed
- [x] Update all modules necessary paying particular attention modules highlighted in yellow which require a special dev version.

### New D8 Site install
- [x] git clone git@github.com:geerlingguy/drupal-vm.git
- [x] configure default.config.yml
- [x] vagrant up
- [x] Set up git
    - set up git repo
    - git init
    - create .gitignore file
    - git add README.md
    - git commit -m "first commit"
    - git branch -M main
    - git remote add origin git@github.com:kimblev/kri-com.git
    - git push -u origin main
- [x] run "drush site:install standard --account-name="" --account-pass="" --db-url=mysql://kyreal_kyreald8:5346Hazel@localhost/kyreal_kyreald8" **Important: minimal install profile turned out badly: no admin user, no text formats**
- [x] Edit settings.php
    - Set proper config_sync_directory: $settings['config_sync_directory'] = '../config/sync';
    - Add migrate db to $databases array: $databases['migrate']['default'] 
- [x] Export and store configuration at this point
- [x] Set up D7 site database
    - My ISP seems to block connection to a DB on the internet so I set up a copy on localhost using port 3307
    - Download DB from live lite with Backup Migrate
    - upload to D7 copy at kyi7.vardaman.com
    - Remove extra product reference from Product Displays
    - Download that DB and upload to DB in localhost

### Contrib module installation
- [x] Download necessary modules per module audit via composer require
- [x] Sync activated/installed modules with D7 site
- [x] Export and store configuration
- [] I had to reinstall site because I had problems with minimal install profile. *drush site:install standard --account-name="" --account-pass=""*

## Migration
    First configuration, then content.

### Configuration migration
- [x] Start new git branch
- [x] Add *ini_set('memory_limit', '-1');* to seetings.php to prevent memory errors
- [x] Run *migrate-status* to see what's there and what isn't
- [x] Run *migrate-upgrade* to build migrate_drupal_7 migration group
- [x] Export and store migrate_drupal_7 migration group
- [x] Run drush mfs on product related migrations to review source fields we are dealing with
- [x] Remove commerce_shipping frmo required module list. That was an error.

#### Product configuration migration
- [x] Review each product type and the differences in their fields
- [] Determine how to handle the checkbox fields
- [] Determine where the 3 items come from in *upgrade_commerce1_product_attribute* migration

##### Trial run
-  Beginning by creating product type broker_education (this creates a product variation type of the same name but the reverse is not true
- I will then switch to the created variation type to add my fields...that just seems to be where they should be looking at the migation config files. migrate_plus.migration.upgrade_commerce1_product_type has "injectVariationFields" where upgrade_commerce1_product_variation_broker_education actually has all the custom fields)
- **Is product status replaced by 'published'?**


## Random commands
- COMPOSER_MEMORY_LIMIT=-1 composer require
- drush site:install minimal --db-url=mysql://user:pass@host/db --site-name=""
drush site:install standard --account-name="" --account-pass="" --existing-config
- 
- drush migrate-upgrade --legacy-db-url=mysql://kvardaman:pass@12.34.56.78/d6db --legacy-root=http://myd6site.com

- drush migrate-upgrade --legacy-db-key=migrate --legacy-root=kyi7.vardaman.com --configure-only
- drush migrate-import --all
- git commit --amend --author="Kimble <kimble@vardaman.com>"

## New resource

https://www.lullabot.com/articles/overview-migrating-drupal-sites-8
- drush migrate:import --tag=Configuration --execute-dependencies
- drush migrate:import --tag=Content --execute-dependencies
- drush migrate:status | grep article




