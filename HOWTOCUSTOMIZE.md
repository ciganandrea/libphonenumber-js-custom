# How to customize

1. Duplicate the folder `max/` and rename the new folder to `custom/`
1. [Customize the files in `custom/`](#Customize-the-files-in-custom)
1. [Add new NPM scripts for creating the custom metadata](#update-packagejson)
1. [Add new export to the build](#update-rollupconfigjs)
1. Build the custom library
   1. Fetch the latest full metadata  
      `npm run metadata:download`
   1. Create the custom metadata JSON file  
      `npm run metadata:generate:custom`
   1. Create the custom metadata JS file  
      `npm run metadata:generate:custom:js`
   1. Build the library  
      `npm run build`
1. The file to be used is `bundle/libphonenumber-js.custom.js`

---

## Customize the files in `custom/`

**`metadata.js`**

```diff
+import metadata from '../metadata.full.json.js'
-import metadata from '../metadata.custom.json.js'
```

**`package.json`**

```diff
-"name": "libphonenumber-js-max",
+"name": "libphonenumber-js-custom",
```

**`index.commonjs.js`**

```diff
-var metadata = require('../metadata.full.json')
+var metadata = require('../metadata.custom.json')
```

Remove all exports that do not mention the only function needed (`parsePhoneNumberFromString`) from both:

1. `index.commonjs.js`
1. `index.is`

## Update `package.json`

Add the script to create the custom metadata JSON for the listed Countries

```json
{
  "metadata:generate:custom": "babel-node -- runnable/generate ../PhoneNumberMetadata.xml ../metadata.custom.json --countries CA,CH,DE,FR,HK,IN,KR,MY,NL,SG,TH,TW,US,ZA --extended --debug"
}
```

Add the script to convert the JSON from above to JS

```json
{
  "metadata:generate:custom:js": "babel-node -- runnable/json-to-js ./metadata.custom.json"
}
```

## Update `rollup.config.js`

Add the export for the custom library

```js
{
	input: 'custom/index.js',
	plugins: [json(), terser()],
	output: {
		format: 'umd',
		name: 'libphonenumber',
		file: 'bundle/libphonenumber-js.custom.js',
		sourcemap: true,
	}
}
```
