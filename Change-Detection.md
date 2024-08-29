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
   ## Asynchronous reactivity
   ## Synchronous reacitvity
# 3.  Subscriptions
# 4. Inputs
  ## Input getters and Setters
  reading: https://blog.bitsrc.io/simplify-input-property-handling-in-angular-with-setters-and-getters-256ddca84ac1
    Setters and getters provide a simpler and more controlled way to handle input property changes. They encapsulate the logic within the component and offer more flexibility when performing additional validations or triggering custom methods.
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
