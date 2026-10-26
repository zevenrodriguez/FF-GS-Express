# Node.js + Express + hbs Starter

**CIM 343/643 – Front End Fundamentals**

Build a small web server with **Express** that uses **hbs** (Handlebars) templates to render a page from data. Everything runs in **GitHub Codespaces**, so you don't need to install anything on your computer.

Tested with Node 22, express 5.3, and hbs 4.4.

---

## Final folder structure

```
your-repo/
├── .gitignore
├── app.js
├── package.json
├── package-lock.json
├── node_modules/      (created by npm, not pushed to GitHub)
├── public/            (CSS, images, browser JS)
└── views/
    ├── layout.hbs
    └── home.hbs
```

---

## Step 0 – Open your repo in a Codespace

1. Go to your repository on GitHub.
2. Click the green **Code** button, then the **Codespaces** tab.
3. Click **Create codespace on main**.
4. Wait for VS Code to load in the browser. The terminal is at the bottom. If you don't see it, use **Terminal → New Terminal**.

Node and npm come preinstalled in Codespaces. Check with:

```bash
node -v
npm -v
```

> Make sure the terminal is in the **same folder** as your project. In Codespaces that's usually `/workspaces/<your-repo-name>`. Run `pwd` to check.

---

## Step 1 – Create the project and install packages

```bash
npm init -y
npm install express hbs
```

- `npm init -y` creates `package.json`, which lists your project's packages and scripts.
- `npm install express hbs` downloads both packages into `node_modules/` and adds them to `"dependencies"`.

Check that both installed:

```bash
npm ls
```

You should see `express@5.x` and `hbs@4.x`.

---

## Step 2 – Add `.gitignore` and a start script

Create a file named `.gitignore` with one line:

```
node_modules/
```

This keeps the large `node_modules/` folder off GitHub. Anyone who clones the repo runs `npm install` to get it back.

Next, open `package.json` and add a `start` line inside `"scripts"`. Put a comma after the line before it:

```json
{
  "name": "your-repo",
  "version": "1.0.0",
  "main": "app.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "start": "node app.js"
  },
  "dependencies": {
    "express": "^5.3.0",
    "hbs": "^4.4.0"
  }
}
```

Now `npm start` runs `node app.js` for you.

---

## Step 3 – Write `app.js`

```js
const express = require("express");
const app = express();
const port = 3000;

// Use hbs for templates (Express loads it for you)
app.set("view engine", "hbs");

// Serve static files (CSS, images) from the public folder
app.use(express.static("public"));

// Home page: render views/home.hbs with this data
app.get("/", (req, res) => {
  res.render("home", {
    title: "My Animals",
    animals: ["narwhal", "penguin", "seal"]
  });
});

// A route that sends JSON
app.get("/api/animals", (req, res) => {
  res.json(["narwhal", "penguin", "seal"]);
});

app.listen(port, () => {
  console.log(`Server running on port ${port}`);
});
```

- `app.set("view engine", "hbs")` tells Express to look in `views/` for `.hbs` files. You don't need `require("hbs")`.
- `res.render("home", {...})` fills in `views/home.hbs` with the data and sends the HTML.
- `res.json(...)` sends data instead of a page. You'll use that with `fetch()` later.

---

## Step 4 – Create the views

Make a folder named `views` with two files in it.

**`views/layout.hbs`** is the page shell every page shares. hbs uses a file with this exact name automatically.

```html
<!DOCTYPE html>
<html>
<head>
  <title>{{title}}</title>
  <link rel="stylesheet" href="/style.css">
</head>
<body>
  {{{body}}}
</body>
</html>
```

**`views/home.hbs`** is the content that goes where `{{{body}}}` is:

```html
<h1>{{title}}</h1>
<ul>
  {{#each animals}}
    <li>{{this}}</li>
  {{/each}}
</ul>
```

| Syntax | What it does |
| --- | --- |
| `{{title}}` | Inserts a value, with HTML escaped for safety |
| `{{{body}}}` | Inserts raw HTML (used only for the page content in the layout) |
| `{{#each animals}} ... {{/each}}` | Loops over an array, like a `for` loop |
| `{{this}}` | The current item inside `#each` |

The stylesheet link is optional. To use it, add `public/style.css`.

---

## Step 5 – Run it in Codespaces

```bash
npm start
```

You'll see `Server running on port 3000`. To view it:

1. A pop-up appears in the bottom-right corner: **"Your application running on port 3000 is available."** Click **Open in Browser**.
2. If you missed the pop-up, open the **Ports** tab (next to Terminal), find port **3000**, and click the globe icon.

The page opens at an address like `https://<codespace-name>-3000.app.github.dev`. Add `/api/animals` to the end of that address to see the JSON.

Press **Ctrl+C** in the terminal to stop the server. After you change `app.js`, stop and restart the server to see your changes. Changes to `.hbs` files show up when you refresh the page.

---

## Step 6 – Commit and push

From the terminal:

```bash
git add .
git commit -m "Add Express + hbs app"
git push
```

You can also use the **Source Control** icon in the left sidebar: type a message, click **Commit**, then **Sync Changes**.

Codespaces is already signed in to GitHub, so you won't be asked for a password.

---

## Troubleshooting

| Problem | Cause | Fix |
| --- | --- | --- |
| `Error: Cannot find module 'hbs'` | hbs isn't installed in this folder | Run `npm install hbs` in the folder that has `package.json` and `app.js`, then `npm start` again |
| `npm start` gives a JSON parse error | A missing or extra comma in `package.json` | Every line in `"scripts"` except the last needs a comma |
| `Missing script: "start"` | No start script, or it's in the wrong place | Add `"start": "node app.js"` inside `"scripts"` |
| Home page ignores your template | `public/index.html` exists and is served first | Delete or rename `public/index.html` |
| `Failed to lookup view "home"` | The file is misnamed or in the wrong folder | It must be `views/home.hbs` |
| `EADDRINUSE: port 3000` | The server is already running | Press Ctrl+C in the other terminal, or change `port` |
| No pop-up and nothing in the Ports tab | The server isn't running | Check the terminal for errors and run `npm start` again |
| Fresh Codespace says modules are missing | `node_modules/` isn't in GitHub | Run `npm install` first |

> **GitHub Pages can't run this app.** Pages only hosts static files (HTML, CSS, browser JS). A Node server has to run with `npm start` in a Codespace or on a computer.