# eslint-prettier-husky-lintstaged-commitlint-commitizen-template

This guide provides a step-by-step tutorial to integrate ESLint, Prettier, Husky, lint-staged, Commitlint, and Commitizen in a `create-react-app` project.

## Step 1: Create React App

Generate a new React application using `create-react-app`:

```bash
npm create-react-app eslint-prettier-husky-lintstaged-commitlint-commitizen-template
```

## Step 2: Install Prettier

Add Prettier as a development dependency:

```bash
npm install --save-dev --save-exact prettier
```

## Step 3: Configure Prettier

Create a Prettier configuration file:

```bash
echo {} > .prettierrc.json
```

Exclude specific directories from formatting by creating a `.prettierignore` file:

```plaintext
dist
build
coverage
```

Install the Prettier extension for your code editor and enable "Format on Save".

## Step 4: Install and Configure Husky and lint-staged

Install Husky and lint-staged:

```bash
npm install --save-dev husky lint-staged
```

Initialize Git and Husky:

```bash
npx husky-init && npm install
npm pkg set scripts.prepare="husky install"
```

Create a pre-commit hook to run lint-staged:

```bash
npx husky add .husky/pre-commit "npx lint-staged"
```

Configure `lint-staged` in your `package.json` to automatically format staged files:

```json
"lint-staged": {
  "**/*": "prettier --write --ignore-unknown"
},
```

## Step 5: Resolve ESLint and Prettier Conflicts

Install `eslint-config-prettier`:

```bash
npm install --save-dev eslint-config-prettier
```

Update ESLint configuration to avoid conflicts with Prettier:

```json
"eslintConfig": {
  "extends": [
    "react-app",
    "react-app/jest",
    "prettier"
  ]
},
```

## Step 6: Install and Configure Commitlint

Install `@commitlint/config-conventional` and `@commitlint/cli`:

```bash
npm install --save-dev @commitlint/config-conventional @commitlint/cli
```

Create a `commitlint.config.js`:

```bash
echo "module.exports = { extends: ['@commitlint/config-conventional'] }" > commitlint.config.js
```

Configure Husky to use commitlint:

```bash
npx husky add .husky/commit-msg "npx --no-install commitlint --edit $1"
```

test
error

```bash
echo "add commitlint first" | npx commitlint
```

success

```bash
echo "fix(package.json): add commitlint first" | npx commitlint
```

## Step 7: Install and Use Commitizen

Install Commitizen globally and set up the conventional changelog:

```bash
npm install -g commitizen
commitizen init cz-conventional-changelog --save-dev --save-exact
```

Use Commitizen for commits:

```bash
npx cz
```

Alternatively, use `git commit -m "your message"` for manual commits.

## Additional Resources

- [Setup Tutorial Video](https://www.youtube.com/watch?v=tmTajqVgkwI)
