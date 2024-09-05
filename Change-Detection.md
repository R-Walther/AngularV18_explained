 # 1. The Zone
 Reading: https://justangular.com/blog/a-change-detection-zone-js-zoneless-local-change-detection-and-signals-story
 <br> Video Summary: https://www.youtube.com/watch?v=DbUS6h1tANQ
 <br> **How does Angular know when to refresh the view? How does it know when the data changes? How does it know when to run the change detection?**
<br><br> Zone.js has been around since the early days of Angular 2.0. It is a library that monkey patches the browser APIs and enables us to hook into the lifecycle of the browser events. 
 
 <br> The **NgZone** helps in managing the execution context and provides a method called run() that triggers change detection. Here's an example:
 ```
import { Component, Input, NgZone } from '@angular/core';

@Component({
  selector: 'app-my-component',
  template: `
    <div>{{ myProperty }}</div>
  `,
})
export class MyComponent {
  private _myProperty: string;

  @Input()
  set myProperty(value: string) {
    this._myProperty = value;
    this.onMyPropertyChange();
  }

  get myProperty(): string {
    return this._myProperty;
  }

  constructor(private ngZone: NgZone) {}

  updatePropertyManually(newValue: string): void {
    this.ngZone.run(() => {
      this.myProperty = newValue;
    });
  }

  onMyPropertyChange(): void {
    // Custom logic to handle property change
    console.log('myProperty has changed:', this.myProperty);
  }
}
```
the **updatePropertyManually()** method is wrapped inside the this.ngZone.run() function. This ensures that the property change triggers change detection and propagates the updated value throughout the application.

After manually updating an input property, we can call the ChangeDetectorRef method markForCheck() to notify Angular to run change detection on the component and its child components.
  Angular uses these strategies to determine whether a child component should be checked while running change detection for a parent component.
  >The default strategy, internally referred to as CheckAlways, implies regular automatic change detection for a component unless the view is explicitly detached.

> What’s known as OnPush strategy, internally referred to as CheckOnce, implies that change detection is skipped unless a component is marked as dirty. Angular implements mechanisms to automatically mark a component as dirty. When needed, a component can be marked dirty manually using markForCheck method exposed on ChangeDetectorRef.
> 
## Default strategy
Each Angular component has an associated change detector, which is created at application startup time. <br>
<br>
Default change detection strategy means that a child component will always be checked if its parent component is checked. 
<br>
> for each expression used in the template, it's comparing the current value of the property used in the expression with the previous value of that property.
> If the property value before and after is different, it will set isChanged to true.
<br>
The only exception to that rule is that if you **detach a change detector** in the child component like this:
```
@Component({
  selector: 'a-op',
  template: `I am OnPush component`
})
export class AOpComponent {
  constructor(private cdRef: ChangeDetectorRef) {
    cdRef.detach();
  }
}
```

 ### What to do when the change is not detected?
 Change detection will fire whenever the parent component changes the data it passes to the child, provided that the data is a JS primitive datatype(string, number, boolean).   
 However, in the following scenarios, it will not fire and you have to take extra actions in order to make it works:
 
  - If you are using a nested object or array (instead of a JS primitive data type) to pass data from Parent to Child, change detection (using either setter or ngchanges) might not fire
  
  - If you are mutating data outside of the angular context / outside the Zone (i.e., externally), then angular will not know of the changes. You may have to use ChangeDetectorRef or NgZone in your component for making angular aware of external changes and thereby triggering change detection. Refer to this.
       
possible approaches:    
  1. Let Change Detection know there is something changing here by changeDetectorRefVar.detectChanges()
  2. Marking Dirty with OnPush
  3. @Input and @Output getters and setters
  4. ngOnChanges() Lifecycle hook

  ## 1. [ChangeDetectionRef](https://v2.angular.io/docs/ts/latest/api/core/index/ChangeDetectorRef-class.html#!#detectChanges-anchor)
  detectChanges() : void  
   Checks the change detector and its children.  
      
   This can also be used in combination with ChangeDetectorRef to implement local change detection checks.
  
  ## 2. OnPush aka CheckOnce strategy  
  change detection is skipped for a component until it’s marked as dirty, then checked once, and then cleared again. A component can be marked dirty either automatically or manually using markForCheck method.    
  > **Pros:** recieve parameter value and parse it immediately.<br>
      
  > **Cons:** no default values on getters and setters. <br>
   ```
   @Component({
     selector: 'a-op',
     template: `I am OnPush component`
   })
   export class AOpComponent {
     constructor(private cdRef: ChangeDetectorRef, private serice: Service) {
     }
   ngOnInit(): void {
    this._subscription.add(
      this.service.observable$.subscribe((val) => {
        this.variable = val;
        this.cdRef.detectChanges();
      })
    );
   }
  }
   ```
  ## 3. @Input and @Output getters and setters
   reading: [angular-with-setters-and-getters](https://blog.bitsrc.io/simplify-input-property-handling-in-angular-with-setters-and-getters-256ddca84ac1)   
   provide a simpler and more controlled way to handle input property changes. They encapsulate the logic within the component and offer more flexibility when performing additional validations or triggering custom methods.<br>
  
  > **Pros:** recieve parameter value and parse it immediately.<br>
      
  > **Cons:** no default values on getters and setters. <br>
   ```
   export class MyComponent {
   private _myProperty: string;
 
   @Input()
   set myProperty(value: string) {
     this._myProperty = value;
     this.onMyPropertyChange();
   }
 
   get myProperty(): string {
     return this._myProperty;
   }
   ```
  
  ## 4. NgOnChanges
  > **Pros:** If your component has several inputs, you will get all changes for all the inputs at once within ngOnChanges(). Using this approach, you can also compare current and previous values of the input that has changed and take actions accordingly.
    
  > **Cons:** no default values on getters and setters. <br>
   ```
   @Input() categoryId: string;
        
    ngOnChanges(changes: SimpleChanges) {
        
        this.doSomething(changes.categoryId.currentValue);
        // You can also use categoryId.previousValue and 
        // categoryId.firstChange for comparing old and new values
        
    }
   ```

# Working with Data Streams, where you expect the value to change
 # 2. Signals vs. Observables
 source: https://youtu.be/4FkFmn0LmLI?si=cjbQglPLpcG1auma
 <br>
 Signals are good for Synchronous Reactivity. They are not good for Asynchronous Reactivity.
 Signals do not suffer from the Diamond Problem in which changes in the value are computed one at a time, instead of simultaneously. 
 <br>
 ![change detection must reprocess every affected asset step by step.](./diamond_step1.png)
 ![](./diamond_step2.png)

   ## Asynchronous reactivity
   > Asynchronous reactivity – we must wait some time to set a new value. Maybe this is due to an API etc.  
   
  Asynchronous operations can be:  
  - Events - User events like click, change, input, submit, …
  - XMLHttpRequests - E.g. when fetching data from a remote service
  - Timers - setTimeout(), setInterval()
  
   ## Synchronous reacitvity
   > Synchronous Reactivity – a value is known and can be set immediately.
   Signal, Variables, standrard functions 
# 3.  Subscriptions

  

