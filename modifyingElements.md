# Methods for modifying classes and styles of a component
## @Viewchild
```
export class HomeComponent implements OnInit {

    @ViewChild('slideBg') el:ElementRef;

    ngAfterViewInit {
        // style can be changed
        this.el.nativeElement.style.background = 'red';
        // changes class
        this.el.nativeElement.className = 'myCSSclass';
        // does not work:
        this.el.nativeElement.class = 'myCSSclass';

        // adds a class to all the classList. Does not require Change Detection
        this.el.nativeElement.classList.toggle('icon-box--selected');

    }

    …

}
```
ElementRef
Class binding
