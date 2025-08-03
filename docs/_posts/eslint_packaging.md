# Packaging ESLint (Creating and Distributing ESLint Configurations/Plugins)

"Packaging ESLint" generally refers to the process of creating and distributing reusable ESLint configurations, plugins, or shareable configs. This is useful for maintaining consistent code quality across multiple projects or within an organization, without having to copy-paste `.eslintrc` files.

## Why Package ESLint?

*   **Consistency:** Enforce a uniform coding style and set of rules across all projects.
*   **Reusability:** Avoid duplicating ESLint configurations in every new project.
*   **Maintainability:** Update rules or configurations in one central place and have changes propagate to all consuming projects.
*   **Shareability:** Easily share best practices with other developers or the open-source community.

## What Can Be Packaged?

1.  **Shareable Configurations:** A set of ESLint rules and configurations that can be extended by other projects.
2.  **ESLint Plugins:** Packages that provide custom rules, processors, environments, or configurations for specific libraries, frameworks, or coding patterns.

## 1. Packaging a Shareable ESLint Configuration

A shareable configuration is an npm package that exports an ESLint configuration. Projects can then `extend` this configuration in their own `.eslintrc` files.

**Steps:**

1.  **Create a new npm project:**
    ```bash
mkdir eslint-config-mycompany
cd eslint-config-mycompany
npm init -y
    ```
    *Naming Convention:* Shareable configs typically start with `eslint-config-`.

2.  **Install ESLint as a `peerDependency`:**
    Your config will rely on ESLint, but you don't want to bundle it. Instead, declare it as a `peerDependency` so the consuming project must install it.
    ```bash
npm install --save-dev eslint
# Then manually add to peerDependencies in package.json
    ```
    `package.json` example:
    ```json
{
  "name": "eslint-config-mycompany",
  "version": "1.0.0",
  "main": "index.js",
  "peerDependencies": {
    "eslint": ">=7.0.0"
  },
  "devDependencies": {
    "eslint": "^8.0.0"
  }
}
    ```

3.  **Create the configuration file (`index.js` or similar):**
    This file will export the ESLint configuration object.

    ```javascript
// index.js
module.exports = {
  env: {
    browser: true,
    node: true,
    es2021: true,
  },
  extends: [
    'eslint:recommended',
    // You can extend other shareable configs here, e.g., 'plugin:react/recommended'
  ],
  parserOptions: {
    ecmaVersion: 'latest',
    sourceType: 'module',
  },
  rules: {
    // Define your custom rules here
    'indent': ['error', 2],
    'linebreak-style': ['error', 'unix'],
    'quotes': ['error', 'single'],
    'semi': ['error', 'always'],
    'no-unused-vars': ['warn', { 'argsIgnorePattern': '^' }],
  },
  // Add plugins if your config uses them
  // plugins: [
  //   'react'
  // ],
  // settings: {
  //   react: {
  //     version: 'detect'
  //   }
  // }
};
    ```

4.  **Publish to npm (or use privately):**
    ```bash
npm publish
    ```
    For private packages, you might use a private npm registry or GitHub Packages.

5.  **Consume in another project:**
    Install your shareable config:
    ```bash
npm install --save-dev eslint-config-mycompany
    ```
    Then, in the consuming project's `.eslintrc.json`:
    ```json
{
  "extends": [
    "mycompany" // refers to eslint-config-mycompany
  ]
}
    ```

## 2. Packaging an ESLint Plugin

An ESLint plugin provides custom rules, processors, or environments. It's more complex than a shareable config because it involves writing the logic for the rules themselves.

**Steps (High-Level):**

1.  **Create a new npm project:**
    ```bash
mkdir eslint-plugin-mycustomrules
cd eslint-plugin-mycustomrules
npm init -y
    ```
    *Naming Convention:* Plugins typically start with `eslint-plugin-`.

2.  **Install ESLint as a `peerDependency`:** Similar to shareable configs.

3.  **Structure the plugin:**
    A typical plugin structure looks like this:
    ```
eslint-plugin-mycustomrules/
├── package.json
├── index.js          // Main entry point
├── rules/            // Directory for custom rules
│   └── no-console-log.js
├── processors/       // Optional: for custom processors
├── environments/     // Optional: for custom environments
└── configs/          // Optional: for shareable configs within the plugin
    └── recommended.js
    ```

4.  **Implement custom rules:**
    Each rule is a JavaScript file that exports an object with `meta` (metadata) and `create` (a function that returns a visitor object for AST traversal).

    ```javascript
// rules/no-console-log.js
module.exports = {
  meta: {
    type: 'suggestion',
    docs: {
      description: 'Disallow the use of console.log',
      category: 'Best Practices',
      recommended: false,
      url: 'https://example.com/no-console-log',
    },
    fixable: 'code', // or 'whitespace' or null
    schema: [], // no options
  },
  create(context) {
    return {
      CallExpression(node) {
        if (node.callee.type === 'MemberExpression' &&
            node.callee.object.type === 'Identifier' &&
            node.callee.object.name === 'console' &&
            node.callee.property.type === 'Identifier' &&
            node.callee.property.name === 'log') {
          context.report({
            node: node,
            message: 'Unexpected console.log statement.',
            fix(fixer) {
              // Optional: provide a fix function
              return fixer.remove(node);
            }
          });
        }
      },
    };
  },
};
    ```

5.  **Export rules (and other components) in `index.js`:**

    ```javascript
// index.js
module.exports = {
  rules: {
    'no-console-log': require('./rules/no-console-log'),
  },
  configs: {
    recommended: {
      plugins: ['mycustomrules'], // refers to this plugin itself
      rules: {
        'mycustomrules/no-console-log': 'warn',
      },
    },
  },
  // processors: { ... },
  // environments: { ... },
};
    ```

6.  **Publish to npm:**
    ```bash
npm publish
    ```

7.  **Consume in another project:**
    Install your plugin:
    ```bash
npm install --save-dev eslint-plugin-mycustomrules
    ```
    Then, in the consuming project's `.eslintrc.json`:
    ```json
{
  "plugins": [
    "mycustomrules" // refers to eslint-plugin-mycustomrules
  ],
  "extends": [
    "plugin:mycustomrules/recommended" // uses a predefined config from your plugin
  ],
  "rules": {
    "mycustomrules/no-console-log": "error" // or enable individual rules
  }
}
    ```

Packaging ESLint configurations and plugins is a powerful way to standardize and streamline code quality efforts across multiple JavaScript projects.
