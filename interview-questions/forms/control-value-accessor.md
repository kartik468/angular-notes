When creating forms in Angular, sometimes you want to have an input that isn’t a standard text input, select, or checkbox. By implementing the ControlValueAccessor interface and registering the component as a NG_VALUE_ACCESSOR, you can integrate your custom form control seamlessly into template driven or reactive forms just as if it were a native input!

https://alligator.io/angular/custom-form-control/

# Custom Form Control

In order to make the RatingInputComponent behave as though it were a native input (and thus, a true custom form control), we need to tell Angular how to do a few things:

Write a value to the input - writeValue

Register a function to tell Angular when the value of the input changes - registerOnChange

Register a function to tell Angular when the input has been touched - registerOnTouched

Disable the input - setDisabledState

These four things make up the ControlValueAccessor interface, the bridge between a form control and a native element or custom input component. Once our component implements that interface, we need to tell Angular about it by providing it as a NG_VALUE_ACCESSOR so that it can be used.

With these changes, our new RatingInputComponent now looks something like this:

```typescript
// rating-input.component.ts
import { Component, forwardRef, HostBinding, Input } from '@angular/core';
import { ControlValueAccessor, NG_VALUE_ACCESSOR } from '@angular/forms';

@Component({
    selector: 'rating-input',
    template: `
        <span *ngFor="let starred of stars; let i = index" (click)="onTouched(); rate(i + (starred ? (value > i + 1 ? 1 : 0) : 1))">
            <ng-container *ngIf="starred; else noStar">⭐</ng-container>
            <ng-template #noStar>·</ng-template>
        </span>
    `,
    styles: [
        `
            span {
                display: inline-block;
                width: 25px;
                line-height: 25px;
                text-align: center;
                cursor: pointer;
            }
        `
    ],
    providers: [
        {
            provide: NG_VALUE_ACCESSOR,
            useExisting: forwardRef(() => RatingInputComponent),
            multi: true
        }
    ]
})
export class RatingInputComponent implements ControlValueAccessor {
    stars: boolean[] = Array(5).fill(false);

    // Allow the input to be disabled, and when it is make it somewhat transparent.
    @Input() disabled = false;
    @HostBinding('style.opacity')
    get opacity() {
        return this.disabled ? 0.25 : 1;
    }

    // Function to call when the rating changes.
    onChange = (rating: number) => {};

    // Function to call when the input is touched (when a star is clicked).
    onTouched = () => {};

    get value(): number {
        return this.stars.reduce((total, starred) => {
            return total + (starred ? 1 : 0);
        }, 0);
    }

    rate(rating: number) {
        if (!this.disabled) {
            this.writeValue(rating);
        }
    }

    // Allows Angular to update the model (rating).
    // Update the model and changes needed for the view here.
    writeValue(rating: number): void {
        this.stars = this.stars.map((_, i) => rating > i);
        this.onChange(this.value);
    }

    // Allows Angular to register a function to call when the model (rating) changes.
    // Save the function as a property to call later here.
    registerOnChange(fn: (rating: number) => void): void {
        this.onChange = fn;
    }

    // Allows Angular to register a function to call when the input has been touched.
    // Save the function as a property to call later here.
    registerOnTouched(fn: () => void): void {
        this.onTouched = fn;
    }

    // Allows Angular to disable the input.
    setDisabledState(isDisabled: boolean): void {
        this.disabled = isDisabled;
    }
}
```
