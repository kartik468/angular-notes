What are Attribute Directives? How will you implement event handling in Attribute Directive?
How to pass parameters to attribute directive?
Directives for Element ref(@HostListener)

# Directives

You use Attribute directives when you want to change appearance or behavior of the DOM element.

There are three kinds of directives in Angular:

1. Components—directives with a template.
2. Structural directives—change the DOM layout by adding and removing DOM elements.
3. Attribute directives—change the appearance or behavior of an element, component, or another directive.

Components are the most common of the three directives. You saw a component for the first time in the Getting Started tutorial.

Structural Directives change the structure of the view. Two examples are NgFor and NgIf. Learn about them in the Structural Directives guide.

Attribute directives are used as attributes of elements. The built-in NgStyle directive in the Template Syntax guide, for example, can change several element styles at the same time.

e.g. Highlight directive

```typescript
// app/highlight.directive.ts
import { Directive, ElementRef, HostListener, Input } from '@angular/core';

@Directive({
    selector: '[appHighlight]'
})
export class HighlightDirective {
    constructor(private el: ElementRef) {}

    @Input() defaultColor: string;

    @Input('appHighlight') highlightColor: string;

    @HostListener('mouseenter') onMouseEnter() {
        this.highlight(this.highlightColor || this.defaultColor || 'red');
    }

    @HostListener('mouseleave') onMouseLeave() {
        this.highlight(null);
    }

    private highlight(color: string) {
        this.el.nativeElement.style.backgroundColor = color;
    }
}
```

```html
<!-- app/app.component.html -->
<h1>My First Attribute Directive</h1>

<h4>Pick a highlight color</h4>
<div>
    <input type="radio" name="colors" (click)="color='lightgreen'" />Green <input type="radio" name="colors" (click)="color='yellow'" />Yellow
    <input type="radio" name="colors" (click)="color='cyan'" />Cyan
</div>
<p [appHighlight]="color">Highlight me!</p>

<p [appHighlight]="color" defaultColor="violet">
    Highlight me too!
</p>
```

## HostListener

The following example declares a directive that attaches a click listener to a button and counts clicks.

```typescript
@Directive({ selector: 'button[counting]' })
class CountClicks {
    numberOfClicks = 0;

    @HostListener('click', ['$event.target'])
    onClick(btn) {
        console.log('button', btn, 'number of clicks:', this.numberOfClicks++);
    }
}

@Component({
    selector: 'app',
    template: '<button counting>Increment</button>'
})
class App {}
```

The following example registers another DOM event handler that listens for key-press events.

```typescript
import { HostListener, Component } from '@angular/core';

@Component({
    selector: 'app',
    template: `
        <h1>Hello, you have pressed keys {{ counter }} number of times!</h1>
        Press any key to increment the counter. <button (click)="resetCounter()">Reset Counter</button>
    `
})
class AppComponent {
    counter = 0;
    @HostListener('window:keydown', ['$event'])
    handleKeyDown(event: KeyboardEvent) {
        this.counter++;
    }
    resetCounter() {
        this.counter = 0;
    }
}
```
