# Playlist mechanics

Interaction-Design demo for music-playlist mechanics:
(cares less about graphical UI)

There are 5 lists:

- played songs
- current song (yes, also a list because of fading tracks)
- queued songs
- queued from previous songs
- remaining songs

Actions:

- Next: Plays the next song from queued list, then from remaining. Puts current at end of played.
- Prev: Play previous played song. Puts current at start of queue-previous.
- Random: If active AND queue/queue-previous empty, picks random next song. No effect on previous songs.
- Queue song: Moves song at the end of queue.
- Move queued song to remaining: Puts song back in remaining at sorted position. (TODO: should be possible to move songs to played)
- Double-click song: Moves current position to that song as if prev/next was triggered so many times. If random, songs between current position and selected song will not move to played.

UX:

- Queue: Listener want's to have THAT song coming next, no matter whats next in the list.
- Played Songs: The music plays. Later the listener wants to look up THAT SONG from 10 Minutes ago.
- Random: Will not change history, so going back and forth stays expectable, while keeping control about queued songs
- Sorting: ...
- In Focus: ...

Motivation:

So far all Music-Players I tried on Linux are either missing an elegant GUI or are too limited in their funcions.

------

# Original README

*Looking for a shareable component template? Go here --> [sveltejs/component-template](https://github.com/sveltejs/component-template)*

---

# svelte app

This is a project template for [Svelte](https://svelte.dev) apps. It lives at https://github.com/sveltejs/template.

To create a new project based on this template using [degit](https://github.com/Rich-Harris/degit):

```bash
npx degit sveltejs/template svelte-app
cd svelte-app
```

*Note that you will need to have [Node.js](https://nodejs.org) installed.*


## Get started

Install the dependencies...

```bash
cd svelte-app
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


## Single-page app mode

By default, sirv will only respond to requests that match files in `public`. This is to maximise compatibility with static fileservers, allowing you to deploy your app anywhere.

If you're building a single-page app (SPA) with multiple routes, sirv needs to be able to respond to requests for *any* path. You can make it so by editing the `"start"` command in package.json:

```js
"start": "sirv public --single"
```

## Using TypeScript

This template comes with a script to set up a TypeScript development environment, you can run it immediately after cloning the template with:

```bash
node scripts/setupTypeScript.js
```

Or remove the script via:

```bash
rm scripts/setupTypeScript.js
```

## Deploying to the web

### With [Vercel](https://vercel.com)

Install `vercel` if you haven't already:

```bash
npm install -g vercel
```

Then, from within your project folder:

```bash
cd public
vercel deploy --name my-project
```

### With [surge](https://surge.sh/)

Install `surge` if you haven't already:

```bash
npm install -g surge
```

Then, from within your project folder:

```bash
npm run build
surge public my-project.surge.sh
```
