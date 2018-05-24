# Create Elements (Web Components) with Angular 6
```
npm i -g @angular/cli
ng new elements-demo --prefix custom
ng add @angular/elements
ng g component button --inline-style --inline-template -v Native
```

Button component should be
```ts
import { Input, Component, ViewEncapsulation, EventEmitter, Output } from '@angular/core';

@Component({
  selector: 'custom-button',
  template: `<button (click)="handleClick()">{{label}}</button>`,
  styles: [`
    button {
      border: solid 3px;
      padding: 8px 10px;
      background: #bada55;
      font-size: 20px;
    }
  `],
  encapsulation: ViewEncapsulation.Native
})
export class ButtonComponent {
  @Input() label = 'default label';
  @Output() action = new EventEmitter<number>();
  private clicksCt = 0;

  handleClick() {
    this.clicksCt++;
    this.action.emit(this.clicksCt);
  }
}
```

app module should be
```ts
import { BrowserModule } from '@angular/platform-browser';
import { NgModule, Injector } from '@angular/core';
import { createCustomElement } from '@angular/elements';

import { ButtonComponent } from './button/button.component';

@NgModule({
  declarations: [ButtonComponent],
  imports: [BrowserModule],
  entryComponents: [ButtonComponent]
})
export class AppModule {
  constructor(private injector: Injector) {
    const customButton = createCustomElement(ButtonComponent, { injector });
    customElements.define('custom-button', customButton);
  }

  ngDoBootstrap() {}
}
```

Add this to package.json
```json
"build": "ng build --prod --output-hashing=none",
"package": "cat dist/elements-demo/{runtime,polyfills,scripts,main}.js | gzip > elements.js.gz"
```


html in anywhere outside angular should look similar to this:

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <title>Custom Button Test Page</title>
  <script src="elements.js"></script>
</head>

<body>
  <custom-button label="First Value"></custom-button>

  <script>
    const button = document.querySelector('custom-button');
    button.addEventListener('action', (event) => {
      console.log(`"action" emitted: ${event.detail}`);
    })
    setTimeout(() => button.label = 'Second Value', 3000);

  </script>
</body>

</html>
```
