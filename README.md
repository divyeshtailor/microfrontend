# Angular Module Federation Starter Kit

This starter kit guides you through the process of setting up and configuring Angular Module Federation using `@angular-architects/module-federation`. In this example, we have a project with two folders: `shell` (host) and `mfe1` (microfrontend). The `shell` hosts the microfrontend module, specifically the `Flights` module located at `projects/mfe1/src/app/flights`.

## Steps to Create Module Federation in Angular

### 1. Install `@angular-architects/module-federation`

In your project folder, install the module federation package for both the shell and microfrontend:

```bash
ng add @angular-architects/module-federation --project shell --type host --port 4200

ng add @angular-architects/module-federation --project mfe1 --type remote --port 4201
```

This sets up module federation, assigns ports for serving, and generates the module federation configuration.

### 2. Configure Microfrontend Exports

Edit the `projects\mfe1\webpack.config.js` file to expose the `Flights` module:

```javascript
exposes: {
  './Module': './projects/mfe1/src/app/flights/flights.module.ts',
},
```

### 3. Configure Host Remote Reference

Edit the `projects\shell\webpack.config.js` file to reference the microfrontend:

```javascript
remotes: {
  mfe1: 'http://localhost:4201/remoteEntry.js',
},
```

Ensure that the microfrontend's port is set to `4201`.

### 4. Add Microfrontend Route in Shell

Edit the `projects\shell\src\app\app.routes.ts` file and add a route to the microfrontend:

```typescript
{
  path: 'flights',
  loadChildren: () => import('mfe1/Module').then(m => m.FlightsModule),
},
```

Note: The import URL should match the names defined in the configuration files.

### 5. Declare Module to Avoid TypeScript Error

In the `projects\shell\src\decl.d.ts` file, declare the module to prevent TypeScript errors:

```typescript
declare module 'mfe1/Module';
```

### 6. Serve the Applications

In separate terminals, serve both the shell and microfrontend:

```bash
ng serve shell -o

ng serve mfe1 -o
```

Visit http://localhost:4200 in your browser, click on "Flights," and the microfrontend should load into the shell. Ensure that the microfrontend also runs in standalone mode at http://localhost:4201.

Congratulations! You've successfully implemented Angular Module Federation.
