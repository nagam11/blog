## Creating a LiveSearch in Angular2
This tutorial shows how to create a LiveSearch functionality for Angular2. Another similar example of such a functionality 
can also be found [here](http://plnkr.co/edit/TWt9Gdo6AdXa2ZMu9tt7?p=preview). Complete code can be found [here](https://github.com/nagam11/LiveSearchAngular2). What we will be building is following:
![Angular2LiveSearch]({{ site.baseurl }}/images/Angular2LiveSearch.gif).

### Setting up the Angular project
To create the initial project we will use [Angular-CLI](https://github.com/angular/angular-cli). Follow the instructions and
create your LiveSearchAngular2 project. 

### Creating files
For this project we will need following files:
* app.component.html
* app.component.css
* app.component.ts
* app.module.ts
* Country.ts
* countriesService.ts

Go ahead and create the missing files in the folder app under src. In the app.module.ts add following import
```
import {CountriesService} from './countriesService';
```
Next, add the CountriesService as a Provider in the app.module.ts.
```
...
providers: [CountriesService],
....
```


### Creating UI
Let's create an input field for searching with a placeholder. Now let's set a trigger upon input using the attribute input and 
bind it to a variable named term
```
<input class="form-control customInput" placeholder="Type to search for countries..." (input)="term.next($event.target.value)">
```

Next lets declare the buttons that will appear upon input as well as the buttons that will appear upon selection. For the first
set of buttons we will use *ngFor to create all buttons dynamically from the array of countries and will use (click) to bind the click event to a method
in the Controller. Finally we want to button to show the name of the country so we assign it as item.name.
```
 <div class="btn-toolbar">
   <button class="btn customButton1" *ngFor="let item of countries" type="button" (click)="selectCountries(item)">
      {{ item.name }}
    </button>
 </div>
```
For the second list of buttons we will use the array of selectedCountries and assign another functions to the click event, namely
deleting.
```
<div class="btn-toolbar">
    <button *ngFor="let country of selectedCountries" class="btn customerButton2" type="button" (click)="deleteCountry(country)">
      {{ country.name }}
    </button>
/div>
```
You can arrange these UI elements as you wish. Second step to do is to change our app component CSS file. This step is optional and 
can be skipped or changed. Following is an example code:
```
.customButton1{
  margin: 5px;
  background-color: #19b9e7;
  border-color: #19b9e7;
  border-radius: 300px;
  color: white;
  border-color: #19b9e7;
  border: 0px solid;
  font-family: 'Lato', 'Helvetica', 'Arial', 'sans-serif';
  font-size: 11px;
  letter-spacing: 2px;
}

button.btn:hover { opacity: 0.6; color: #fff; }

button.btn:active { outline: 0; opacity: 0.6; color: #fff; -moz-box-shadow: none; -webkit-box-shadow: none; box-shadow: none; }

.customInput{
  font-family: 'Catamaran', 'Helvetica', 'Arial', 'sans-serif';
  font-weight: 200;
  letter-spacing: 1px;
  font-size: 15px;
  color: #000;
  height: 40px;
  width: 300px;
}

.customerButton2{
  margin: 5px;
  background-color: #D3D3D3;
  color: white;
  border-color: white;
  border: 1px solid;
  font-family: 'Lato', 'Helvetica', 'Arial', 'sans-serif';
  font-size: 11px;
  letter-spacing: 2px;
}
```
We are then finished with UI.

### Model
Let's create a model for our contries. In the class Country put following code.
```
export class Country {
  name: String;
  capital: String;
}
```
All countries have a name and capital. However, we won't be using the latter for this tutorial.

### Controller
Let's got to the app.component.ts and edit our controller. Firstly let's import our model and service.
```
import {Country} from './Country';
import {CountriesService} from './countriesService';
```
Next edit the Component by adding the CountriesService as a provider in the controller.
```
@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css'],
  providers: [CountriesService],
})
```
Now we start implementing the app logic. We declared two array in the UI called selectedCountries and countries so let's bind them to the controller/data model. We will declare two arrays with objects of type Country. What we also did declare, is the object item in the input field. What we want to achieve is to dynamically watch for changes in the input field and send requests using the typed message. One smart way to achieve this is using the **Observer pattern**. Here, we have an Object of type Observable which can be 'observed' in order to track changes and an Object of type Observer which 'observes' or 'subscribes' and acts upon seeing changes. In our case, we want the Object term to be an Observer and watch for changes from the results (which are retrieved from the server). To do this we use declare term as a [Subject](http://reactivex.io/documentation/subject.html) of type string.
```
import { Subject } from 'rxjs/subject';
....
countries: Array<Country>;
selectedCountries: Array<Country> = [];
term = new Subject<string>();
```
Now let's initialize our Controller with the parameter countriesService and subscribe to the result of the method search in the countries service. The return object is of type Observable.
```
constructor(private countriesService: CountriesService){
 this.countriesService.search(this.term).subscribe(results => this.countries = results);
}
```
### Service
Now let's finally implement our data retrieval. Add following imports to your CountriesService.
```
import { Injectable }    from '@angular/core';
import { Http, Response, Headers, RequestOptions } from '@angular/http';
import 'rxjs/add/operator/toPromise';
import 'rxjs/add/operator/delay';
import {Observable} from 'rxjs/Observable';
import "rxjs/Rx";
```
Next we need to configure our request options. 
```
 private headers: Headers;
  private url = 'https://restcountries.eu/rest/v2/name/';
  options: RequestOptions;
  constructor(private http: Http) {
    this.headers = new Headers({ 'Content-Type': 'application/json' });
    this.options = new RequestOptions({ headers: this.headers });
  }
```
In the controller we have called the method search(this.term) on the countriesService. So let's now implement that.
```
 search(terms: Observable<string> ) {
    return terms.debounceTime(300)
      .distinctUntilChanged()
      .switchMap(term => this.rawSearch(term));
  }
   rawSearch(terms: string) {
    return this.http.get(this.url + terms).toPromise()
      .then(this.extractDataGet)
      .catch(this.handleError);
  }
 ```
The method search initializes a new search every 300ms and calls the rawSearch method which performs a search by adding the entered string in the API call. The API will deliver a list of countries containing the searched string. Finally, we extract the returned data.
```
private extractDataGet(res: Response) {
    let body = res.json();
    return body;
  }
 ```
 If you now, run the project you should be able to search and view a list of countries. However, when you click nothing happens at the moment.
### Saving and deleting
In the beggining of the tutorial, you have implemented two buttons in the app.component.html. These two buttons were bound via a click event to two method (saving and deleting. In the app.component.ts implement the method selectCountries which is responsible for selecting countries.
 ```
  selectCountries(item: Country) {
    if ( this.selectedCountries.indexOf(item) == -1 ) {
      this.selectedCountries.push(item);
      }
    }
  ```
If we have already added the country to the list of selected ones, then we don't want to add this country again. Otherwise we push it in the list. Next we want to be able to delete some countries we selected. So let's implement the method deleteCountry.
 ```
 deleteCountry(country: Country) {
      this.selectedCountries = this.selectedCountries.filter(item => item !== country);
  }
  ```
 Finally, you should be able to perform all actions and use the LiveSearch. Full code of this tutorial can be found [here](https://github.com/nagam11/LiveSearchAngular2).
