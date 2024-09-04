# Async Functions / promises

# Lifecycle hooks

# Observabes vs Subjects
- part of the Reactive Extensions for JavaScript (RxJS) library.
![Untitled design](https://github.com/user-attachments/assets/064a9e83-0fd2-4938-8cc7-f83e489e6adc)
## Observables
  - Observables provide support for passing messages between parts of your application.
  - Observables are a powerful feature used extensively in reactive programming to handle **asynchronous** operations and data streams.
  - Observables provide a way to **subscribe** to and receive notifications when new data or events are emitted, enabling you to react to changes in real-time.
  
  - Observables are declarative. You define a function for publishing values — the source — but that function is not executed until a consumer subscribes to the observable by calling the observable's subscribe method.
  - This subscriber then receives notifications from the observable until it completes, emits an error, or the consumer unsubscribes.

    ```
    //------------------Observables are unicast-----------------
    //observable
    let observable = new Observable<number>(ele =>
      ele.next(Math.random()))

    //first subscriber
    observable.subscribe(result => {
      this.first_subscriber_observable = result;
      console.log(result)
    })

    //second subscriber
    observable.subscribe(result => {
      this.second_subscriber_observable = result;
      console.log(result)
    })

    //third subscriber
    observable.subscribe(result => {
      this.thrid_subscriber_observable = result;
      console.log(result)
    })

    results outputs:
    Subscriber 1: 0.84659
    Subscriber 2: 0.4123
    Subscriber 3: 0.92318
    ```

    ```
    numbers$.subscribe({
      next: value => console.log('Observable emitted the next value: ' + value),
      error: err => console.error('Observable emitted an error: ' + err),
      complete: () => console.log('Observable emitted the complete notification')
    });
    ```
Note: Subscriptions should be unsubscribed on Components and types that are created and destroyed within the single-page application. Services do not need unsubscribe, as they exist throughtout the application.

## Subject
 - An RxJS Subject is a special type of Observable that allows values to be multicasted to many Observers. 
 - It is an object with the methods next(v), error(e), and complete(). To feed a new value to the Subject, just call next(theValue), and it will be multicasted to the Observers registered to listen to the Subject.
 - It does not have an initial value, so subscribers only receive values emitted after they subscribe.
```
import { Subject, from } from 'rxjs';
 
const subject = new Subject<number>();
 
subject.subscribe({
  next: (v) => console.log(`observerA: ${v}`),
});
subject.subscribe({
  next: (v) => console.log(`observerB: ${v}`),
});
 
const observable = from([1, 2, 3]);
 
observable.subscribe(subject); // You can subscribe providing a Subject
 
// Logs:
// observerA: 1
// observerB: 1
// observerA: 2
// observerB: 2
// observerA: 3
// observerB: 3
```
### BehaviorSubject
### ReplaySubject
 <br>
https://www.learnrxjs.io/learn-rxjs/subjects/behaviorsubject <br>
next, error handeling etc <br>
https://medium.com/@jaydeepvpatil225/observables-and-subjects-in-angular-a4d73dfa5bb <br>
Event Emmiter vs Subject : https://stackoverflow.com/questions/35869406/angular2-detect-change-in-service <br>

