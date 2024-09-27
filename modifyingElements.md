@Viewchild
export class HomeComponent implements OnInit {

    @ViewChild('slideBg') el:ElementRef;

    ngAfterViewInit {
        // style can be changed
        this.el.nativeElement.style.background = 'red';
        // changes class
        this.el.nativeElement.className = 'myCSSclass';
        // does not work:
        this.el.nativeElement.class = 'myCSSclass';
    }

    …

}

ElementRef
Class binding
