# svelte-adapter-azure-swa

Adapter for Svelte apps that creates an Azure Static Web App, using an Azure function for dynamic server rendering.

**This is beta software. Please report any issues you encounter.**

## Usage

Run `npm install -D svelte-adapter-azure-swa`.

Then in your `svelte.config.js`:

```js
import azure from 'svelte-adapter-azure-swa';

export default {
	kit: {
		...
		adapter: azure()
	}
};
```

You will need to create an `api/` folder in your project root containing a [`host.json`](https://docs.microsoft.com/en-us/azure/azure-functions/functions-host-json) (see sample below). The adapter will output the `render` Azure function for SSR in that folder. The `api` folder needs to be in your repo so that Azure can recognize the API at build time. However, you can add `api/render` to your .gitignore so that the generated function is not in source control.

### Sample `host.json`

```json
{
	"version": "2.0",
	"extensionBundle": {
		"id": "Microsoft.Azure.Functions.ExtensionBundle",
		"version": "[2.*, 3.0.0)"
	}
}
```

## Azure configuration

When deploying to Azure, you will need to properly [configure your build](https://docs.microsoft.com/en-us/azure/static-web-apps/build-configuration?tabs=github-actions) so that both the static files and API are deployed.

| property          | value          |
| ----------------- | -------------- |
| `app_location`    | `./`           |
| `api_location`    | `/api`         |
| `output_location` | `build/static` |

## Running locally with the Azure SWA CLI

**Local SWA debugging is currently broken** due to the following SWA CLI issues: [261](https://github.com/Azure/static-web-apps-cli/issues/261) and [286](https://github.com/Azure/static-web-apps-cli/issues/286)

You can debug using the [Azure Static Web Apps CLI](https://github.com/Azure/static-web-apps-cli). Note that the CLI is currently in preview and you may encounter issues.

To run the CLI, install `@azure/static-web-apps-cli` and add a `swa-cli.config.json` to your project (see sample below). Run `swa start` to start the emulator. See the CLI docs for more information on usage.

### Sample `swa-cli.config.json`

```json
{
	"configurations": {
		"app": {
			"context": "./build/static",
			"apiLocation": "./api"
		}
	}
}
```

## Advanced Configuration

### esbuild

As an escape hatch, you may optionally specify a function which will receive the final esbuild options generated by this adapter and returns a modified esbuild configuration. The result of this function will be passed as-is to esbuild. The function can be async.

For example, you may wish to add a plugin:

```js
adapterVercel({
	esbuild(defaultOptions) {
		return {
			...defaultOptions,
			plugins: []
		};
	}
});
```