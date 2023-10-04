```bash
npx @citolab/tspci@latest init
```

ga in subfolder met de naam van de PCI die je net aangemaakt hebt

```bash
npm run tspci -- add --target qbci
```

add qbci for the correct debugging template
add ci build with target qbci

```diff
  "scripts": {
-   "dev": "tspci --dev"
+   "dev": "tspci --dev qbci"
    "prod": "tspci",
    "tspci": "tspci",
+    "ci": "npm version patch && tspci --target qbci" 
  },
```

in index.ts , add the following

```index.ts```
```js
import { CES, CI } from "@citolab/tspci-qbci"; 
const ces: CES = window["CES"]
  ? window["CES"]
  : window.parent["CES"]
    ? window.parent["CES"]
    : console.log("no QTI player found");
```


```diff
constructor() {
- ctx && ctx.register(this);
+ this.getInstance(document.body, { properties: configProps, onready: () => { } }, ces.getResponse());
}
```

subscribe to the store in getInstance
```diff
  getInstance = (dom: HTMLElement, config: Configuration<PropTypes>, stateString: string) => {
...    
+    this.store.subscribe((a) => ces.setResponse(this.getState()))
...
  };
```

call new App on window onload
```diff
- export default new App();
+ window.onload = () => new App()
```