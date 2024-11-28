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

