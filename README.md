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