---
layout: post
title:  "JSON Server"
date:   2025-08-03 00:00:00 +0900
categories: jekyll update
---
# JSON Server

**What it is:**
JSON Server is a lightweight, zero-configuration tool that allows you to quickly set up a full fake REST API. It's incredibly useful for frontend developers who need a backend API for prototyping, testing, or developing their applications without having to build a real backend from scratch.

**How it Works:**

JSON Server works by taking a single JSON file as input. This JSON file represents your database. Based on the structure of this JSON file, JSON Server automatically creates a set of RESTful endpoints (GET, POST, PUT, PATCH, DELETE) that you can interact with using standard HTTP requests.

**Example `db.json` file:**

```json
{
  "posts": [
    { "id": 1, "title": "json-server", "author": "typicode" }
  ],
  "comments": [
    { "id": 1, "body": "some comment", "postId": 1 }
  ],
  "profile": { "name": "typicode" }
}
```

From this `db.json`, JSON Server will automatically generate the following endpoints:

*   `GET /posts`
*   `GET /posts/1`
*   `POST /posts`
*   `PUT /posts/1`
*   `PATCH /posts/1`
*   `DELETE /posts/1`
*   `GET /comments`
*   `GET /profile`

And so on for each top-level key in your JSON file.

## Key Features and Use Cases:

1.  **Rapid Prototyping:** Quickly get a working API for your frontend application without waiting for backend development.
2.  **Frontend Development:** Allows frontend developers to work independently of backend teams, mocking API responses and testing UI components.
3.  **Testing:** Useful for writing integration tests or end-to-end tests where you need a predictable API to interact with.
4.  **Demonstrations:** Create quick demos or proof-of-concepts.
5.  **No Backend Knowledge Required:** You only need to know JSON to define your data structure.
6.  **Full RESTful Operations:** Supports all standard HTTP methods (GET, POST, PUT, PATCH, DELETE).
7.  **Filtering, Sorting, Pagination:** Supports query parameters for common operations:
    *   **Filtering:** `GET /posts?author=typicode`
    *   **Sorting:** `GET /posts?_sort=views&_order=asc`
    *   **Pagination:** `GET /posts?_page=1&_limit=10`
    *   **Full-text search:** `GET /posts?q=json`
8.  **Relationships:** Can handle relationships between resources (e.g., `GET /posts/1/comments`).
9.  **Custom Routes:** You can define custom routes in a `routes.json` file to map specific URLs to your resources.
10. **Middleware:** Can be used as a module in an Express.js application to add custom middleware.

## How to Use JSON Server:

1.  **Installation:**
    Install it globally (recommended for CLI usage) or as a dev dependency in your project.
    ```bash
npm install -g json-server
# or as a dev dependency
npm install --save-dev json-server
    ```

2.  **Create a `db.json` file:**
    Create a file named `db.json` (or any other name) in your project directory with your desired data structure.

    ```json
    // db.json
    {
      "todos": [
        { "id": 1, "title": "Learn JSON Server", "completed": false },
        { "id": 2, "title": "Build a Frontend App", "completed": true }
      ]
    }
    ```

3.  **Start the server:**
    Run JSON Server from your terminal, pointing it to your `db.json` file.
    ```bash
json-server --watch db.json
    ```
    By default, it will run on `http://localhost:3000`.

4.  **Access the API:**
    You can now make HTTP requests to your endpoints:
    *   `GET http://localhost:3000/todos`
    *   `GET http://localhost:3000/todos/1`
    *   `POST http://localhost:3000/todos` (with a JSON body)

## Advanced Usage:

*   **Custom Port:** `json-server --watch db.json --port 3001`
*   **Serving Static Files:** `json-server --watch db.json --static ./public`
*   **Using a JavaScript file:** You can use a `.js` file that exports an object instead of a `.json` file, allowing for dynamic data generation.
    ```javascript
    // db.js
    module.exports = () => {
      const data = { posts: [], comments: [], profile: {} }
      // Create 100 posts
      for (let i = 0; i < 100; i++) {
        data.posts.push({ id: i, title: `post ${i}`, views: i })
      }
      return data
    }
    ```
    Then run: `json-server --watch db.js`

JSON Server is an indispensable tool in a frontend developer's toolkit for efficient and independent development.