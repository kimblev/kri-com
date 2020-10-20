# Code base for kri-com

## RECONSTRUCTING KYI-COM

### D7 Preparation
- [x] Install upgrade_status module which will add a tab under Reports > Available updates which will report upgrade status of modules installed
- [x] Update all modules necessary paying particular attention modules highlighted in yellow which require a special dev version.
- [x]Sync text formats on fields that use different text type on different content types. D8 will not accept fileds that have multiple text formats assigned to them
- [x] Disable Registration module. There is no viable Registration module for D8 so it causes migration errors. Disable and un-install!
- {x} Disable commerce_coupon. It also causes errors. Disable and un-install!

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
- [x] I had to reinstall site because I had problems with minimal install profile. *drush site:install standard --account-name="" --account-pass=""*

## Migration
    First configuration, then content.

### Configuration migration
- [x] Start new git branch
- [x] Add *ini_set('memory_limit', '-1');* to settings.php to prevent memory errors
- [x] Run *migrate-status* to see what's there and what isn't
- [x] Run *migrate-upgrade* to build migrate_drupal_7 migration group
- [x] Export and store migrate_drupal_7 migration group
- [x] Run drush mfs on product related migrations to review source fields we are dealing with
- [x] For now commerce_shipping is required due to a bug in commerce_migrate_commerce
- [x] Disable core actions module. There are no compatible actions in D7 site.
- [x] Disable comments module

#### Product configuration migration
- [x] Review each product type and the differences in their fields
- [] Determine how to handle the checkbox fields
- [] Determine where the 3 items come from in *upgrade_commerce1_product_attribute* migration
- [x] Install this patch: https://www.drupal.org/files/issues/2020-09-12/commerce_migrate-n3157708-27.patch, which gets rid of commerce1 dependencies. I tried unsuccessfully to add the patch in composer, ended up adding it manually.
- [x] Run `drush migrate-upgrade --legacy-db-key=migrate --legacy-root=kyi7.vardaman.com --configure-only`
**HERE I FINALLY HAVE AN ERROR FREE MIGRATION GROUP WITHOUT THE commerce1 DEPENDENCY ISSUE.**
- [x] Run `drush ms --group=migrate_drupal_7 --tag=Configuration` to check for errors
- [x] Run `drush migrate:import --group=migrate_drupal_7 --tag=Configuration --execute-dependencies`
- [x] Run `drush ms --group=migrate_drupal_7 --tag=Configuration`


##### Trial run
-  Beginning by creating product type broker_education (this creates a product variation type of the same name but the reverse is not true
- I will then switch to the created variation type to add my fields...that just seems to be where they should be looking at the migation config files. migrate_plus.migration.upgrade_commerce1_product_type has "injectVariationFields" where upgrade_commerce1_product_variation_broker_education actually has all the custom fields)
- **Is product status replaced by 'published'?**


## Random commands
- COMPOSER_MEMORY_LIMIT=-1 composer require
- COMPOSER_PROCESS_TIMEOUT=2000 composer update
- drush site:install minimal --db-url=mysql://user:pass@host/db --site-name=""
- drush site:install standard --account-name="" --account-pass="" --existing-config
- 
- drush migrate-upgrade --legacy-db-url=mysql://kvardaman:pass@12.34.56.78/d6db --legacy-root=http://myd6site.com

- drush migrate-upgrade --legacy-db-key=migrate --legacy-root=kyi7.vardaman.com --configure-only
- drush migrate-import --all
- drush ms --group=migrate_drupal_7
- git commit --amend --author="Kimble <kimble@vardaman.com>"
- git commit --amend --reset-author
- `patch -b < c_m_c.patch`
- drush config-get "system.site" uuid
drush config-set "system.site" uuid "d802cba4-8354-46f2-89a6-faef5fd1ff7f"


## New resource

https://www.lullabot.com/articles/overview-migrating-drupal-sites-8
- drush migrate:import --group=migrate_drupal_7 --tag=Configuration --execute-dependencies
- drush migrate:import --tag=Content --execute-dependencies
- drush migrate:status | grep article

        "patches": {
            "drupal/commerce_migrate_commerce": {
                "Invalid dependencies when using Migrate Upgrade": "https://www.drupal.org/files/issues/2019-04-17/recaptcha-ajax-2493183-172.patch"
            }
        },
        "enable-patching": true,

## Remove and repeat a migration
- Rollback drush migrate:rollback --group=migrate_drupal_7 --tag=Configuration
- Delete migration group in migrate manager
- drush cex -y
- Commit
- drush cr
- Check status: drush ms --group=migrate_drupal_7 --tag=Configuration
**Now the old migration is gone - begin new one**
- drush migrate-upgrade --legacy-db-key=migrate --legacy-root=http://kyi7.vardaman.com --configure-only
- Check status: drush ms --group=migrate_drupal_7 --tag=Configuration
- drush cst //check differences in case something does not belong
- drush cex -y
- Remove these migration files:
-- adminimal.settings.yml
-- kyrealtors.settings.yml
-- migrate_plus.migration.upgrade_action_settings
-- migrate_plus.migration.upgrade_commerce1_shipping_flat_rate
-- migrate_plus.migration.upgrade_d7_action
-- migrate_plus.migration.upgrade_d7_comment* // All comment migration files
-- ?? Product displays ??
- drush cim -y
- Check status: drush ms --group=migrate_drupal_7 --tag=Configuration
- Commit
- drush migrate:import --group=migrate_drupal_7 --tag=Configuration --execute-dependencies


I had to use this to fnd the offending entities:
SELECT *
FROM `field_config_instance`
WHERE (`entity_type` LIKE '%registration%')
ORDER BY `entity_type`
LIMIT 50

And this to get rid of them:
DELETE 
FROM `field_config_instance`
WHERE (`entity_type` LIKE '%registration%')
ORDER BY `entity_type`


