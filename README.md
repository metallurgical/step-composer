[logo_main]: https://encrypted-tbn2.gstatic.com/images?q=tbn:ANd9GcST9v8A3x54BSoD9ipEB2i_QLTkh7OCY6VK_sGI_THbAH2IY0G1 "Logo Title Text 1"
[logo_sub]: https://www.cannastaff.com/include/themes/nasthon1001-restyle/images/delicious.gif "Logo Title Text 2"

Installing composer, load custom namespace/class


![alt text][logo_main]  Composer Install
------------
- Skip this for now

![alt text][logo_main]  Add our custom class using standard autoload(same like require declarative) - not recommended
------------
Make sure you have `composer.json` file in your root folder, if you did't have those file, refer Composer Install section above.
Open up composer.json file and write down below code :

    {
      "autoload": {
         "classmap": [
             "path/to/FirstClass.php",
             "path/to/SecondClass.php"
         ]
      }
    }
 
 
And finally update autoloader using this command : `composer update`, and just call/instantiated directly class from defined classmap above.


![alt text][logo_main]  Add our custom class/namespace using PSR4 standard - recommended
------------
1) Make sure you have `composer.json` file in your root folder, if you did't have those file, refer Composer Install section above.

2) Suppose we have folder directory like below :

    ProjectName/
    |src/
    |----/Db.php      -------> **this is our class located**
    |----/Api.php     -------> **this is our class located**
    |vendor/
    |----/composer
    |------/..list of file inside this folder...
    |----autoload.php   -------> **autoload file**
    |composer.json     -------> **need to defined psr4 here**
    |index.php         -------> **our main file to run later on**
    
3) Declare `namespace` inside both file `Db.php` and `Api.php`, give name `Emi` just for this example :
  - **Db.php** may contains this :
    
    ```Php
    <?php
    namespace Emi;
    class Connection {
	    function hey(){
		    return 'a';
	    }
    }
    ```
  - Same goes to **Api.php**
  
    ```Php
    <?php
    namespace Emi;
    class Api {
	    function hey(){
		   return 'b';
	    }
    }
    ```
    
4) Then open up `composer.json` file to add autoload properties by using `psr-4` and write down below code inside :

    
    {
      ....
      "autoload" : {
         "psr-4" : {
             "Emi\\" : "src/" //<-- "Emi\\" = this is the name of namespace, and should be forwarded by backslash, and "src/" is the   actual folder path that represent of namespace, simply put, composer will find the file having namespace "Emi" inside the folder "src"
          }
      }
    }
   
5) After finish, run this command to ensure composer update the autoload and cache : 
 - `composer update`
 
6) Finally, we can create new instance of our Db and Api class directly by using the keyword `use`, before that, we need to load `autoload.php` file from vendor folder. Open up `index.php` from your root directory and write down below code :


```Php
<?php
require_once __DIR__ . '/vendor/autoload.php'; // here we load autoload class file from vendor folder
                                               // must be note that all files inside src folder already required by composer

use Emi\Connection;      // Here we reference our code. Emi = is namespace name, Connection = is a class name
use Emi\Api;             // Here we reference our code. Emi = is namespace name, Api = is a class name

$C = new Connection();
echo $C->hey();

$A = new Api();
echo $A->hey();
```

7) Done!

8) Oh wait, what if the `src` folder having complex folder structure as example `Db.php` are located inside their own folder eg : `src/Database/Db.php`, like below structure :

    ProjectName/
    |src/
    |----/Database
    |-------/Db.php   -------> **this is our class located**
    |----/Api.php     -------> **this is our class located**
    |vendor/
    |----/composer
    |------/..list of file inside this folder...
    |----autoload.php   -------> **autoload file**
    |composer.json     -------> **need to defined psr4 here**
    |index.php         -------> **our main file to run later on**

9) So, in order to make this available again, we need to update a lil bit inside `Db.php` file, write down below code :

```Php
<?php
namespace Emi\Database;    // we just only need to add parent folder of Db.php located
class Connection {
    function hey(){
	return 'a';
    }
}
```

So in index.php file now should be like this :

```Php
<?php
require_once __DIR__ . '/vendor/autoload.php'; // here we load autoload class file from vendor folder
                                               // must be note that all files inside src folder already required by composer

use Emi\Database\Connection;      // ******* change to this, just add respective folder name
use Emi\Api;                      // Here we reference our code. Emi = is namespace name, Api = is a class name

$C = new Connection();
echo $C->hey();

$A = new Api();
echo $A->hey();
```

And finally we update again the cache using either this `composer update`

![alt text][logo_main]  Performance. Must read!!
------------
One thing worth noting is that for large projects the overhead of using the Composer autoloader can become noticeable. This is partly due to the overhead incurred with a lot of file_exists() calls. One way to get around this is to convert the autoloader to use a class map using the dumpautoload command:

`composer dumpautoload -o`

This will create a map of all namespaces to their respective files. The downside to this approach is that every time new classes are introduced the class map would need to be re-created, as well as any time the composer install/update commands are ran
