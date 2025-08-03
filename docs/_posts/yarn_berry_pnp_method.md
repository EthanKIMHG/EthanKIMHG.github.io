# Yarn Berry - PnP Method

This topic specifically refers to **Yarn Berry's primary method of dependency management, which is built around the Plug'n'Play (PnP) strategy.**

Yarn Berry (also known as Yarn 2, 3, 4, etc., as it follows a continuous release cycle) fundamentally re-architected how Node.js dependencies are managed, moving away from the traditional `node_modules` directory.

## Core Concepts Revisited:

1.  **Plug'n'Play (PnP):**
    *   Instead of creating a `node_modules` folder, Yarn Berry generates a single `.pnp.cjs` (or `.pnp.js`) file.
    *   This file acts as a comprehensive map, telling Node.js exactly where to find every module and its dependencies within the Yarn cache.
    *   This eliminates the need for Node.js to traverse the filesystem to resolve modules, leading to faster startup times and more reliable dependency resolution.
    *   It strictly enforces declared dependencies, preventing "ghost dependencies."
    *   *For a deeper dive, refer to `PnP.md`.*

2.  **Compressed Files (Zip Archives):**
    *   Yarn Berry stores packages as compressed zip archives in a `.yarn/cache` directory.
    *   These archives are *not* extracted to `node_modules`.
    *   This significantly reduces disk space usage and speeds up installation times, as there are far fewer files to manage and I/O operations are minimized.
    *   *For more details, refer to `yarn_dependency_management_compressed_files.md`.*

## Why Yarn Berry uses PnP:

Yarn Berry adopted PnP as its default installation strategy to address long-standing issues with the `node_modules` approach, including:
*   **Slow Installation Times:** PnP, combined with zip archives, makes installations virtually instantaneous after the initial download.
*   **Large `node_modules` Size:** Eliminates the massive `node_modules` folder, saving significant disk space.
*   **Inconsistent Dependency Trees:** PnP ensures deterministic resolution, making builds more reliable across different environments.
*   **Ghost Dependencies:** By strictly controlling module resolution, PnP prevents accidental reliance on undeclared dependencies.

## How to use Yarn Berry with PnP:

To enable Yarn Berry and its PnP mode in a project, you typically run:

```bash
yarn set version stable
# or
yarn set version berry
```

After this, subsequent `yarn install` commands will use the PnP strategy.

## Tooling Compatibility:

While PnP offers significant advantages, it does change the fundamental way Node.js resolves modules. This means some older tools, IDEs, or build systems might require specific plugins or configurations to work seamlessly with Yarn Berry's PnP mode. However, the Yarn team provides extensive documentation and integrations for popular tools (e.g., VS Code, TypeScript, Webpack).

**In summary, "Yarn Berry - PnP method" refers to the modern, efficient, and strict dependency management system that Yarn Berry employs, leveraging the PnP resolution strategy and compressed package archives to deliver faster, smaller, and more reliable builds.**
