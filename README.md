Propel2Exa
==========

How You can integrated prople2 ORM with your corephp project
---------------------------------------------------------------

Use   composer json for  for install propel2 with composer.json


      {
         "require": {
             "propel/propel": "2.0.*@dev"
         },
         "autoload": { 
              "classmap": ["generated-classes/"] 
         } 
      }


Note "classmap": ["generated-classes/"]  is  important for generate a autoload class map from generated-classes/


  1. create build.properties file on root  level
      
            propel.project = propeldemo
            # The Propel driver to use for generating SQL, etc.
            propel.database = mysql
            
            # This must be a PDO DSN
            propel.database.url = mysql:host=localhost;dbname=propel2
            propel.database.user = root
            # propel.database.password #

  3.  Run command 
 
            > cd projectdir
            >$ vendor/bin/propel --verbose reverse "mysql:host=127.0.0.1;dbname=propel2;user=root;password=" 
    
4. create a schema file shema.xml in a generated-schema folder
            
            <?xml version="1.0" encoding="utf-8"?>
              <database name="bookstore" defaultIdMethod="native" defaultPhpNamingMethod="underscore">
               <table name="groups" idMethod="native" phpName="Groups">
                 <column name="id" phpName="Id" type="INTEGER" primaryKey="true" autoIncrement="true" required="true"/>
                 <column name="name" phpName="Name" type="VARCHAR" size="100" required="true"/>
                 <vendor type="mysql">
                   <parameter name="Engine" value="InnoDB"/>
                 </vendor>
               </table>
               <table name="posts" idMethod="native" phpName="Posts">
                 <column name="id" phpName="Id" type="INTEGER" primaryKey="true" autoIncrement="true" required="true"/>
                 <column name="title" phpName="Title" type="LONGVARCHAR" required="true"/>
                 <column name="content" phpName="Content" type="LONGVARCHAR" required="true"/>
                 <column name="user_id" phpName="UserId" type="INTEGER" required="true"/>
                 <vendor type="mysql">
                   <parameter name="Engine" value="MyISAM"/>
                 </vendor>
               </table>
               <table name="users" idMethod="native" phpName="Users">
                 <column name="id" phpName="Id" type="INTEGER" primaryKey="true" autoIncrement="true" required="true"/>
                 <column name="name" phpName="Name" type="VARCHAR" size="200" required="true"/>
                 <column name="age" phpName="Age" type="VARCHAR" size="50" required="true"/>
                 <column name="email" phpName="Email" type="VARCHAR" size="200" required="true"/>
                 <vendor type="mysql">
                   <parameter name="Engine" value="InnoDB"/>
                 </vendor>
               </table>
              </database>
            


Note – in schema file database  name is Impotent because this is connection name use in runtime-config.php file and  runtime-config.xml


now create model from schema file
                   >vendor/bin/propel build:model

--create all required model class file in  generated-class file

--now make a runtime config file with xml or manualy 

-- create a runtime-config.xml file in rool directory  with below content

      
      <?xml version="1.0" encoding="UTF-8"?>
      <config>
       <propel> 
         <datasources default="bookstore">
           <datasource id="bookstore">
             <adapter>mysql</adapter> <!-- sqlite, mysql, myssql, oracle, or pgsql -->
             <connection>
               <dsn>mysql:host=127.0.0.1;dbname=propel2</dsn>
               <user>root</user>
               <password></password>
             </connection>
           </datasource>
         </datasources>
      
         <log>
           <logger name="defaultLogger">
             <type>stream</type>
             <path>/opt/lampp/htdocs/propel2.3/log/propel.log</path>
             <level>300</level>
           </logger>
         </log>
       </propel>
      </config>

 Note   in above file datasource id name is very important it is same as schema file database  connection name 



run below command for generate
runtime config file 
           > vendor/bin/propel config:convert-xml

--This will generate a config.php file in generated-conf folder 
--now  we are ready to run the application 

--Make root level boostrap.php file 

      <?php
       # bootstrap.php 
      // setup the autoloading // patha to vendor /autoload file 
      require_once 'vendor/autoload.php';
      
      // setup Propel // fpath to runtime configfile 
      require_once 'generated-conf/config.php';
      


ready for test 
make a test.php file

      <?php       
      require 'bootstrap.php'
      $userobj= new Users();
      $userobj->setName(“abc”);
      $userobj->setAge(20);
      $userobj->save();










