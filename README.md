### Setup Process
1. #### package installation

``` npx create-react-app folderName```

``` cd folderName```

``` yarn add concurrently wait-on cross-env```

``` yarn add electron --dev```

``` yarn add tailwindcss@npm:@tailwindcss/postcss7-compat postcss@^7 autoprefixer@^9```

``` yarn add @craco/craco```

2. #### add tailwind.config.js
```
module.exports = {
  mode: "jit",
  purge: ['./src/**/*.{js,jsx,ts,tsx}', './public/index.html'],
  theme: {
    extend: {},
  },
  variants: {
    extend: {},
  },
  plugins: [],
}
```

3. #### add craco.config.js
```
module.exports = {
    style: {
        postOptions: {
            plugins: [require('tailwindcss'), require('autoprefixer')]
        }
    }
}
```

4. #### update package.json :
```
"main": "public/electron.js",
"homepage": "./",
"scripts": {
    "start": "concurrently \"cross-env BROWSER=none cross-env TAILWIND_MODE=watch craco start\" \"wait-on http://localhost:3000 && electron .\"",
    "build": "craco build",
    "test": "craco test",
    "eject": "react-scripts eject"
  }
  ```

5. #### create public/electron.js:
```
const path = require('path');
const { app, BrowserWindow } = require('electron');
function createWindow() {
    const mainWindow = new BrowserWindow({
        width: 800,
        height: 600,
        minWidth: 800,
        minHeight: 600,
        webPreferences: {
            nodeIntegration: true
        }
    })
    if (app.isPackaged) {
        mainWindow.loadFile(path.join(__dirname, "../build/index.html"));
    }
    else {
        mainWindow.loadURL("http://localhost:3000");
    }
}
app.whenReady().then(createWindow);
app.on("window-all-closed", () => {
    if (process.platform !== "darwin") {
        app.quit();
    }
})
app.on("activate", () => {
    if (BrowserWindow.getAllWindows().length === 0) {
        createWindow();
    }
})
```

