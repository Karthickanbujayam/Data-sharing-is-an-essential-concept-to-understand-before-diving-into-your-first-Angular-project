# Data-sharing-is-an-essential-concept-to-understand-before-diving-into-your-first-Angular-project
Data sharing is an essential concept to understand before diving into your first Angular project


Contents
1.	What is a Component?
1.	Binding
2.	Interpolation
2.	How to Load Components
3.	Using Directives
4.	Pipes
5.	Don’t Touch the DOM
6.	Smart vs Dumb Components (Container vs Presentational)
7.	Lifecycle Hooks
8.	Pass data into a component with @Input()
9.	Emit custom events from a component via @Output()
10.	Grab elements in the DOM with @ViewChild() or @ViewChildren 
  
Components are the building blocks of an Angular application. There are many pages of documentation dedicated solely to components, so it’s easy to get lost when first learning the framework. In the following lesson, you will learn everything you need to know as a beginner to be successful with Components in Angular 6.

# 1. What is a Component?

A component is just a class that serves as a controller for the user interface. It consists of three parts - some TypeScript code, an HTML template, and CSS styles.
Binding
Binding is just a way to connect data from the TypeScript code to the HTML. We can bind to attributes using square brackets []. For example, we might want to disable a button after it has been clicked.
@Component(...)
export class SomeComponent {
  clicked = false
}
<button [disabled]="clicked"></button>
We can also bind to events using () parenthesis. Let’s setup a method to change the clicked property to false - this is basic event handling in Angular.
@Component(...)
export class SomeComponent {
  clicked = false;

  handleClick() {
    this.clicked = true;
  }
}
<button [disabled]="clicked" (click)="handleClick()"></button>
Interpolation
We can also interpolate values from the TypeScript using handlebars syntax.
@Component(...)
export class SomeComponent {
  appName = 'Cool App';
}
<h1>{{ appName }}</h1>
This will print out a heading of Cool App

# 2. How to Load Components

There are serveral different ways you can load components in Angular:
(1) Declare the component in the HTML.
<app-cool></app-cool>
(2) Load the component with the router. This tells Angular to imperatively load the component when you navigate to http://localhost:4200/cool
const routes: Routes = [{ path: 'cool', component: CoolComponent }];
(3) Load the component dynamically as an Entry Component. This is most common for things like popups and modals, which usually get rendered after initialization, but don’t trigger a route change. A good example is the AlertController in the Ionic Framework.
(4) Convert it to a web component with Angular Elements. This is a new feature in Angular6 that allows us to export a component to be used outside of Angular altogether.

# 3. Using Directives

Directives allow you to extend HTML elements with custom attributes. Angular comes with several built-in directives you should know about:
•	*ngIf - Renders some HTML is condition is true.
•	*ngFor - Repeats elements by looping over an array of data.
•	ngClass - Applies CSS classes conditionally
•	ngStyle - Applies styles conditionally
Structural directives start with a * and can determine which elements are rendered in the DOM.
<!-- Conditional -->
<div *ngIf="clicked">Clicked!</div>

<!-- Loop -->
<div *ngFor="let boat of boats">
  {{ boat.name }}
</div>
Other built-in directives can do things like apply conditional CSS classes. The left side contains a CSS class, which will be applied when the right side evaluates to true.
<h3 [ngClass]="{
  'green': boat.name === 'Starfire',
  'red'  : boat.name === 'Oracle'  
}">
But you can also build your own. A custom directive in Angular is just a component minus the HTML/CSS. Instead of creating its own template, the directive becomes it’s own custom attribute that attaches to a host element. Let’s say we want a directive that magnifies an image when hovered.
When should you use a directive over a component? Simple… If you don’t need to customize the HTML structure you probably want a directive.
ng g directive maginfier
import { Directive, HostBinding, HostListener } from '@angular/core';

@Directive({

  selector: '[magnifier]'
  
})

export class MagnifierDirective {

  // The image's width property
  
  @HostBinding('width') width = 200;

  // Fires when the user hovers
  
  @HostListener('mouseenter', ['$event'])
  
  onHover(e) {
  
    this.width = 300;
    
  }
  
}
Now to can apply it to any element in the HTML and it will increse width from 200px to 300.

<img [src]="imgURL" magnifier>

# 4. Pipes

Pipes provide yet another way to manipulate values in the HTML.
Let’s say you want to round an interpolated value:
{{ 23.232323223233 | number }}
The number will be rendered as 23.232.
You can build your own pipes to easily format interpolated values. Let’s say we need to make sure that a user’s input is in camelCase.
ng g pipe camelCase
import { camelCase } from 'lodash';

@Pipe({name: 'camelCase'})

export class CamelCasePipe implements PipeTransform {

  transform(value: string): string {
  
    return camelCase(value)
    
  }
  
}

{{ 'Foo Bar Baz' | camelCase }}

<!-- fooBarBaz -->

# 5. Don’t Touch the DOM

If you’re a seasoned JavaScript developer, you’re probably used to doing things like this:
document.getElementById('#some-element').innerHTML = 'Hello';

// or with jQuery
$('#some-element').html('Hello');
Even experienced Angular Developers can hit this pitfall by manipulating the DOM using ElementRef.nativeElement.
This code will work in Angular on the web, so why is it a bad practice? Angular is designed to be a cross-platform framework that also works on servers, mobile apps, and service workers - but the DOM does exist on these platforms. Therefore, Angular encourages you to use it’s API to make single-codebase apps possible. Take a look at no. 9 on this list of ways to misue Angular.

# 6. Smart vs Dumb Components (Container vs Presentational)
One of the most common component patters in Angular is the smart-dumb pattern - aka Container vs Presentational or Stateful vs Stateless. In a more general sense, it is just a separation of concerns to keep our code predictible.
Your smart components are concered with how the code works, while the dumb component deals with how it looks.
7. Lifecycle Hooks
Just like us humans, components have a lifecyle. They are created, things change, then they’re destroyed. You can hook into these lifecycle events, which are commonly used to run setup and teardown code.
By far the most common Lifecycle hook is ngOnInit which happens after the class is instantiated and the data-bound properties have been checked. You use this hook perform any logic needed right away, like fetching data with HTTP calls, form setup, property definitions, and stuff like that.
constructor() {
  // first thing to happen, class instantiation
}

ngOnInit() {
  // second thing to happen, bindings are available
}

ngAfterViewInit() {
  // child views loaded
}

ngDoCheck() {
  // happens on each change detection check
}

ngOnDestroy() {
  // teardown
}

# 8. Pass data into a component with @Input()

Dectorators are a TypeScript feature that allows us to write expressive code through meta programming. I highly recommend watching Sharing Data between Components video to see some of these decorators in action. A very common use case is passing data from a parent component to a child through an HTML attribute.
export class ChildComponent {
  @Input() color;
}
This allows us to pass data into the component:
<child-component color="blue"></child-component>

# 9. Emit custom events from a component via @Output()

We can emit custom events in a component using Output and EventEmitter. Here is a trivial example of event emitting on mouseenter.
export class MyComponent {

  @Output() hovered = new EventEmitter();

  @HostListener('mouseenter', ['$event'])
  
  onHover(e) {
  
    this.hovered.emit('howdy')
    
  }
  
}

We can then listen to the custom event on the component:
<my-component (hovered)="doSomething()"></my-component>

# 10. Grab elements in the DOM with @ViewChild() or @ViewChildren

Another common task is to grab child elements or components in the DOM. You can grab the first element with ViewChild or an array of elements with ViewChildren (similar to querySelector and querySelectorAll in vanilla JS)
// DOM element
@ViewChild('title') title;
// Children components
@ViewChildren(BoatComponent) boats;


ngAfterViewInit() {
  console.log(this.title);
  console.log(this.boats);
}
The child views in this component might look something like this. Notice how a #title is used to set a template reference to the DOM element.
<h1 #title>My Boats</h1>

<boat></boat>
<boat></boat>
<boat></boat>

