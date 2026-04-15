# Shopify | Vite/React | Tailwind - Dev Stack

## Prerequisites

You most already have a working environment w/ Node(NVM 22), NPM, Git & Shopify-cli.
If not, please install them first.

## Installation

### Step 1: Create a Vite Project

1. Initialize Vite Project

```sh
npm create vite@latest
```

- _NOTE:_ Select React and choose Javascript or Typescript as per your preference.

2. Navigate to Project Directory and Install Dependencies

```sh
cd project-name
npm install
npm run dev
```

### Step 2: Pull Shopify Theme

1. Download Shopify's theme file

```sh
shopify theme pull --nodelete
```

- _NOTE:_ Download your starting point (it should be already installed on your store)

### Step 3: Install Tailwind CSS

```sh
npm install --save-dev tailwindcss @tailwindcss/cli postcss autoprefixer
npx tailwindcss init -p
npm install --save-dev npm-run-all
```

### Step 4: Configure Vite

1. Update the ./vite.config.js

```js
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [react()],
  build: {
    rollupOptions: {
      input: {
        counter: "src/main.jsx",
      },
      output: {
        dir: "assets",
        entryFileNames: "vite-[name].js",
        chunkFileNames: "vite-[name].js",
        assetFileNames: "vite-[name].[ext]",
      },
    },
    watch: {},
    emptyOutDir: false,
  },
});
```

- _NOTE:_ update the file input to be reflrected on the assets folders.

### Step 5: Configure Tailwind CSS

1. Update the tailwind.config.js file

```js
/** @type {import('tailwindcss').Config} */
module.exports = {
  content: [
    "./blocks/*.liquid",
    "./blocks/*.json",
    "./config/*.json",
    "./layout/*.liquid",
    "./assets/*.liquid",
    "./sections/*.liquid",
    "./snippets/*.liquid",
    "./templates/*.liquid",
    "./templates/*.json",
    "./templates/customers/*.liquid",
  ],
  theme: {
    screens: {
      sm: "320px",
      md: "750px",
      lg: "990px",
      xl: "1200px",
      xxl: "1400px",
    },
    extend: {},
  },
  plugins: [],
};
```

- _NOTE 1:_ Update the content to reflect the liquid files that you are using on your project.
- _NOTE 2:_ the screens are the default screens that you can use on your project/used by Dawn Dev team too.

### Step 6: Create Tailwind CSS File

1. Create tailwindcss file

```sh
touch src/tailwind.css
```

2. Add the next info

```css
@import "tailwindcss" prefix(tw);
```

- _NOTE:_ The prefix (tw) is the prefix that you want to use on your project. Classes will be prefixed with 'tw:' > `tw:border-8 tw:border-red-500`

### Step 7: Build Tailwind CSS

1. From project folder, create tailwind build process

```sh
npx @tailwindcss/cli -i ./src/tailwind.css -o ./assets/tailwind.css
```

### Step 8: Update `package.json` Scripts

1. Add scripts

```json
{
  "scripts": {
    "dev": "run-p -sr dev:shopify build:vite dev:tailwind",
    "dev:full": "run-p -sr dev:shopify-sync build:vite dev:tailwind dev:gulpcss",
    "dev:gulpcss": "gulp watch",
    "dev:shopify": "shopify theme dev",
    "dev:shopify-sync": "shopify theme dev --theme-editor-sync",
    "dev:tailwind": "npx @tailwindcss/cli -i ./src/tailwind.css -o ./assets/tailwind.css -w",
    "dev:vite": "vite",
    "build": "run-p -sr build:css build:gulpcss build:vite",
    "build:css": "npx @tailwindcss/cli -i ./src/tailwind.css -o ./assets/tailwind.css -m",
    "build:deploy": "run-p -sr build:css build:gulpcss && run-p -sr build:push",
    "build:gulpcss": "gulp minify-css",
    "build:push": "shopify theme push",
    "build:vite": "vite build",
    "lint": "eslint src --ext js,jsx --report-unused-disable-directives --max-warnings 0",
    "preview:vite": "vite preview"
  }
}
```

### Step 9: Install Scripts (if missed)

1. Install dependencies

```sh
npm install
```

### Step 10: Update Shopify Project Files

1. Update theme.liquid for Tailwind CSS

```liquid
{% comment %} Tailwind CSS Configs {% endcomment %}
{{ 'tailwind.css' | asset_url | stylesheet_tag }}
```

- Just before the </head> tag

2. Update theme.liquid for Alpine.js

```liquid
{% comment %} Alpine JS {% endcomment %}
<script
  defer
  src="https://cdn.jsdelivr.net/npm/alpinejs@3.13.1/dist/cdn.min.js"
></script>
```

Or use the local version via Vite

```sh
npm install --save-dev alpinejs
```

Create a new file in the src folder called `alpine.js` and add the following code:

```sh
touch src/alpine.js
```

```js
import Alpine from "alpinejs";
window.Alpine = Alpine;
Alpine.start();
```

Then update the vite.config.js file to include the new file:

```js
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [react()],
  build: {
    rollupOptions: {
      input: {
        counter: "src/main.jsx",
      },
      external: {
        alpine: "src/alpine.js",
      },
      output: {
        dir: "assets",
        entryFileNames: "vite-[name].js",
        chunkFileNames: "vite-[name].js",
        assetFileNames: "vite-[name].[ext]",
      },
    },
    watch: {},
    emptyOutDir: false,
  },
});
```

```liquid
<script type="module" src="{{ 'vite-alpine.js' | asset_url }}"></script>
```

- Just before the </body> tag

### Step 11: Add Shopify Prettier Support

1. Install Prettier Plugin for Shopify

```ssh
npm install --save-dev prettier @shopify/prettier-plugin-liquid
```

2. Create .prettierrc file

```sh
touch .prettierrc
```

3. Add the following code to the .prettierrc file

```json
{
  "plugins": ["@shopify/prettier-plugin-liquid"],
  // testing
  "overrides": [
    {
      "files": "*.liquid",
      "options": {
        "parser": "liquid-parser"
      }
    }
  ]
}
```

4. Run Prettier on command

```ssh
npm run prettier -- path/to/file.liquid --write
```

- **NOTE:** Shopify Prettier Support [documentation](https://shopify.dev/docs/themes/tools/liquid-prettier-plugin)

## Shopify Dev Start

```sh
npm run dev
```

## Shopify Push (select theme to update)

```sh
npm run build:push
```

## Shopify Deploy

```sh
npm run build:deploy
```

- **NOTE:** You need to have an already theme created on the store id/theme just to be pushed

## Stack Testing

### Alpine.js Example

```html
<div x-data="{ open: false }">
  <button @click="open = !open">Expand</button>
  <span x-show="open"> Content... </span>
</div>
```

- _NOTE:_ This is a simple example of how a simple Alpine.js component works. Remove after testing.

### Vite React Configuration

[Youtube](https://www.youtube.com/watch?v=qVw1-k7kPEg)

1. Create a Vite/React Rendering file

```sh
touch snippets/framework-snippet.liquid
```

```liquid
{% render 'framework-snippet' %}
```

- _NOTE:_ To make the react files work they need to be rendered by a snippet. Where you want it to render.

2. Paste the contents

```liquid
{% comment %} React {% endcomment %}
<div id="message"></div>
<div id="root"></div>
<script type="module" src="{{ 'vite-message.js' | asset_url }}" defer></script>
<script type="module" src="{{ 'vite-counter.js' | asset_url }}" defer></script>
{% comment %} React build data {% endcomment %}
<script type="module" src="{{ 'vite-client.js' | asset_url }}" defer></script>
```

- _NOTE:_ The div's id is the one targeted by the react components. "main.jsx"
- _NOTE:_ the vite-client.js is the main file that will render the react components. Once you have at least 2 components.
- _NOTE:_ Review if I need to re-run the vite build process after the first time. (I think I do)

### Gulp Compressing CSS

1. Install gulp dependencies and create a gulp file

```sh
npm install gulp gulp-clean-css gulp-minify gulp-rename --save-dev
touch gulpfile.cjs
```

2. Paste the following code

```js
const gulp = require("gulp");
const cleanCSS = require("gulp-clean-css");
const minify = require("gulp-minify");
const rename = require("gulp-rename");

const cssFileNames = ["./assets/<file-name>.css"];
const jsFileNames = ["./assets/<file-name>.js"];

function minifyCSS() {
  return gulp
    .src(cssFileNames)
    .pipe(cleanCSS())
    .pipe(
      rename(function (path) {
        path.basename += ".min";
      }),
    )
    .pipe(gulp.dest("./assets/"));
}

gulp.task("minify-css", minifyCSS);

function minifyJS() {
  return gulp
    .src(jsFileNames)
    .pipe(
      minify({
        mangle: false,
        noSource: true, // This option prevents generating the original file copy
        ext: {
          min: ".min.js",
        },
      }),
    )
    .pipe(gulp.dest("./assets/"));
}

gulp.task("minify-js", minifyJS);

gulp.task("build", gulp.series(["minify-css", "minify-js"]));

gulp.task("watch", () => {
  gulp.watch(cssFileNames, minifyCSS);
  gulp
    .watch(jsFileNames, { ignoreInitial: false }, minifyJS)
    .on("change", function (event) {
      if (!event.endsWith(".min.js")) {
        minifyJS();
      }
    });
});
```

- _NOTE:_ Replace the <file-name> with the name of the file you want to minify.

---

Last updated: 20240718

When adding dotenv variables first install the package

```sh
npm install dotenv --save
```

Then add the following code to the vite.config.js file

```js
import dotenv from "dotenv";

// Load environment variables from .env file
dotenv.config();

export default defineConfig({
  plugins: [react()], // this should be already in file
  define: {
    "process.env": process.env,
  },
});
```

- _NOTE:_ All Vite .env variables should be prefixed with VITE\_ to be used on the client-side.
