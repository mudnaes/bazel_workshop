# Case 4: Vite + React + TypeScript + Bazel

In this case we use Vite to build a TypeScript React app, and Bazel to build the app and run tests.  
The approach in this example also works with local development through `pnpm` as developers are used to.

We use [rules_js](https://github.com/aspect-build/rules_js) to handle the JavaScript ecosystem in Bazel.

Bazel only handles packages from the `pnpm-lock.yaml` at the workspace root, so when you make changes to package.json remember to run `pnpm install` afterward.
To see the setup for pnpm, check the [npm_translate_lock function in MODULE.bazel at root](../../MODULE.bazel). 

To make your IDE happy when using local workspace packages, you have to extra paths to the Bazel output in your `tsconfig.json`:

```json
{
  "compilerOptions": {
    "paths": {
      "openapi_typescript/*": [
        "../../bazel-bin/workshop/case5/pkg/*"
      ]
    }
  }
}
```

See more about this in the [rules_js FAQ](https://github.com/aspect-build/rules_js/blob/main/docs/faq.md#making-the-editor-happy) in this case.

## Things to try out

### Build the application
`bazel build :build`

### Run application in dev mode
`bazel run :dev` - note that this does not notice changed files in the workspace, so you have to restart the command to see changes.  
To get hot reloading through Bazel you need to use ibazel, see [bazel-watcher](https://github.com/bazelbuild/bazel-watcher).  
Then you can run `ibazel run :dev` or `npx @bazel/ibazel run :dev` instead.

Dev mode also works through `pnpm run dev`, which goes outside of Bazel

### Create a Docker image
`bazel run :tarball` builds an OCI compatible image and loads it into your Docker context.  
Afterwards, you can run the application with `docker run --rm -it -p80:80 case4:latest`

### Inspect the build outputs
You can run `bazel cquery //workshop/case4:<target> --output=files` to get the files output for a given target.

If you run `bazel cquery //workshop/case4:tar --output=files` you will get the tar layer for application code. You can then inspect the tar by running `tar -tvf bazel-bin/workshop/case4/tar.tar`:
```shell
drwxr-xr-x  0 0      0           0  1 jan  2000 usr/
drwxr-xr-x  0 0      0           0  1 jan  2000 usr/share/
drwxr-xr-x  0 0      0           0  1 jan  2000 usr/share/nginx/
drwxr-xr-x  0 0      0           0  1 jan  2000 usr/share/nginx/html/
drwxr-xr-x  0 0      0           0  1 jan  2000 usr/share/nginx/html/assets/
-rw-r--r--  0 0      0      142531  1 jan  2000 usr/share/nginx/html/assets/index-DDIbAWc_.js
-rw-r--r--  0 0      0         334  1 jan  2000 usr/share/nginx/html/index.html
```

## Additional things to try

### Can you include the [greeting-component](../../teams/libs/frontend/greeting-component/package.json) in the app?

### Can you transpile the TypeScript to JavaScript before building the app with Vite?
Check out [rules_swc](https://github.com/aspect-build/rules_swc) for an example on how to transpile TypeScript to JavaScript with Bazel.
This could be useful if you want to reuse the JavaScript in tests to avoid multiple transpilations.
