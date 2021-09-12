---
layout: post
title: "Database migrations with FluentMigrator"
date: "2019-07-06"
categories: 
  - "c"
  - "database"
  - "programming"
tags: 
  - "fluentmigrate"
  - "sqlite"
---

### How to setup database migrations in AspNet Core 3 project and SQLite database

Add NuGet packages to projects with migration classes (data layer library):  
\- FluentMigrator  
\- Microsoft.Data.Sqlite

Add two additional NuGet packages to AspNet Core web project :  
\- FluentMigrator.Runner  
\- FluentMigrator.Runner.SQLite

###### Configure Startup.cs

using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.AspNetCore.HttpsPolicy;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;

using FluentMigrator.Runner;
using FluentMigrator.Runner.Initialization;
using Bisaga.Core.Data.Model.Common;
using Trade.Core.Data.Model;

namespace Trade.Scanner
{
     public class Startup
    {
        public Startup(IConfiguration configuration)
        {
            Configuration = configuration;
        }

        public IConfiguration Configuration { get; }

        // This method gets called by the runtime. Use this method to add services to the container.
        // For more information on how to configure your application, visit https://go.microsoft.com/fwlink/?LinkID=398940
        public void ConfigureServices(IServiceCollection services)
        {
            services.AddRazorPages();
            services.AddServerSideBlazor();

            //Setup data migrations 
            services.AddFluentMigratorCore()
                .ConfigureRunner(rb => rb
                    // Add SQLite support to FluentMigrator
                    .AddSQLite()
                    // Set the connection string
                    .WithGlobalConnectionString(Configuration.GetConnectionString("DefaultConnection"))
                    // Define the assemblies containing the migrations
                    .ScanIn(typeof(Company).Assembly).For.Migrations()
                    .ScanIn(typeof(Exchange).Assembly).For.Migrations()
                ).AddLogging(lb => lb
                    .AddFluentMigratorConsole());

            // Setup services 
            //services.AddSingleton<WeatherForecastService>();
        }

        // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
        public void Configure(IApplicationBuilder app, IWebHostEnvironment env, IMigrationRunner migrationRunner)
        {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }
            else
            {
                app.UseExceptionHandler("/Home/Error");
                // The default HSTS value is 30 days. You may want to change this for production scenarios, see https://aka.ms/aspnetcore-hsts.
                app.UseHsts();
            }

            app.UseHttpsRedirection();
            app.UseStaticFiles();

            app.UseRouting();

            app.UseEndpoints(endpoints =>
            {
                endpoints.MapBlazorHub();
                endpoints.MapFallbackToPage("/\_Host");
            });

            // Run database migrations 
            migrationRunner.MigrateUp();
        }
    }
}

Part of the statement: ".**ScanIn(typeof(Company).Assembly).For.Migrations()**" is where we define assembly (DLL) where the migration classes are. In case of multiple libraries we simply repeat same method again. The class defined in the "**typeof**" function could be any class from the targeted assembly.

**Migration runner** - MigrateUp procedure

To migrate database up we define interface of migration runner in the "**Configure**" method as parameter. The dependency injection will give us an instance of runner on which we run "**MigrateUp**" on. Configure will run immediately after ConfigureServices in the application start process.

### Sample migration class

using System;
using System.Collections.Generic;
using System.Text;
using FluentMigrator;
namespace Bisaga.Core.Data.Migrations
{
  \[Migration(201907061936)\]
  public class AddCompanyExhangeStock : Migration
  {
    public override void Up()     {         
     Create.Table("Company")
       .WithColumn("Code").AsString().PrimaryKey().NotNullable().Unique()
       .WithColumn("Name").AsString()
       .WithColumn("DefaultUrl").AsString();

     Create.Table("Exchange")
       .WithColumn("Id").AsInt64().PrimaryKey().NotNullable().Unique().Identity()
       .WithColumn("Name").AsString().Unique().NotNullable()
       .WithColumn("Url").AsString().Nullable();

     Create.Table("Stock")
       .WithColumn("Symbol").AsString().PrimaryKey().NotNullable().Unique()
       .WithColumn("IssuerName").AsString().Unique().NotNullable()
       .WithColumn("ExchangeId").AsInt64().ForeignKey("Exchange", "Id");
    }

    public override void Down()     {         
       throw new NotImplementedException();
    } 
  }
}

## More informations :

[https://fluentmigrator.github.io/](https://fluentmigrator.github.io/)

https://www.youtube.com/watch?v=IX5ehpjpH3g
