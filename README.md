## Shopware 6 CLI
### Plesk Server Information
```bash
/opt/plesk/php/7.4/bin/php -d memory_limit=-1 bin/console
```

```
### Database Migrations
```bash
database:create-migration -p pluginname [--name examplename]
```
```bash
database:migrate [pluginname] [--all]
```
```bash
database:migrate-destructive [pluginname] [--all]
```
### Messenger
```bash
messenger:consume
```
### Tasks
```bash
scheduled-task:run
```
### Refresh SEO Index
```bash
dal:refresh:index
```
### Generate Media Thumbnails
First of all you need to activate the different thumbnail sizes for your media folders.
After that you need to generate those thumbnails via a command
```bash
media:generate-thumbnails
```
If you change the thumbnail sizes or add new ones you need to clear the media_thumbnail table.
Shopware doesnt check for differences in the thumbnails, just for the first addition

## Shopware 6 Environment
### .htaccess
#### Location of the .htaccess and password file
```bash 
shopware_root/public
```
#### How to force disable pwd protection
```bash
satisfy Any
```
### MessageQueue
The table "dead_message" denies some tasks in the messagequeue to get scheduled. You have to remove the dead message and reset the task manually to scheduled again.
### Switch PHP Version for php cli
```bash
mkdir ~/bin && cd ~/bin
```
```bash
ln -s /usr/local/php8.0/bin/php php
```
```bash
echo 'PATH=$HOME/bin:$PATH' >> ~/.bash_profile
```
```bash
source ~/.bash_profile
```
### Plugin Snippets
If you want to add or change snippets in your plugin, you simply have to edit the snippet json file in
  - ```plugin/src/Resources/snippet/de_DE```

If you added new snippets or changed some, you have to execute: 
  - ```bin/console plugin:update pluginname --clearCache```
  - ```bin/console theme:compile```

## Shopware 6 Events
### Common Shopware event directories
```bash
vendor/shopware/core/Checkout/ModuleName/ModuleNameEvents.php
```
```bash
vendor/shopware/core/Content/ModuleName/ModuleNameEvents.php
```
### EventSubscription
```php
<?php declare(strict_types=1);

namespace Conne\PluginName\Subscriber;

use Symfony\Component\EventDispatcher\EventSubscriberInterface;
use Shopware\Core\Framework\DataAbstractionLayer\Event\EntityWrittenEvent;
use Shopware\Core\Framework\DataAbstractionLayer\Event\EntityLoadedEvent;
use Conne\PluginName\Service\ServiceName;

class SubscriberName implements EventSubscriberInterface
{
    // Don't forget the dependency injection in the services.xml
    private ServiceName $serviceName;

    public function __construct(ServiceName $serviceName)
    {
        $this->serviceName = $serviceName;
    }

    public static function getSubscribedEvents(): array  
    {
        return [  
            ProductEvents::PRODUCT_TRANSLATION_WRITTEN_EVENT => 'onProductTranslationWritten',
            ProductEvents::PRODUCT_TRANSLATION_LOADED_EVENT  => 'onProductTranslationLoaded',
        ];  
    }

    public function onProductTranslationWritten(EntityWrittenEvent $event): void
    {
        // Call injected custom service on event written
        $this->serviceName->method($param);
    }

    public function onProductTranslationLoaded(EntityLoadedEvent $event): void
    {
        // Call injected custom service on event loaded
        $this->serviceName->method($param);
    }
}
```

## SW6ComposerPatches
### How to work with composer patches and diff command

#### Create diff file (compare with base file)

If you want to create a diff file for a non existent file simply do ```diff -uN```

```sh
diff -u file/path/withoutChanges.js file/path/withChanges.diff.js > src/patches/vendorname/modulename/describing_name.diff
```
## Shopware 6 Compiling
### Compile Administration
```bash
docker machine > bin/console plugin:refresh
docker machine > bin/console plugin:install PLUGINNAME
docker machine > bin/console plugin:activate PLUGINNAME

docker machine > copy changed AdministrationJS files to machine
docker machine > ./bin/build-administration.sh

docker machine > copy compiled minified AdministrationJS to server
location       > plugindir/src/Resources/public/administration/js/full-plugin-name.js

remote server  > bin/console plugin:update PLUGINNAME
remote server  > bin/console theme:compile
remote server  > bin/console cache:clear
```

### Compile Storefront
```bash
docker machine > bin/console plugin:refresh
docker machine > bin/console plugin:install PLUGINNAME
docker machine > bin/console plugin:activate PLUGINNAME

docker machine > copy changed StorefrontJS files to machine
docker machine > ./bin/build-storefront.sh

remote server  > copy compiled minified StorefrontJS to server
location       > plugindir/src/Resources/app/storefront/dist/storefront/js/full-plugin-name.js

remote server  > bin/console theme:compile
remote server  > bin/console cache:clear
```
### Compile Vue
```bash
docker machine > bin/console plugin:refresh
docker machine > bin/console plugin:install PLUGINNAME
docker machine > bin/console plugin:activate PLUGINNAME

docker machine > copy changed VueJS component files to machine

docker machine > npm run build
location       > plugindir/src/Resources/app/storefront/src/plugins/COMPONENT-NAME

docker machine > ./bin/build-storefront.sh

remote server  > copy compiled minified VueJS to server
location       > plugindir/src/Resources/app/storefront/src/plugins/COMPONENT-NAME/dist/COMPONENT-NAME.js
remote server  > copy compiled minified StorefrontJS to server
location       > plugindir/src/Resources/app/storefront/dist/storefront/js/full-plugin-name.js

remote server  > bin/console theme:compile
remote server  > bin/console cache:clear
```






