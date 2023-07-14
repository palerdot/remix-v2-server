An sample repo for setting up the new Remix v2 dev server, which offers better development experience and HMR/HDR support.

---

Depending on whether you're using native esm/cjs server and server.js/ts, the following files needs to be adjusted:

- package.json
- remix.config.js
- tsconfig.json
- server.ts or server.js

## For examples specific to your setup, [check the branches here](https://github.com/xHomu/remix-v2-server/branches).

How they differ:

- [CommonJS + server.js](https://github.com/xHomu/remix-v2-server/commit/f73c6f3e2dc6f8cc60c80ecda97be68eda163e64)
- [CommonJS + server.ts](https://github.com/xHomu/remix-v2-server/commit/ab19dd14c524fbc7eb8b1968bb9a2d9fe5e037f7)
- [ES Modules + server.js](https://github.com/xHomu/remix-v2-server/commit/8119a735e73b0716cbaeab53dac5adf58d14278e)
- [ES Modules + server.ts](https://github.com/xHomu/remix-v2-server/commit/d4993e73f5d6a28291bf120364f93210eddbb516)

For more on Remix v2 dev server, check these talks by @pcattori Pedro Cattori!

- [Remix v2 Dev Server Docs](https://remix.run/docs/en/main/other-api/dev-v2)
- [Migrating your project to v2_dev](https://www.youtube.com/watch?v=6jTL8GGbIuc)
- [Next gen HMR in Remix](https://www.youtube.com/watch?v=79M4vYZi-po)

## Troubleshooting

### [ERR_REQUIRE_ESM]: require() of ES Module after update

### Remix serve cannot be found

This occurs because you're accidentally using the built-in RAS instead of of a custom Express server.js/ts.

Make sure you're using `v2_dev: true` instead of `unstable_dev: true` when using Remix v1.18+.

### "Error: package.json not found at PackageJson.load"

To maintain Windows compatibility, escape the quotation marks in package.json script:

- ❌ `"dev": "remix dev -c 'npm run dev:server' --no-restart",`
- ✅ ` "dev": "remix dev -c \"npm run dev:server\" --no-restart",`

### `tsx watch` fails to start on Windows

- Reference https://github.com/remix-run/remix/pull/6538

Due to upstream an `tsx watch` bug, use `ts-node` and `nodemon --watch` instead.

If you prefer `tsx watch`, you will need to patch `node_modules\@remix-run\dev\dist\devServer_unstable\index.js` with `patch-package` to fix the issue:

```js
    let newAppServer = execa.command(command, {
-     stdio: "pipe",
+     stdio: ['ignore', 'inherit', 'inherit'],
+     shell: true,
```

Alternatively, you can use `tsx` without the watch flag: `"dev:server": "tsx ./server.ts",`.

Beware that without `tsx watch`, changes to `server.ts` will not appear until you manually reboot the server.

- See also: [tsx workaround on Epic Stack](https://github.com/epicweb-dev/epic-stack/blob/main/server/dev-server.js)

---

# Welcome to Remix!

- [Remix Docs](https://remix.run/docs)

## Development

Start the Remix development asset server and the Express server by running:

```sh
npm run dev
```

This starts your app in development mode, which will purge the server require cache when Remix rebuilds assets so you don't need a process manager restarting the express server.

## Deployment

First, build your app for production:

```sh
npm run build
```

Then run the app in production mode:

```sh
npm start
```

Now you'll need to pick a host to deploy it to.

### DIY

If you're familiar with deploying express applications you should be right at home just make sure to deploy the output of `remix build`

- `build/`
- `public/build/`

### Using a Template

When you ran `npx create-remix@latest` there were a few choices for hosting. You can run that again to create a new project, then copy over your `app/` folder to the new project that's pre-configured for your target server.

```sh
cd ..
# create a new project, and pick a pre-configured host
npx create-remix@latest
cd my-new-remix-app
# remove the new project's app (not the old one!)
rm -rf app
# copy your app over
cp -R ../my-old-remix-app/app app
```
