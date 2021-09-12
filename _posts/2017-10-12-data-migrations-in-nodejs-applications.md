---
layout: post
title: "Data migrations in Node applications"
date: "2017-10-12"
categories: 
  - "javascript"
  - "programming"
tags: 
  - "database"
  - "javascript"
  - "migrations"
  - "nodejs"
---

# Setup [db-migrate](https://db-migrate.readthedocs.io/en/latest/) tool

As your application grow your database model evolve too.  The changes from one version to another is made by migrations. Each migration in the db-migrate tool is in essence  a small javascript program. You can code your migration changes manually in the javascript syntax or generate small javascript program (with --sql-file option) to execute your SQL script files. One for the UP and one for the DOWN function of migration.

[![](/assets/images/dbmigrate.png)](http://bisaga.com/blog/wp-content/uploads/2017/10/dbmigrate.png)

**up**: you upgrade your database schema to the new version **down**: you reverse latest changes to the previous version **create**: create new migration, with an **\--sql-file** option create a SQL file runner

## Installation

We install db-migrate module and corresponding database driver with npm.

npm install db-migrate
npm install db-migrate-pg

Depends where you install it the command is available as:

$ ./node\_modules/.bin/db-migrate
or
$ db-migrate

It is possible to add local ./node\_module/.bin folder to the **local path variable** as I described in [this article](http://bisaga.com/blog/programming/setting-up-the-environment-for-nodejs-and-typescript/#git-bash-setup) and call it the same way as you would if you install it in to the global space.

## Configuration

Minimal config file (database.json) to work with postgres database in the development environment:

{
  "dev": "postgresql://postgres:postgres@localhost:5432/dbsam"  
}

The file should be in the main project folder. More about [configuration](https://db-migrate.readthedocs.io/en/latest/Getting%20Started/configuration/).

## Migrations with SQL files

To create new empty migration use the command:

$ db-migrate create new\_mig\_name --sql-file

By default your migration javascript files reside in the "./migrations" folder and sql files in the "./migrations/sqls"  subfolder.

Three new files are created, all files are prefixed with the timestamp which represent order of execution.

[![](/assets/images/2017-10-12-22_37_52-currency.ts-database-Visual-Studio-Code.png)](http://bisaga.com/blog/wp-content/uploads/2017/10/2017-10-12-22_37_52-currency.ts-database-Visual-Studio-Code.png)

Additional two files with the suffix **sql** are prepared for your upgrade script and downgrade script. If you wish to have ability to downgrade database to the previous level make sure you write down script to.

Example of upgrade script (it is just for the sake of example, you basically write DDL statements with the familiar SQL syntax):

/\*
\* Table: Currency  
\*
\* Contains currencies 
\*/
CREATE TABLE currency (
	code                varchar(60) not null,               -- code 
	abbreviation        varchar(60),						-- ex. EUR, USD
	description         text,								
	row\_id              serial primary key,                 -- row identifier, autonumber, pk 
	org\_id				smallint not null,					-- multitenant identifier 
	co\_id				int NOT NULL,						-- company identifier  
	created\_at          timestamptz not null default now(),
	created\_by          varchar(60) not null,
	modified\_at         timestamptz,
	modified\_by         varchar(60),
	CONSTRAINT currency\_sys\_org\_fk FOREIGN KEY (org\_id) REFERENCES sys\_org (row\_id),
	CONSTRAINT currency\_sys\_co\_fk FOREIGN KEY (co\_id) REFERENCES sys\_co (row\_id)
) WITHOUT OIDS;                                               -- don't create object identifier field 
CREATE INDEX currency\_org\_id\_fkix ON currency (org\_id);
CREATE INDEX currency\_co\_id\_fkix ON currency (co\_id);
CREATE INDEX currency\_created\_at ON currency (created\_at DESC) ;
CREATE INDEX currency\_modified\_at ON currency (modified\_at DESC) ;

Example of downgrade script:

DROP TABLE currency;

Run "db-migrate up" command and all your migrations which are not yet executed will run.

The migration log is kept in the database migrations table. The table is automatically created at first run.

## Other useful commands

**reset**: will rewind your database to the state before first migration is called

## Using db-migrate with typescript

If you write node programs with the typescript you probably wish to use it with migration to. I didn't go in this direction simply because I write my scripts in SQL language and runners are just perfect already in javascript. Because the migration are already in the javascript (the runner part), you should exclude migrations folder from the typescript compiler path.

Sample tsconfig.json file with **excluded** migrations folder :

{
    "compilerOptions": {
        "target": "es2017",
        "module": "commonjs",
        "moduleResolution": "node",
        "noEmitOnError": true,
        "noImplicitAny": true,
        "experimentalDecorators": true,
        "sourceMap": true,
        "baseUrl": ".",
        "allowJs": true,
        "paths": {
            "\*":\[ 
                "src/types/\*"
            \]
        },
        "outDir": "dist"
    },
    "typeRoots": \[
        "node\_module/@types",
        "src/@types"
    \],
    "include": \[
        "src/\*\*/\*",
        "spec/\*\*/\*"    
    \],
    "exclude": \[
        "dist",
        "migrations",
        "node\_modules"
    \]
}

I didn't include migration scripts to production deployment code yet, that step would be necessary if you wan't to upgrade database automatically after deployment.

###### External links

Database migration tool: [Db-migrate](https://db-migrate.readthedocs.io/en/latest/). Gui prototyping and drawing tool: [The pencil](http://pencil.evolus.vn/).
