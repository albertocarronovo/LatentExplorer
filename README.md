# session06

## Project submission

### Create your own Github branch
Clone this repository and create new branch (from main) named with your name. 
Do not edit any existing files in this repository. That might cause conflicts when trying to merge your branch back to main branch.

### Run project locally
In terminal, run `npm i` or `npm install` to install packages.
To run project with Rhino.Compute, start `rhino.compute.exe` and type `npm run serve` in terminal.

### Add your submission files
1. Add new folder inside of `.src/submission/` folder with your name. 

Your submission folder structure

    /src/submissions/yourfolder
    ├── {yourname}.vue              # Main Vue component with your submission
    ├── components/                 # Folder for your own components
      ├── YourCustomComponent.vue   # All your Vue components
    ├── assets/                     # Automated tests (alternatively `spec` or `tests`)
       ├── definition.gh            # Your grasshopper definition
       ├── other files              # If you are using other files as images, music etc. place it in assets folder.


2. Place your project inside of this folder. Folder should contain one main Vue component named {yourname}.vue. 
3. All other components should be placed inside of `.src/submission/{yourfoldername}/components` folder. 
4. Other assets like images, Grasshopper definitions place inside `.src/submission/{yourfoldername}/assets` folder.

Once you are ready to submit your assignment, select `Create Pull Request` in Github Desktop. 

### Using common Vue components
In folder `.src/components` you can find Vue components with Slider, Toggle etc. If you want to use those components
inside of you project, import them using this format:
` import {ComponentName} from "commoncomponents/{ComponentName}.vue`

For example, to import `ToggleInput` write:
```
import ToggleInput from "commoncomponents/ToggleInput.vue";
```

This template should help get you started developing with Vue 3 in Vite.

## Recommended IDE Setup

[VSCode](https://code.visualstudio.com/) + [Volar](https://marketplace.visualstudio.com/items?itemName=Vue.volar) (and disable Vetur) + [TypeScript Vue Plugin (Volar)](https://marketplace.visualstudio.com/items?itemName=Vue.vscode-typescript-vue-plugin).

## Customize configuration

See [Vite Configuration Reference](https://vitejs.dev/config/).

## Project Setup

```sh
npm install
```

### Compile and Hot-Reload for Development

```sh
npm run dev
```

### Compile and Minify for Production

```sh
npm run build
```

### Lint with [ESLint](https://eslint.org/)

```sh
npm run lint
```
