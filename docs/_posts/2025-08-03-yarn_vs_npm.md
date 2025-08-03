---
layout: post
title:  "Yarn vs. npm: Installation Inefficiencies"
date:   2025-08-03 00:00:00 +0900
categories: jekyll update
---
# Yarn vs. npm: Installation Inefficiencies

This topic focuses on the historical inefficiencies of `npm` (Node Package Manager) compared to `yarn` (Yet Another Resource Negotiator), particularly regarding package installation.

## npm (Node Package Manager)
*   **History:** The default package manager for Node.js, and the first widely adopted solution for managing JavaScript packages.
*   **Installation Process (Older npm versions - pre-npm 5):
    *   **Sequential Installation:** Packages were often installed one after another. For projects with many dependencies, this led to significantly longer installation times.
    *   **Flat `node_modules` (with hoisting):** npm aimed to create a flat `node_modules` structure to reduce duplication. However, this could lead to "hoisting" issues where dependencies were moved up the tree, potentially causing problems if different versions of the same package were required by different parts of the dependency tree.
    *   **Lack of Lockfile Enforcement:** Before `package-lock.json` became standard and enforced (npm 5+), `npm install` could result in different dependency trees on different machines, leading to "it works on my machine" issues.
    *   **Network Dependency:** Every `npm install` typically involved network requests to fetch packages, even if they were already present in a global cache.

## Yarn
*   **History:** Developed by Facebook (now Meta) in 2016 to address some of the performance and reliability issues experienced with npm at the time.
*   **Key Improvements (compared to older npm):
    *   **Parallel Installation:** Yarn was designed to install packages in parallel, significantly speeding up the installation process, especially for projects with many dependencies.
    *   **Deterministic Installs (`yarn.lock`):** Yarn introduced `yarn.lock` (similar to `package-lock.json` later adopted by npm) to ensure that every installation results in the exact same `node_modules` tree across different environments. This solved the "it works on my machine" problem.
    *   **Offline Mode/Caching:** Yarn had a more robust caching mechanism, allowing it to install packages from a local cache without a network connection if they had been downloaded before.
    *   **Improved Output:** Yarn's CLI output was often considered cleaner and more readable.

## How npm Addressed Inefficiencies (npm 5+):
It's important to note that npm has significantly improved since Yarn's initial release.
*   **`package-lock.json`:** npm 5 introduced `package-lock.json`, providing deterministic installs, similar to `yarn.lock`.
*   **Improved Performance:** Later versions of npm have also implemented parallel installation and better caching strategies, narrowing the performance gap with Yarn.
*   **`npx`:** A tool for executing npm packages, often used for running CLI tools without globally installing them.

## Current State (npm vs. Yarn today):
*   The performance differences between npm and Yarn (especially npm 5+ and Yarn Classic) are often negligible for most projects.
*   Both have robust lockfile mechanisms.
*   Both have good caching.
*   Yarn still offers some advanced features like Workspaces (for monorepos) and Plug'n'Play (PnP), which npm has also started to address with its own solutions.
*   The choice often comes down to team preference, existing project setup, and specific features required.

## Inefficiencies (Summary of older npm issues that Yarn addressed):
The "inefficient installation" often refers to:
1.  **Sequential nature of older npm installs:** Leading to longer wait times.
2.  **Lack of deterministic installs:** Causing inconsistencies across environments.
3.  **Less robust caching:** More reliance on network for repeated installs.