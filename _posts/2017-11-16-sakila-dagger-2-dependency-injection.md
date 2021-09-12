---
layout: post
title: "Sakila- Dagger 2 Dependency Injection"
date: "2017-11-16"
categories: 
  - "java"
  - "programming"
tags: 
  - "dagger"
  - "database"
  - "dependency-injection"
  - "di"
  - "hikaricp"
  - "java"
  - "jooq"
---

# REST web server and dependency injection

The code from this article is available [here](https://github.com/bisaga/sakila).

I described basic usage of Dagger 2 already in [this article](http://bisaga.com/blog/programming/dependency-injection-with-dagger-2/), now we need to implement dependency injection mechanism in the web application. Typical web application would have at least two layers, one for the web server itself and another for client request processing.

[![](/assets/images/daggerwebarchitecturev2.png)](http://bisaga.com/blog/wp-content/uploads/2017/11/daggerwebarchitecturev2.png)

Spark Java web server internally use embedded Jetty web server. To setup and start the server we provide some central services like configurations, authentication, statistics etc. Those services are usually instantiated once per whole application.

For each client request a new thread is allocated for the whole duration until the request is processed. Each client request trigger a method defined in the router.

Depend on the design decisions but we usually want that each request will be processed by new object instance (an example is ActorResource in the picture). We will probably have multi threaded problems or simply data leaks from one user to another if we don't create new instance each time the request is received.

Some objects needed in the typical request processing scenario have different lifecycle requirements, for example database transaction object must be the same for the whole duration of the started transaction but different for each user. There are times when we need two independent transactions in one service request for example. Transactions are span over multiple service objects for example.

On the other side when we do not require transaction (read only processing) we would be better of if we use first available connection allocated to us from the connection pool for the smallest amount of time possible.

As we see from the use cases above there are very different lifecycle scenarios and dependency injection must support them all.

## Scopes

We will need at least two scopes for our web application. First scope is application level scope. In the dagger this scope is on by default. Each time we tag a class as a "@Singleton", the object will be instantiated on the application level and all subsequent requests for this object will return the same instance. So the singleton representing an "application scope" by default. No need for specific scope definitions.

Classes without any scope annotations (no @Singleton or any other scope) are always provided with a new instance.

To manage injection on the application level we create ApplicationComponent interface and ApplicationModule class.

package com.bisaga.sakila.dagger;

import com.bisaga.sakila.Application;
import com.bisaga.sakila.server.ConfigProperties;
import com.bisaga.sakila.server.RequestSession;

import dagger.Component;
import javax.inject.Singleton;

@Singleton
@Component(modules = {ApplicationModule.class})
public interface ApplicationComponent {

    // we first need to create instance of Application on which we will inject dagger into
    Application application();

    // Parent component is obliged to declare sub components getters inside its interface (RequestScope component)
    RequestComponent requestComponent();

    // We will need new instance of RequestSession for each user requests (save instance to the request attributes)
    String REQUEST\_SESSION\_ATTR\_NAME = "requestSession";
    RequestSession requestSession();

    ConfigProperties configProperties();

}

Application module class:

package com.bisaga.sakila.dagger;

import com.bisaga.sakila.server.ConfigProperties;
import com.bisaga.sakila.server.HikariProperties;
import com.bisaga.sakila.server.RuntimeEnvironment;
import com.bisaga.sakila.server.GsonTransformer;
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.zaxxer.hikari.HikariConfig;
import com.zaxxer.hikari.HikariDataSource;
import dagger.Module;
import dagger.Provides;
import spark.ResponseTransformer;

import javax.inject.Named;
import javax.inject.Singleton;
import java.util.Properties;

@Module
public class ApplicationModule {
    // this is a parameter member variable
    private RuntimeEnvironment runtimeEnvironment;

    // we need an constructor to take parameter in
    public ApplicationModule(RuntimeEnvironment runtimeEnvironment) {
        this.runtimeEnvironment = runtimeEnvironment;
    }

    // This provide method at the creation of the instance of ConfigProperties object take the parameter from the local member variable.
    @Provides
    @Singleton
    public ConfigProperties provideConfigProperties() {
        return new ConfigProperties(runtimeEnvironment);
    }

    // All provide methods should be static, generated dagger code is much simpler and smaller,
    // except when module member variables are needed to create new instances
    // Gson constructor is not available to be annotated with inject (not our source), instead we write provider method
    // the method is static, because we do not need any module member variable to build new instance
    @Provides
    @Singleton
    public static Gson provideGson(){
        return new GsonBuilder().setPrettyPrinting().create();
    }

    // Here we provide an instance of the ResponseTransformer interface. Because we didn't define which implementation
    // we need at injecting place we write provide method with the injected parameter expressed with a required interface type
    // If we have more then one possible implementation, we put on "Named" annotation and associate the same name at
    // injected constructor variable
    @Provides
    @Singleton
    public static ResponseTransformer provideResponseTransformer(GsonTransformer gsonTransformer){
        return gsonTransformer;
    }

    // here we provide a String instance. We need the provide method, because many "String" objects are possible.
    // We use "Named" annotation to be able to differentiate this one from other possible provide methods.
    // At injecting place we also define same "Named" annotation to connect the proper one
    // The method could be static, because is independent of local member variables
    @Provides
    @Singleton
    @Named("api\_key")
    public static String provideApiKey(){
        return "01657172-ecc0-4cb6-8486-5e7e05a0876f";
    }

    // Here we provide external library class as singleton with our extended properties class as constructor parameter
    @Provides
    @Singleton
    public static HikariConfig provideHikariConfig(HikariProperties properties) {
        return new HikariConfig(properties);
    }

    // Here we provide external library class as singleton (Hikari Connection Pool Data source, holder of pooled connections)
    @Provides
    @Singleton
    public static HikariDataSource provideHikariDataSource(HikariConfig config) {
        return new HikariDataSource(config);
    }

}

At the application level example we present next use cases:

- creating an instance with the supplied constructor parameter (ConfigProperties service)
- instantiate objects  from the external dependencies with provide method (Gson)
- instantiate specific implementation for an interface (ResponseTransformer interface)
- instantiate an String object with named annotation (using name as differentiator)

## Request scope

To create "request scope" we write one annotation interface ("@interface") one component (RequestComponent) and at least one module class (RequestModule). The component must have sub-component annotation.

To manage DI on the request scope level we create annotation type interface RequestScope , RequestComponent interface and RequestModule class.

@Scope
@Retention(RetentionPolicy.RUNTIME)
public @interface RequestScope {
}

Just to be clear I want to emphasize that each class annotated with the "@RequestScope"  annotation will be instantiated exactly once per created instance of RequestComponent class.  It means that scope annotations represent local singletons.

package com.bisaga.sakila.dagger;

import com.bisaga.sakila.resource.ActorResource;
import com.bisaga.sakila.server.\*;
import dagger.Subcomponent;

import java.sql.Connection;

@RequestScope
@Subcomponent(modules = {RequestModule.class})
public interface RequestComponent {

    ActorResource actorResource();
}

Module class:

package com.bisaga.sakila.dagger;

import com.bisaga.sakila.dbmodel.tables.daos.ActorDao;
import com.bisaga.sakila.server.JooqConfigurationBuilder;
import com.bisaga.sakila.server.Transaction;
import com.bisaga.sakila.server.TransactionBuilder;
import dagger.Module;
import dagger.Provides;
import org.jooq.Configuration;
import org.jooq.DSLContext;
import org.jooq.impl.DSL;

import javax.inject.Named;

@Module
public class RequestModule {

    // Provides primary transaction/connection for the whole time request ran (loaded at the first component needs)
    // Because it is request scoped, it will be called only once, any other time the request scoped instance will return
    @Provides
    @RequestScope
    public static Transaction provideTransaction(TransactionBuilder transactionBuilder) {
        return transactionBuilder.create(false);    // default autoCommit=False, commit/rollback must be called manually
    }

    // Default configuration with transactions enabled, this is RequestScoped default database connection
    @Provides
    @RequestScope
    public static Configuration provideConfiguration(JooqConfigurationBuilder jooqConfigurationBuilder) {
        return jooqConfigurationBuilder.build();
    }

    // Default DSLContext with default configuration
    @Provides
    @RequestScope
    public static DSLContext provideDSLContext(Configuration configuration) {
        return DSL.using(configuration);
    }

    @Provides
    public static ActorDao provideActorDao(Configuration configuration){
        return new ActorDao(configuration);
    }

}

We use localized singletons especially for the transaction and jooq data access support.

Provide methods are optional, we can decorate classes in the source code with the corresponding annotations.

## Service classes

If we analyze the code in the consumer classes, it become ridiculously simple.  All externalized requirements are created by the dagger code almost hassle free.

In the ActorResource class for example we analyze received request,  extract parameters and start business logic. The transaction object is created on the request scope and pass down to all service objects in need to collaborate in the same database transaction.

package com.bisaga.sakila.resource;

import com.bisaga.sakila.dagger.RequestScope;
import com.bisaga.sakila.dbmodel.tables.pojos.Actor;
import com.bisaga.sakila.server.Transaction;
import com.bisaga.sakila.service.ActorService;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import spark.Request;
import spark.Response;

import javax.inject.Inject;
import java.util.List;

@RequestScope
public class ActorResource {
    private static final Logger LOG = LoggerFactory.getLogger(ActorResource.class);
    private final Transaction transaction;
    private final ActorService actorService;

    @Inject
    public ActorResource(
            Transaction transaction,
            ActorService actorService
    ) {
        this.transaction = transaction;
        this.actorService = actorService;
    };

    public List<Actor> getActors(Request request, Response response) {
        try {
            // Analyse & extract request parameters

            // multiple SQL statements within single transaction, first we need to start the transaction
            transaction.begin();

            // Call services with parameters (if any)
            List<Actor> actors = actorService.getActors();

            // Commit transaction and release the underline connection to the pool
            transaction.commit();

            // Return list, it will be transformed by GsonTransformer and returned to the browser as Json
            return actors;

        // Catch expected business exceptions and throw them with meaningful messages and present them to the customer
        } catch (Exception e) {
            // Rollback transaction and release the underline connection to the pool
            transaction.rollback();
            // rethrow all exceptions to master exception handler which create response for the customer
            throw e;
        }
    }
}

In the ActorService class we receive all constructor parameters from the dagger automatically.

package com.bisaga.sakila.service;

import com.bisaga.sakila.dagger.RequestScope;
import com.bisaga.sakila.dbmodel.tables.daos.ActorDao;
import com.bisaga.sakila.dbmodel.tables.pojos.Actor;
import org.jooq.DSLContext;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import javax.inject.Inject;
import java.util.List;

@RequestScope
public class ActorService {
    private static final Logger LOG = LoggerFactory.getLogger(ActorService.class);

    private final DSLContext db;
    private final ActorDao actorDao;

    @Inject
    public ActorService(DSLContext db, ActorDao actorDao) {
        this.db = db;
        this.actorDao = actorDao;
    }

    public List<Actor> getActors() {
        return actorDao.findAll();
    }

    public Actor getActor(int id) {
        return actorDao.fetchOneByActorId(id);
    }
}

The class ActorService require two objects at the constructor: jooq DSLContext  class and ActorDao class.

DSLContext class is part of the Jooq data access library and is instantiated with the provider method "provideDSLContext". It is annotated as @RequestScope it means the RequestComponent will keep single instance of it for the duration of one request cycle.

ActorDao object is also generated by Jooq library so we couldn't tag it with scope annotation in the source (so we wrote the provideActorDao method in the request module).

### Summary

Dagger calculate all dependencies in the compile time and generate the required code for the whole graph of dependencies and is able to instantiate appropriate objects at appropriate times really fast.

The code from this article is available [here](https://github.com/bisaga/sakila).

### Other resources:

[More about dagger scopes, sub-components](https://proandroiddev.com/dagger-2-part-ii-custom-scopes-component-dependencies-subcomponents-697c1fa1cfc) .
