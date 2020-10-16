# Code base for kri-com

## RECONSTRUCTING KYI-COM

### Site install
- [x] git clone git@github.com:geerlingguy/drupal-vm.git
- [x] configure default.config.yml
- [x] vagrant up
- [x] Set up git
- [x]  set up git repo
-- [x] git init
-- create .gitignore file
-- git add README.md
-- git commit -m "first commit"
-- git branch -M main
-- git remote add origin git@github.com:kimblev/kri-com.git
-- git push -u origin main
- run "drush site:install minimal --db-url=mysql://kyreal_kyreald8:5346Hazel@localhost/kyreal_kyreald8"
- Edit D1 from assigned credentials to user from current site
- Edit settings.php
-- Set proper config_sync_directory: $settings['config_sync_directory'] = '../config/sync';
-- Add migrate db to $databases array: $databases['migrate']['default'] 
- Export and store configuration at this point

### Contrib module installation


## Random commands
- COMPOSER_MEMORY_LIMIT=-1 composer require
- drush site:install minimal --db-url=mysql://user:pass@host/db --site-name=""







