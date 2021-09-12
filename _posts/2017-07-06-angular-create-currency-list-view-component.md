---
layout: post
title: "Angular - create currency list view component"
date: "2017-07-06"
categories: 
  - "angular"
  - "programming"
  - "typescript"
tags: 
  - "angular"
  - "angular-cli"
  - "binding"
  - "bootstrap"
  - "rest"
---

##### You can get [source code](https://github.com/bisaga/SpringBootMyApp) of this project from github repository “[ANGULARMYAPP](https://github.com/bisaga/AngularMyApp)” .

# Build components

In [previous blog article](http://bisaga.com/blog/programming/angular-and-git-project-setup/) I describe angular environment and generate base angular skeleton application. In this article I will create my first list component and connect client app to server REST service.

# Create new component

When you create new component, you will need to name it first. You can use dash in the middle of the name, but it is not required. The dash will not be used by generator, the name will be converted by pascal convention (camel case). But your file names and folder names will remain readability.

Use angular cli interface and create skeleton for a component:

$ ng generate component currency-list
  or
$ ng g c currency-list

Result of the command in the terminal window will look something like this:

[![](/assets/images/2017-06-27-22_33_01-AngularMyApp-—-Visual-Studio-Code-300x81.png)](http://bisaga.com/blog/wp-content/uploads/2017/06/2017-06-27-22_33_01-AngularMyApp-—-Visual-Studio-Code.png)The **app.module** file is updated automatically and new component is registered to angular application:

import { CurrencyListComponent } from './currency-list/currency-list.component';

The result in the project folder hierarchy shown new sub-folder "currency-list" with all corresponding  files:

[![](/assets/images/2017-06-27-22_36_16-AngularMyApp-—-Visual-Studio-Code-228x300.png)](http://bisaga.com/blog/wp-content/uploads/2017/06/2017-06-27-22_36_16-AngularMyApp-—-Visual-Studio-Code.png)Open app.component.html and replace generated content with new component tag like:

<app-currency-list></app-currency-list>

Now start serving angular application locally :

ng serve

And navigate to http://localhost:4200 :

[![](/assets/images/2017-06-27-23_30_26-AngularMyApp-300x155.png)](http://bisaga.com/blog/wp-content/uploads/2017/06/2017-06-27-23_30_26-AngularMyApp.png)Yeah, that was easy.

### Proxy to java embedded server

To be able to call to the java spring boot server application we need to add proxy configuration as described [here](http://bisaga.com/blog/programming/angular-environment/). After proxy configuration we should  add "start" command  ("start": "ng serve --proxy-config proxy-conf.json") to "package.json" and use "npm start" instead of "ng serve".

## Create currency interface

Create file _currency.ts_ and add currency definition as is returned from server :

export interface ICurrency {
    rowId: number;
    code: string;
    abbreviation: string;
    description: string;
}

## Add "HttpModule" to application

We will request data from REST server with this module, therefore we need to add it to the app.module.ts (as imports) :

import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';
**import { HttpModule } from '@angular/http';**

import { AppComponent } from './app.component';
import { CurrencyListComponent } from './currency-list/currency-list.component';

@NgModule({
  declarations: \[
    AppComponent,
    CurrencyListComponent
  \],
  imports: \[
   ** HttpModule,**
    BrowserModule
  \],
  providers: \[\],
  bootstrap: \[AppComponent\]
})
export class AppModule { }

## Create currency service

Well, without list of currencies from back end service there will not be a currency list component. To provide the list of currencies to the local component I need to create new angular client side service class and connect it to back end server.

If you wish generate to specific folder, you define folder as a relative path to app folder in the name of new service :

$ ng g service currency-list/currency

If you are in the root or the source folder of your angular application, you are perceived to be in the app folder.

The _currency.service.ts_ and _currency.service.spec.ts_ files are created in app/currency-list folder.

The service will provide a Observable of the list of currencies from the [REST server application written in Java](http://bisaga.com/blog/programming/java-spring-boot-project-setup/). You will find the code for the server app on [this github repository](https://github.com/bisaga/SpringBootMyApp).

import { Injectable } from '@angular/core';
import { Http, Response } from '@angular/http';

import { Observable } from 'rxjs/Observable';
import 'rxjs/add/operator/map';
import 'rxjs/add/operator/do';
import 'rxjs/add/operator/catch';

import { ICurrency } from './currency';

@Injectable()
export class CurrencyService {
  currencyUrl: string = "/api/currency";

  constructor(private \_http: Http) { }

  getCurrencies() : Observable<ICurrency\[\]> {
    return this.\_http.get(this.currencyUrl)
      .map((response: Response) => <ICurrency\[\]>response.json() )
      .do(data => console.log('Received ' + JSON.stringify(data)))
      .catch(this.handleError);
  } 

  handleError(error: Response) {
    console.error(error);
    return Observable.throw(error.json().error || 'Severe error');
  }

}

### Provide new service

Don't forget to provide new service otherwise you will not be able to use it. You need _to import it and add it to the_ providers array.  If you add new service to the application itself you add into app.module.ts  file, but  you can also add it to the parent component, for example "app.component.ts".  This way all components in the hierarchy from "app" component down will be able to use the service.

import { Component } from '@angular/core';
**import { CurrencyService } from "./currency-list/currency.service";**

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: \['./app.component.css'\],
 ** providers: \[ CurrencyService \]**
})
export class AppComponent {
  title = 'app';
}

## Using the service

New service return "Observable" object, it means we will subscribe to it instead of call it. The method will not be triggered before data are really requested (by binding for example).  Unsubscribing should be automatic when using with angular components, otherwise we need to unsubscribe manually.

import { Component, OnInit } from '@angular/core';
import { ICurrency } from "app/currency-list/currency";
import { CurrencyService } from "app/currency-list/currency.service";
import { Observable } from "rxjs/Observable";

@Component({
  selector: 'app-currency-list',
  templateUrl: './currency-list.component.html',
  styleUrls: \['./currency-list.component.css'\]
})
export class CurrencyListComponent implements OnInit {
  title: string = 'Currency List';
  errorMessage: string;
  currencies: ICurrency\[\];

  constructor(private \_currencyService: CurrencyService) { }

  ngOnInit() {
      this.\_currencyService.getCurrencies()
      .subscribe(currencies => this.currencies = currencies, 
                  error => this.errorMessage = <any>error);
  }

}

And binding to the array inside HTML template:

<div class='panel panel-default'>
  <div class='panel-heading'>
    <h3 class='panel-title'>
      {{title}}
    </h3>
  </div>

  <table class='table'>
    <thead>
      <tr>
        <th>Currency</th>
        <th>Name</th>
      </tr>
    </thead>
    <tbody>
      <tr \*ngFor='let currency of currencies'>
        <td>{{currency.code}}</td>
        <td>{{currency.description}}</td>
      </tr>
    </tbody>
  </table>

</div>

In the template we use for each directive "\*ngFor" and loop over array of ICurrency objects received from java back-end service.

The result of this very simple angular list component is:

[![](/assets/images/2017-07-06-23_25_13-AngularMyApp-300x103.png)](http://bisaga.com/blog/wp-content/uploads/2017/06/2017-07-06-23_25_13-AngularMyApp.png)
