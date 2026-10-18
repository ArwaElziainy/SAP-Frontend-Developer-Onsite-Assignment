# Product Article Application

This project is a small SAP Fiori/UI5 application that displays product articles from a local JSON file. Users can view the articles in a table and select an article to open its detail page.

## Features

- Loads article data from `webapp/localServices/data/Articles.json`.
- Displays the articles in a responsive `sap.m.Table`.
- Navigates from the article list to a detail route using `ArticleId`.
- Finds and displays the selected article on the detail page.
- Includes formatted article dates and status information.
- Supports back navigation from the detail page.

## Implementation Steps

### 1. Create the article list

The `Articles` JSON model is configured in `webapp/manifest.json` and points to `localServices/data/Articles.json`.

The table in `ArticleList.view.xml` binds its `items` aggregation to:

```xml
items="{Articles>/Articles}"
```

Each table cell uses the named `Articles` model to display fields such as `ArticleId`, `Description`, `Category`, `Supplier`, and `UnitPrice`.

### 2. Navigate with the article ID

When a row is selected, `onArticlePress` reads the selected row's binding context and retrieves its `ArticleId`:

```javascript
const oContext = oEvent.getSource().getBindingContext("Articles");
const sArticleId = oContext.getProperty("ArticleId");
```

The controller then navigates to the detail route and passes the ID as a route parameter:

```javascript
oRouter.navTo("RouteArticleDetail", {
    ArticleId: sArticleId
});
```

The route is configured in `manifest.json` with the pattern:

```text
RouteArticleDetail/{ArticleId}
```

### 3. Bind the selected article on the detail page

The detail controller performs the following steps:

1. Listens for the `RouteArticleDetail` route to be matched.
2. Reads `ArticleId` from the route arguments.
3. Retrieves the named `Articles` JSON model from the view.
4. Waits until the JSON file has finished loading.
5. Searches the `/Articles` array for the matching `ArticleId`.
6. Binds the detail view to the matching JSON path, for example `/Articles/0`.
7. Displays an error message and returns to the list if no matching article exists.

The element binding is created in the controller:

```javascript
oView.bindElement({
    path: `/Articles/${iArticleIndex}`,
    model: "Articles"
});
```

After the element binding is set, controls in `RouteArticleDetail.view.xml` can use relative bindings such as:

```xml
<Text text="{Articles>ArticleId}" />
<Text text="{Articles>Description}" />
```

## Time and Development Notes

I spent approximately one hour creating the article list, binding the table to `Articles.json`, routing by `ArticleId`, and binding the selected record on the detail page. Additional time was needed to handle the initial SAP Fiori/UI5 project setup and configuration.

The detail view and detail controller were reviewed with AI assistance. AI was used to review the routing and binding logic, improve error handling, and check the detail-page implementation. The application was then validated using the lint and build commands.

## Prerequisites

- Node.js LTS
- npm

## Install and Run

Open a terminal in the `product` project directory and install the dependencies:

```bash
npm install
```

Run the application with the SAP Fiori launchpad preview:

```bash
npm start
```

For a local SAPUI5 configuration, run:

```bash
npm run start-local
```

To run the application directly without the launchpad preview:

```bash
npm run start-noflp
```

The development server starts automatically and opens the application in the default browser. Stop the server with `Ctrl+C`.

## Validation

Check the source code with ESLint:

```bash
npm run lint
```

Create a production build in the `dist` directory:

```bash
npm run build
```

## Main Project Files

```text
webapp/
├── controller/
│   ├── ArticleList.controller.js
│   └── RouteArticleDetail.controller.js
├── localServices/data/
│   └── Articles.json
├── view/
│   ├── ArticleList.view.xml
│   └── RouteArticleDetail.view.xml
└── manifest.json
```
