```
  constructor(public elementRef: ElementRef, private renderer: Renderer)
  {
          this.renderer.setElementClass(this.elementRef, 'class');
     // or 
  
              this.elementRef.nativeElement.classList.add('class');
  
  }
```

Using Renderer
Using addClass on Native element



## hostbinding
```
\:host
  @extend %dashboard-tile
  position: relative
  height: 120px
  min-height: unset
  transition: height 400ms ease-in-out

\:host.expanded
  height: 100%
```
```
  @HostBinding('class.expanded') get expandedClass(): boolean {
    return this.isSourceSelectOpened || this.isExpanded;
  }
```
## Working with Signal Hostbinding
In the Typescript:
```
@Component({
....
host: {
'[class..inactive]':'this.inactive'
  }
})
export class TestClass {
  inactive = input<boolean>(false);
}
```
