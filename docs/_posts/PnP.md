---
layout: post
title:  "Plug'n'Play (PnP)"
date:   2025-08-03 00:00:00 +0900
categories: jekyll update
---
# Plug'n'Play (PnP)

**What it is:**
Plug'n'Play (PnP) is an innovative installation strategy introduced by Yarn (specifically Yarn Berry, also known as Yarn 2+). It aims to fundamentally change how Node.js resolves modules, moving away from the traditional `node_modules` directory structure and instead using a single `.pnp.cjs` file (or `.pnp.js` for older versions) to map module resolutions.

**Why PnP was created (Problems it solves):**
Traditional `node_modules` installations, while widely used, have several drawbacks:
1.  **Large `node_modules` size:** Deeply nested dependency trees can lead to extremely large `node_modules` folders, consuming significant disk space and slowing down file operations (e.g., copying, deleting, indexing).
2.  **Slow Installation Times:** Even with optimizations like hoisting and caching, the sheer number of files and directories in `node_modules` can make installation and `node_modules` manipulation slow.
3.  **Hoisting Issues & Ghost Dependencies:** As discussed in the previous topic, hoisting can lead to non-deterministic `node_modules` layouts and the problem of "ghost dependencies" (or phantom dependencies), where code implicitly relies on packages not explicitly declared in `package.json`.
4.  **Symlink Overhead:** Some package managers use symlinks extensively, which can sometimes cause issues on different operating systems or with certain tools.

**How PnP Works:**
Instead of creating a complex `node_modules` directory structure, PnP generates a single `.pnp.cjs` file. This file is essentially a large JavaScript file that contains a map of all your project's dependencies and their exact locations within the Yarn cache (which is typically a global or project-level `.yarn/cache` directory).

When Node.js tries to `require()` or `import` a module, Yarn intercepts the resolution process. It uses the `.pnp.cjs` file to look up the exact path to the requested module directly from the cache, bypassing the need to traverse `node_modules` directories.

**Key Features and Benefits of PnP:**
*   **Instant Installs:** After the initial download of packages into the Yarn cache, subsequent installations are virtually instantaneous because there's no `node_modules` folder to create or manipulate. It's just a matter of generating the `.pnp.cjs` file.
*   **Reduced Disk Space:** No more massive `node_modules` folders. Packages are stored in a compressed, deduplicated cache.
*   **Strict Dependency Resolution:** PnP enforces strict dependency resolution. If your code tries to import a package that is not explicitly declared in your `package.json` (or a direct dependency of one of your declared dependencies), it will fail. This eliminates ghost dependencies and makes your dependency graph more predictable.
*   **Faster Startup Times:** Node.js doesn't need to traverse the file system to resolve modules, leading to potentially faster application startup times.
*   **Improved Reliability:** The deterministic nature of PnP (guaranteed by the `.pnp.cjs` file and `yarn.lock`) ensures that your project will behave consistently across different environments.
*   **Better Monorepo Support:** PnP works very well with Yarn Workspaces for monorepos, further optimizing dependency management across multiple packages.

**Challenges and Considerations:**
*   **Tooling Compatibility:** Because PnP fundamentally changes module resolution, some older tools, IDEs, or build systems might not be immediately compatible. However, Yarn provides integration plugins for popular tools (e.g., VS Code, Webpack, TypeScript).
*   **Learning Curve:** Developers accustomed to `node_modules` might find the PnP approach initially unfamiliar.
*   **Debugging:** Debugging module resolution issues can be different, as you're not inspecting a physical `node_modules` folder.

**Enabling PnP:**
PnP is the default installation strategy for Yarn Berry (Yarn 2+). You can initialize a project with Yarn Berry using `yarn set version berry` or `yarn set version stable`.

**In summary, PnP is a powerful innovation that addresses many long-standing issues with Node.js package management by rethinking how modules are resolved, leading to faster, smaller, and more reliable dependency management.**