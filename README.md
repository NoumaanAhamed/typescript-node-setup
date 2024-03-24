## Production Ready Node.js + Typescript Setup

Install node

```sh
# installs NVM (Node Version Manager)
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash

# download and install Node.js
nvm install 21

# verifies the right Node.js version is in the environment
node -v # should print `v21.7.1`

# verifies the right NPM version is in the environment
npm -v # should print `10.5.0`
```

Install TS globally ( optional )
```sh
npm install --global typescript
```

Install pnpm ( better package manager )

```sh
curl -fsSL https://get.pnpm.io/install.sh | sh -
source /root/.bashrc
```
nvm setup ( optional )
```sh
echo "v20.10.0" > .nvmrc
nvm install
nvm use
node -v
# v20.10.0
```

Install volta ( JS tool manager ) 
```sh
curl https://get.volta.sh | bash
source /root/.bashrc
volta install node
```

Setup Working Directory
```
mkdir project
cd project
git init
pnpm init
touch .gitignore
volta pin node@20.11.1
```
Add in `.gitignore`
```
dist
node_modules
```

Install Dependencies 
```
pnpm add typescript
pnpm add -D @tsconfig/node20
touch tsconfig.json
pnpm add -D @types/node
```

Edit tsconfig.json
```.json
{
    "extends": "@tsconfig/node20/tsconfig.json",
    "compilerOptions": {
      "lib": [
        "ESNext"
      ],
      "rootDir": "src",
      "outDir": "dist"
    },
    "include": [
      "src"
    ],
    "exclude": [
      "node_modules",
      "**/*.test.ts"
    ],
  }
  ```

Configure VSCode Typescript version

inside `.vscode/settings.json` in root
```.json
{
  "typescript.tsdk": "node_modules/typescript/lib"
}
```

Test by `console.log('hello')` in `src/index.ts` by running
```sh
pnpm tsc
```

Defining build script
```sh
pnpm add -D @swc/cli @swc/core rimraf
```

adding scripts to `package.json`
```.jsonc
{
  // ...,
  "scripts": {
    "build": "rimraf dist && swc ./src -d dist --strip-leading-paths",
    "start": "node dist/index.js"
  }
}
```

configure swc in `.swcrc` (optional)

```.json
{
  "env": {
    "targets": {
      "node": 20
    }
  },
  "jsc": {
    "parser": {
      "syntax": "typescript"
    }
  },
  "module": {
    "type": "commonjs",
  },
  "sourceMaps": "inline"
}
```

Test the above setup
```sh
pnpm build && pnpm start
```

Setting up eslint and prettier

adding TS plugins for eslint and eslint for prettier
```sh
pnpm add --save-dev eslint @typescript-eslint/eslint-plugin @typescript-eslint/parser
pnpm add --save-dev prettier eslint-plugin-prettier eslint-config-prettier
```

inside `.eslintrc`
```.json
{
  "extends": [
    "eslint:recommended",
    "plugin:@typescript-eslint/recommended",
    "plugin:prettier/recommended"
  ],
  "parser": "@typescript-eslint/parser",
  "plugins": [
    "@typescript-eslint"
  ],
  "root": true
}
```
inside `.prettierrc`
```.json
{
    "printWidth": 120,
    "singleQuote": true,
    "semi": true
}
```

inside `.eslintignore` and `.prettierignore`
```
node_modules
dist
```

inside `package.json`
```.json
 "scripts": {
    "lint": "eslint . --ext .ts",
    "lint:fix": "eslint . --ext .ts --fix",
    "format": "prettier --write src/"
  },
```

run using `pnpm lint` or `pnpm format`

( use vscode extensions to show errors )

Setup simple express server
```sh
pnpm add express morgan
pnpm add -D @types/express @types/morgan
pnpm add -D nodemon ts-node
```

in `tsconfig.json`
```
"ts-node": {
      "swc": true
    },
 ```

in `src.index.ts`

```ts
import express from 'express';
import morgan from 'morgan';

const app = express();
app.use(morgan('dev'));

app.get('/', (req, res) => {
    res.json({ message: 'Hello World!..' });
});

const port = process.env.PORT || 3001;

app.listen(port, () => {
    console.log(`Server is running on port ${port}`);
});

```

in `package.json`
```.json
 "scripts": {
    "clean": "rimraf dist",
    "build": "pnpm clean && swc ./src -d dist --strip-leading-paths",
    "start": "node --env-file=.env dist/index.js",
    "build:start": "pnpm build && pnpm start",
    "dev": "nodemon --exec ts-node --watch src src/index.ts",
    "lint": "eslint . --ext .ts",
    "lint:fix": "eslint . --ext .ts --fix",
    "format": "prettier --write src/"
  },
```

create `.env` file and add `PORT=3000`

run using `pnpm build:start` or `pnpm dev`

