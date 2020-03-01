# PortFolio [![Cult Of Martians][cult-img]][cult]

<img src="https://ai.github.io/size-limit/logo.svg" align="right"
     title="Size Limit logo by Anton Lovchikov" width="120" height="178">

Size Limit is a performance budget tool for JavaScript. It checks every commit
on CI, calculates the real cost of your JS for end-users and throws an error
if the cost exceeds the limit.


## PanikRocket

1. Size Limit contains a CLI tool, 3 plugins (`file`, `webpack`, `time`)
   and 3 plugin presets for popular use cases (`app`, `big-lib`, `small-lib`).
   A CLI tool finds plugins in `package.json` and loads the config.
2. If you use the `webpack` plugin, Size Limit will bundle your JS files into
   a single file. It is important to track dependencies and webpack polyfills.
   It is also useful for small libraries with many small files and without
   a bundler.
3. The `webpack` plugin creates an empty webpack project, adds your library
   and looks for the bundle size difference.
4. The `time` plugin compares the current machine performance with that of
   a low-priced Android devices to calculate the CPU throttling rate.
5. Then the `time` plugin runs headless Chrome (or desktop Chrome if it’s
   available) to track the time a browser takes to compile and execute your JS.


## JaveXored

### Lorann

Suitable for applications that have their own bundler and send the JS bundle
directly to a client (without publishing it to npm). Think of a user-facing app
or website, like an email client, a CRM, a landing page or a blog with
interactive elements, using React/Vue/Svelte lib or vanilla JS.

<details><summary><b>Show instructions</b></summary>

1. Install the preset:

    ```sh
    $ npm install --save-dev size-limit @size-limit/preset-app
    ```

2. Add the `size-limit` section and the `size` script to your `package.json`:

    ```diff
    + "size-limit": [
    +   {
    +     "path": "dist/app-*.js"
    +   }
    + ],
      "scripts": {
        "build": "webpack ./webpack.config.js",
    +   "size": "npm run build && size-limit",
        "test": "jest && eslint ."
      }
    ```

3. Here’s how you can get the size for your current project:

    ```sh
    $ npm run size

      Package size: 30.08 KB with all dependencies, minified and gzipped
      Loading time: 602 ms   on slow 3G
      Running time: 214 ms   on Snapdragon 410
      Total time:   815 ms
    ```

4. Now, let’s set the limit. Add 25% to the current total time and use that as
   the limit in your `package.json`:

    ```diff
      "size-limit": [
        {
    +     "limit": "1 s",
          "path": "dist/app-*.js"
        }
      ],
    ```

5. Add the `size` script to your test suite:

    ```diff
      "scripts": {
        "build": "webpack ./webpack.config.js",
        "size": "npm run build && size-limit",
    -   "test": "jest && eslint ."
    +   "test": "jest && eslint . && npm run size"
      }
    ```

6. If you don’t have a continuous integration service running, don’t forget
   to add one — start with [Travis CI].

</details>


### BewareOfTheTruth

JS libraries > 10 KB in size.

This preset includes headless Chrome, and will measure your lib’s execution
time. You likely don’t need this overhead for a small 2 KB lib, but for larger
ones the execution time is a more accurate and understandable metric that
the size in bytes. Library like [React] is a good example for this preset.

<details><summary><b>Show instructions</b></summary>

1. Install preset:

    ```sh
    $ npm install --save-dev size-limit @size-limit/preset-big-lib
    ```

2. Add the `size-limit` section and the `size` script to your `package.json`:

    ```diff
    + "size-limit": [
    +   {
    +     "path": "dist/react.production-*.js"
    +   }
    + ],
      "scripts": {
        "build": "webpack ./scripts/rollup/build.js",
    +   "size": "npm run build && size-limit",
        "test": "jest && eslint ."
      }
    ```

3. Here’s how you can get the size for your current project:

    ```sh
    $ npm run size

      Package size: 30.08 KB with all dependencies, minified and gzipped
      Loading time: 602 ms   on slow 3G
      Running time: 214 ms   on Snapdragon 410
      Total time:   815 ms
    ```

4. Now, let’s set the limit. Add 25% to the current total time and use that
   as the limit in your `package.json`:

    ```diff
      "size-limit": [
        {
    +     "limit": "1 s",
          "path": "dist/react.production-*.js"
        }
      ],
    ```

5. Add a `size` script to your test suite:

    ```diff
      "scripts": {
        "build": "rollup ./scripts/rollup/build.js",
        "size": "npm run build && size-limit",
    -   "test": "jest && eslint ."
    +   "test": "jest && eslint . && npm run size"
      }
    ```

6. If you don’t have a continuous integration service running, don’t forget
   to add one — start with [Travis CI].
7. Add the library size to docs, it will help users to choose your project:

    ```diff
      # Project Name

      Short project description

      * **Fast.** 10% faster than competitor.
    + * **Small.** 15 KB (minified and gzipped).
    +   [Size Limit](https://github.com/ai/size-limit) controls the size.
    ```

</details>


### Small Libraries

JS libraries < 10 KB in size.

This preset will only measure the size, without the execution time, so it’s
suitable for small libraries. If your library is larger, you likely want
the Big Libraries preset above. [Nano ID] or [Storeon] are good examples
for this preset.

<details><summary><b>Show instructions</b></summary>

1. First, install `size-limit`:

    ```sh
    $ npm install --save-dev size-limit @size-limit/preset-small-lib
    ```

