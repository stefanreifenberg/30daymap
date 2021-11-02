
# 30DayMapChallenge - [Svelte WebGIS](https://freiburg.kaldera.dev)

My goal for the 30DayMapChallenge is to create a WebGIS application based on open data from my home town. Every day will be a standalone application that will be deployed to netlify. In the end i will combine all the applications into one single application. 

The following technologies are used:
- [Svelte](https://svelte.dev/)
- [Mapbox](https://www.mapbox.com/)
- [Maplibre](https://maplibre.org/)

The current day will always be available [here](https://freiburg.kaldera.dev).

## Run the project locally

Clone this repo

```bash
git clone https://github.com/stefanreifenberg/30daymap.git
cd 30daymap
npm install
```

...then start [Rollup](https://rollupjs.org):

```bash
npm run dev
```

Navigate to [localhost:5000](http://localhost:5000). You should see your app running. Edit a component file in `src`, save it, and reload the page to see your changes.

By default, the server will only respond to requests from localhost. To allow connections from other computers, edit the `sirv` commands in package.json to include the option `--host 0.0.0.0`.

If you're using [Visual Studio Code](https://code.visualstudio.com/) we recommend installing the official extension [Svelte for VS Code](https://marketplace.visualstudio.com/items?itemName=svelte.svelte-vscode). If you are using other editors you may need to install a plugin in order to get syntax highlighting and intellisense.

## Building and running in production mode

To create an optimised version of the app:

```bash
npm run build
```

You can run the newly built app with `npm run start`. This uses [sirv](https://github.com/lukeed/sirv), which is included in your package.json's `dependencies` so that the app will work when you deploy to platforms like [Heroku](https://heroku.com).

