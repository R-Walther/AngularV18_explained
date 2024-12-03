# Signals
## Writing Signals
To change the value of a writable signal, either .set() it directly:
```
count.set(3);
```
check
or use the .update() operation to compute a new value from the previous one:
```
// Increment the count by 1.
count.update(value => value + 1);
```
check
Writable signals have the type WritableSignal.

## Responding to & modifying Signal Changes
### Compute
```
const count: WritableSignal<number> = signal(0);
const doubleCount: Signal<number> = computed(() => count() * 2);
```
check
The doubleCount signal depends on the count signal. Whenever count updates, Angular knows that doubleCount needs to update as well.

- Computed signals are not writable signals
- signals assigned to variables are updated automatically. Be careful, that any conversion to the signal will only give you the first value and not update. Use Compute for this
  ```
  //does not work
   protected activeContextID: ContextID = this.contextService.$activeContext().contextID;
  //correct
  protected activeContextID: ContextID = computed(() => {
    return this.contextService.$activeContext().contextID;
  })();
  ```
### Effect
 An effect is an operation that runs whenever one or more signal values change
 ```
 effect(() => {
  console.log(`The current count is: ${count()}`);
});
```
- Effects always run at least once.
- When an effect runs, it tracks any signal value reads. Whenever any of these signal values change, the effect runs again. 
- Effects always execute asynchronously, during the change detection process.

  Write Effect inside of the constructor:
```
  @Component({...})
export class EffectiveCounterComponent {
  readonly count = signal(0);
  constructor() {
    // Register a new effect.
    effect(() => {
      console.log(`The count is: ${this.count()}`);
    });
  }
}
```
You can prevent a signal read from being tracked by calling its getter with untracked:
```
effect(() => {
  console.log(`User set to ${currentUser()} and the counter is ${untracked(counter)}`);
});
```

## Signal Operators
### Observables to Signals 
```
const count$ = increment$.pipe(scan(n => n + 1, 0));

const count = toSignal(count$);
```
toSignal(source$) immediately subscribes to the source$ observable, and stays subscribed until it is destroyed. This is necessary because signals are expected to always contain a current value that can be read from at any time. However, it blocks the ability of RxJS streams to automatically

Problems:
#### clean up :
_toSignal_ in a shared service, the source observable will never clean up resources.
#### reset and re-fetch :
Apps that don't use RxJS effectively will have extra code that runs inside an OnDestroy to manually clear stale data. They also have to manually trigger a re-fetch.
if you call toSignal in a shared service, the source observable will never reset its state or re-fetch data
#### cancel requests
#### defer work and requests :
Observables can represent data sources but wait for subscribers instead of prematurely consuming them.
But if you call toSignal in a shared service, the source observable will consume the data source it represents as soon as that service is created:


## Input Data Handling
### Inputs, Outputs, Viewchild and others have a new syntax that automatically converts them to Signals
```
mainContainer : Signal<ElementRef<any> | undefined> =  viewChild<ElementRef>('blurredBg');
//input signal
value = input<number>(0);
//ouput signal
panelClosed = output<void>();
```


### Input
The input function returns an InputSignal. You can read the value by calling the signal:
```
import {Component, input} from '@angular/core';
@Component({/*...*/})
export class CustomSlider {
  // Declare an input named 'value' with a default value of zero. 
  value = input(0);
  // Create a computed expression that reads the value input
  label = computed(() => `The slider's value is ${this.value()}`);
  // Declare a required input named value. Returns an `InputSignal<number>`.
  value = input.required<number>();

}
```
Signals created by the input function are read-only.
### Model
Model inputs are a special type of input that enable a component to propagate new values back to its parent component.
```
@Component({ /* ... */})
export class CustomSlider {
  // Define a model input named "value".
  value = model(0);
  increment() {
    // Update the model input with a new value, propagating the value to any bindings. 
    this.value.update(oldValue => oldValue + 10);
  }
}
@Component({
  /* ... */
  // Using the two-way binding syntax means that any changes to the slider's
  // value automatically propagate back to the `volume` signal.
  // Note that this binding uses the signal *instance*, not the signal value.
  template: `<custom-slider [(value)]="volume" />`,
})
export class MediaControls {
  // Create a writable signal for the `volume` local state. 
  volume = signal(0);
}
```
In the above example, the **CustomSlider** can write values into its value model input, which then propagates those values back to the volume signal in **MediaControls**. This binding keeps the values of value and volume in sync. Notice that the binding passes the volume signal instance, not the value of the signal.
_______
When you declare a model input in a component or directive, Angular automatically creates a corresponding output for that model. The output's name is the model input's name suffixed with "Change".
```
@Directive({ /* ... */ })
export class CustomCheckbox {
  // This automatically creates an output named "checkedChange".
  // Can be subscribed to using `(checkedChange)="handler()"` in the template.
  checked = model(false);
}
check
Angular emits this change event whenever you write a new value into the model input by calling its set or update methods.
```
