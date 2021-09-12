---
title: "Doctrine DBAL - working with database"
date: "2015-09-27"
categories: 
  - "php"
  - "programming"
tags: 
  - "database"
  - "dbal"
  - "doctrine"
  - "mysql"
  - "php"
  - "symfony"
---

In this article I am going to cover using MySql database in Symfony 2 application.

I will use the simplest possible database access technology PHP offers - PDO,  but in slightly extended form of [Doctrine DBAL](http://docs.doctrine-project.org/projects/doctrine-dbal/en/latest/). So if you want to use [Doctrine ORM](http://symfony.com/doc/current/book/doctrine.html) , this article is not right place to start.

## Before we begin

We need some sample data in the database,  so we create a database "dbsamples" with "country" table and import data from [this csv file](http://bisaga.com/blog/wp-content/uploads/2015/09/iso_3166_2_countries.csv). Because I will not use full [Doctrine ORM](http://docs.doctrine-project.org/projects/doctrine-orm/en/latest/) ,  generating db schema is not an option, I will create database manually.

create table country (
    id int not null auto\_increment,
    name char varying(250) not null,
    formal\_name	char varying(250),
    legal\_type char varying(100),
    legal\_sub\_type char varying(100),
    sovereignty char varying(100),
    capitol\_city char varying(100),
    currency\_code char varying(30), 
    currency\_name char varying(100),
    telephone\_code char varying(100),
    iso2code char varying(2),
    iso3code char varying(3),
    iso3num char varying(3),
    domain\_code char varying(20),
    primary key (id),
    key ix\_name (name),
    key ix\_formal\_name (formal\_name)
)

You can use phpMyAdmin for that,  it is usually installed by default. If you need true utf8 database, you need to set character set to [utf8mb4](https://dev.mysql.com/doc/refman/5.5/en/charset-unicode-utf8mb4.html).

## Connecting to the database

Because we use [Symfony standard framework](https://symfony.com/what-is-symfony), we already have  connection parameters in a configuration file ("parameters.yml").

\# This file is auto-generated during the composer install
parameters:
    database\_driver: pdo\_mysql
    database\_host: 127.0.0.1
    database\_port: 3306
    database\_name: dbsamples
    database\_user: dbuser
    database\_password: mypassword
    # \[20\] PDO::ATTR\_EMULATE\_PREPARES = false, 
    # \[17\] PDO::ATTR\_STRINGIFY\_FETCHES = false
    database\_options: {20: 0, 17: 0}

I added one new parameter here, an array of custom driver options ("database\_options"). By default all received data to php are stringified, it means you get all data in strings. To receive native data types from the database (int as integer for example), you need to set those options and use prepared statements. But this options will only work if you have luck and your hosting installed and enabled  "mysqlnd" native driver, [check your hosting](http://stackoverflow.com/questions/1475701/how-to-know-if-mysqlnd-is-the-active-driver) for the driver type.

In the config.yml file, this parameters are mapped to dbal connection  parameters:

\# Doctrine Configuration
doctrine:
    dbal:
        driver:   "%database\_driver%"
        host:     "%database\_host%"
        port:     "%database\_port%"
        dbname:   "%database\_name%"
        user:     "%database\_user%"
        password: "%database\_password%"
        charset:  UTF8
        options: "%database\_options%"

When we are in the symfony controller file (as in \\AppBundle\\Controller\\DefaultController.php for example), we get connection directly from the container:

$cn = $this->getDoctrine()->getConnection();

or more specific for DBAL connection :

$cn = $this->container->get('doctrine.dbal.default\_connection');

but because aquired connections are not pooled (php connections do not support connection pool yet), is actually the same if you create new connection by yourself :

$config = new \\Doctrine\\DBAL\\Configuration();
$connectionParams = array('url'=>'mysql://dbuser:mypassword@localhost/dbsamples' );
$cn = \\Doctrine\\DBAL\\DriverManager::getConnection($connectionParams, $config);

## Selecting data

Selecting data in DBAL is easy and intuitive, just use connections executeQuery method. If you use parameters in SQL statement, this will create prepared statement for you and bind parameters automatically. You should always use prepared statements because they are more secure and more efficient as plain text statements.

//create prepared statement and execute it 
$stmt = $cn->executeQuery("SELECT \* FROM country WHERE id = :id", array("id"=>157));

But there is even better way, using QueryBuilder object and assembling SQL statement with PHP code.

$stmt = $cn->createQueryBuilder()
        ->select("\*")
        ->from("country")
        ->where("id = :id")
        ->setParameters(array("id"=>157))
        ->execute();

### Fetch data to types

To get result from a statement, we need to execute one of the "fetch" methods.

$country = $stmt->fetch();

By default we receive each result row as an array, or as array of arrays for multiple records. We can even fetch directly to an instance of an class:

//fetch result to class instance 
$stmt->setFetchMode(\\PDO::FETCH\_CLASS | \\PDO::FETCH\_PROPS\_LATE, "\\Bisaga\\Database\\Model\\Country");
        
/\*@var $country \\Bisaga\\Database\\Model\\Country  \*/
$country = $stmt->fetch();

Don't forget we have all the power of [PDO library](http://php.net/manual/en/pdostatement.fetch.php) at our disposal.

With little help from [variable type hinting](https://blogs.oracle.com/netbeansphp/entry/defining_a_variable_type_in) , we get full code completion on received object instance in the IDE:

[![2015-09-27 11_05_12-Untitled - paint.net 4.0.6](images/2015-09-27-11_05_12-Untitled-paint.net-4.0.6-300x156.png)](http://bisaga.com/blog/wp-content/uploads/2015/09/2015-09-27-11_05_12-Untitled-paint.net-4.0.6.png)Well, that's of course possible only in case we use same exact names in database and in php class :

<?php

namespace Bisaga\\Database\\Model;

/\*\*
 \* Country table 
 \*/
class Country {
    /\*\*
     \* Row identifier 
     \* @var integer auto numbered unique identifier
     \*/
    public $id;

    /\*\*
     \* Country name 
     \* @var string country name  
     \*/
    public $name;
    
    /\*\*
     \* Formal country name 
     \* @var string 
     \*/
    public $formal\_name;

    /\*\*
     \* Country legal type 
     \* @var string  
     \*/
    public $legal\_type;
    
    /\*\*
     \* Country legal subtype 
     \* @var string 
     \*/
    public $legal\_sub\_type;

    /\*\*
     \* Country sovereignty 
     \* @var string  
     \*/
    public $sovereignty;

    /\*\*
     \* Country capitol 
     \* @var string 
     \*/
    public $capitol\_city;
    
    /\*\*
     \* Currency code 
     \* @var string  
     \*/
    public $currency\_code;
    
    /\*\*
     \* Country currency name  
     \* @var string 
     \*/
    public $currency\_name;

    /\*\*
     \* Country telephone code 
     \* @var string
     \*/
    public $telephone\_code;
    
    /\*\*
     \* Country 2 characters iso code 
     \* @var string 
     \*/
    public $iso2code;

    /\*\*
     \* Country 3 characters iso code 
     \* @var string 
     \*/
    public $iso3code;

    /\*\*
     \* Country 3 characters numeric iso code 
     \* @var string 
     \*/
    public $iso3num;
    
    /\*\*
     \* Country domain name suffix
     \* @var string 
     \*/
    public $domain\_code;
    
}

### Custom columns mapping

Well, you really need to have a very good reason to select different names for columns in database, but sometimes you are not original owner of database. In that case you could introduce mapping function like this:

 /\*\*
 \* Function create instance of target type and fill target properties 
 \* 
 \* @param object $sourceObject Object instance or array as source 
 \* @param string $targetClass Target class name fith full namespace 
 \* @param array $mapping  array with mapping names, 
 \*                        key represent name of the property in source object, 
 \*                        value represent column name in target object 
 \* @return any function return object instance of target type 
 \*/
private function castToClass($sourceObject, $targetClass, $mapping = null)
{
	$target = new $targetClass();
	if (is\_array($sourceObject) || is\_object($sourceObject)) {
		foreach ($sourceObject as $key => $value) {
			
			if (null === $mapping ) {
				$target->$key = $value;
			}
			elseif ( array\_key\_exists($key, $mapping) )
			{
				$mapped\_name = $mapping\[$key\];
				$target->$mapped\_name = $value; 
			}
		}
	}
	return $target;
}

### Prepare sample for mapping

We create new table "currency" in the database  :

create table currency (
    numeric\_code int not null,
    currency\_code char varying(20) not null,
    primary key (numeric\_code),
    key ix\_code (currency\_code)
)

and write new class "Currency" in php :

<?php

namespace Bisaga\\Database\\Model;
/\*
 \* Currency table 
 \*/
class Currency {
    /\*\*
     \* Currency code (840, 978 etc.)
     \* @var integer currency numeric value 
     \*/
    public $id;
    /\*\*
     \* Currency code (USD, EUR etc.)
     \* @var string currency code 
     \*/
    public $code;
}

We do not want push to far in this direction, we could start developing a new ORM or something and that is not our intention. We want to retain full power of SQL language in the application, without need to write to much PHP or SQL code manually.

##### Usage example:

 $stmt = $cn->createQueryBuilder()
                ->select("numeric\_code, currency\_code")
                ->from("currency")
                ->where("id = :id")
                ->setParameters(array("id"=>852))
                ->execute();
        
 $object = $stmt->fetch();
 /\*@var $currency \\Bisaga\\Database\\Model\\Currency  \*/
 $currency = $this->castToClass($object, 
               "\\Bisaga\\Database\\Model\\Currency", 
               array("numeric\_code" => "id", 
                     "currency\_code"=>"code"));

Mapping array contain database columns first (as keys) and class properties second (as values).

## Inserting data

Inserting data is straightforward :

$cn->insert("country", (array)$country);
$country->id = $cn->lastInsertId();

Because insert command expect array as second parameter, we simply convert object instance ($country)  to associative array.  After record is inserted, we inspect last inserted value in auto numbered column and return value to the program.

## Update data

Update is very similar to insert, except we need to add third parameter to find existing record :

 $cn->update("country", (array)$country, array("id"=>$country->id));

## Delete data

And deleting is event simpler:

$cn->delete("country", array("id"=>$country->id));

I didn't mentioned that all commands are available as query builder methods.

## Working with transactions

There is full transaction support directly in php code. We must explicitly start transaction and commit after last database command. In case of errors we need to call rollback on the used connection.

$cn->beginTransaction();
try {
	$cn->insert("country", (array)$country);
	$country->id = $cn->lastInsertId();
	$cn->commit();
} catch (Exception $exc) {
	$cn->rollBack();
	throw $exc;
}

If you think this is little verbose and want to shorten the code, you actually can, just use closure with "transactional" function :

/\*\*
 \* @param \\Bisaga\\Database\\Model\\Country $country
 \*/
public function insertWithClosure(\\Bisaga\\Model\\Country $country)
{            
	// use closure form with beginTransaction/commit/rollBack support 
	$this->getConnection()->transactional(function(\\Doctrine\\DBAL\\Connection $cn) use ($country) {
		/\* @var $cn Connection \*/
		$cn->insert("country", (array)$country);
		$country->id = $cn->lastInsertId();
	});
}
