
# Recipe for CI

This repository is the result for the following steps
to go from creating a standard PCI with the tspci tooling and making minor changes to use the same tooling to create a CI.

```bash
npx @citolab/tspci@latest init
```
add qbci target in the subfolder
```bash
npm run tspci -- add --target qbci
```
add the target to the dev, and add a build target.
```diff
  "scripts": {
-   "dev": "tspci --dev"
+   "dev": "tspci --dev qbci"
    "prod": "tspci",
    "tspci": "tspci",
+    "ci": "npm version patch && tspci --target qbci" 
  },
```

The following are changes you have to make to the existing PCI example to communicate in a CI environment

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