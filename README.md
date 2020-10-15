# Code base for kri-com

## RECONSTRUCTING KYI-COM
- git clone git@github.com:geerlingguy/drupal-vm.git
- configure default.config.yml
- vagrant up

- Set up git
-- set up git repo
-- git init
-- create .gitignore file
-- git add README.md
-- git commit -m "first commit"
-- git branch -M main
-- git remote add origin git@github.com:kimblev/kri-com.git
-- git push -u origin main
- run "drush site:install minimal --db-url=mysql://kyreal_kyreald8:5346Hazel@localhost/kyreal_kyreald8"


composer create-project drupal/recommended-project --prefer-dist --stability dev --no-interaction

## Random commands
- run "COMPOSER_MEMORY_LIMIT=-1 composer require "
