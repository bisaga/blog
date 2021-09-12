---
title: "Ada database - first steps"
date: "2016-07-21"
categories: 
  - "ada"
  - "programming"
tags: 
  - "ada"
  - "postgresql"
---

# Connect to database

Using [Ada](http://www.adacore.com/) for application [development](http://www.adacore.com/knowledge) require access to database.

With [GNATColl](http://docs.adacore.com/gnatcoll-docs/sql.html) library is possible to work with PostgreSQL and Sqlite (embedded database).

#### Checking if everything is in place for database development

If you successfully [install](http://stackoverflow.com/questions/38424775/how-to-prepare-cygwin-environment-on-windows-10-for-compilation-of-ada-gnatcoll/38468529#38468529) AdaCore and compile GNATColl library, you can start writing and checking if everything works as expected.

Create new project, project file must contain gnatcoll reference:

**My dbsample1.gpr file contains:**

with "gnatcoll";
with "gnatcoll\_postgres";

project Dbsample1 is
    for Source\_Dirs use ("src");
    for Object\_Dir use "obj";
    for Main use ("main.adb");
end Dbsample1;

**And main.adb file:**

with gnatcoll.SQL.Postgres;  use gnatcoll.SQL.Postgres;
with gnatcoll.SQL.Exec;      use gnatcoll.SQL.Exec;
with Ada.Text\_IO;            use Ada.Text\_IO;

procedure Main is
   DB\_Descr : Database\_Description;
   DB       : Database\_Connection;
   IsOpen   : Boolean;
begin
   DB\_Descr := Setup(Database => "appdb",
                     User => "postgres",
                     Host => "localhost",
                     Password => "postgres",
                     Port => 5432
                    );
   DB := DB\_Descr.Build\_Connection;

   IsOpen := DB.Check\_Connection;
   if IsOpen then
      Put\_Line("Connection is open.");
   else
      Put\_Line("Last Db error = " & DB.Error);
   end if;

   -- reset state of connection for reuse
   Reset\_Connection(DB);

   Free (DB);
   Free (DB\_Descr);

end Main;

You do not forget to add PostgreSQL/bin folder to local windows PATH, or copy postgres libraries to executing application folder.

If you do everything as needed, main.exe will be created and when you run it, you  will get something minimal but without any errors :

H:\\Ada\\SOURCE\\DBSample1\\obj\\main
Connection is open.
\[2016-07-23 22:38:22\] process terminated successfully, elapsed time: 00.30s
