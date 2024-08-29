 # 1. The Zone
 The **NgZone** helps in managing the execution context and provides a method called run() that triggers change detection. Here's an example:
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
 # 2. Signals vs. Observables
 source: https://youtu.be/4FkFmn0LmLI?si=cjbQglPLpcG1auma
 <br>
 Signals are good for Synchronous Reactivity. They are not good for Asynchronous Reactivity.
 Signals do not suffer from the Diamond Problem in which changes in the value are computed one at a time, instead of simultaneously. 
 

   ## Asynchronous reactivity
   > Asynchronous reactivity – we must wait some time to set a new value. Maybe this is due to an API etc.
   Ex. Search – due to serverside processing that may terminate at different moments while typing in the searchbar
   ## Synchronous reacitvity
   > Synchronous Reactivity – a value is known and can be set immediately.
# 3.  Subscriptions
# 4. Inputs
  ## Input getters and Setters
  reading: https://blog.bitsrc.io/simplify-input-property-handling-in-angular-with-setters-and-getters-256ddca84ac1
  <br> **Setters and getters** provide a simpler and more controlled way to handle input property changes. They encapsulate the logic within the component and offer more flexibility when performing additional validations or triggering custom methods.<br>
 
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

  
  ## Input OnPush
  ## NgOnChanges
