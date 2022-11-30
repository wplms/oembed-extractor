
# oembed-extractor

Extract oEmbed content from given URL.

[![NPM](https://badge.fury.io/js/@extractus%2Foembed-extractor.svg)](https://badge.fury.io/js/@extractus%2Foembed-extractor)
[![CI test](https://github.com/extractus/oembed-extractor/workflows/ci-test/badge.svg)](https://github.com/extractus/oembed-extractor/actions)
[![Coverage Status](https://coveralls.io/repos/github/extractus/oembed-extractor/badge.svg)](https://coveralls.io/github/extractus/oembed-extractor)
![CodeQL](https://github.com/extractus/oembed-extractor/workflows/CodeQL/badge.svg)
[![JavaScript Style Guide](https://img.shields.io/badge/code_style-standard-brightgreen.svg)](https://standardjs.com)

## Intro

*oembed-extractor* is a part of tool sets for content builder:

- [feed-extractor](https://github.com/extractus/feed-extractor): extract & normalize RSS/ATOM/JSON feed
- [article-extractor](https://github.com/extractus/article-extractor): extract main article from given URL
- [oembed-extractor](https://github.com/extractus/oembed-extractor): extract oEmbed data from supported providers

You can use one or combination of these tools to build news sites, create automated content systems for marketing campaign or gather dataset for NLP projects...

### Attention

`oembed-parser` has been renamed to `@extractus/oembed-extractor` since v3.1.5


## Demo

- [Give it a try!](https://demos.pwshub.com/oembed-parser)
- [Example FaaS](https://oembed.deta.dev/?url=https://www.instagram.com/tv/CVlR5GFqF68/)


## Install & Usage

### Node.js

```bash
npm i @extractus/oembed-extractor

# pnpm
pnpm i @extractus/oembed-extractor

# yarn
yarn add @extractus/oembed-extractor
```

```ts
// es6 module
import { extract } from '@extractus/oembed-extractor'

// CommonJS
const { extract } = require('@extractus/oembed-extractor')

// or specify exactly path to CommonJS variant
const { extract } = require('@extractus/oembed-extractor/dist/cjs/oembed-extractor.js')

const result = await extract('https://www.youtube.com/watch?v=x2bqscVkGxk')
console.log(result)
```

### Deno

```ts
// deno > 1.28
import { extract } from 'npm:@extractus/oembed-extractor'

// deno < 1.28
// import { extract } from 'https://esm.sh/@extractus/oembed-extractor'

const result = await extract('https://www.youtube.com/watch?v=x2bqscVkGxk')
console.log(result)
```

### Browser

```ts
import { extract } from 'https://unpkg.com/@extractus/oembed-extractor@latest/dist/oembed-extractor.esm.js'
```

Please check [the examples](https://github.com/extractus/oembed-extractor/tree/main/examples) for reference.


### Deta cloud

For [Deta](https://www.deta.sh/) devs please refer [the source code and guideline here](https://github.com/ndaidong/oembed-parser-deta) or simply click the button below.

[![Deploy](https://button.deta.dev/1/svg)](https://go.deta.dev/deploy?repo=https://github.com/ndaidong/oembed-parser-deta)


## APIs

### `.extract()`

Load and extract oembed data.

#### Syntax

```ts
extract(String url)
extract(String url, Object params)
extract(String url, Object params, Object fetchOptions)
```

#### Parameters

##### `url` *required*

URL of a valid oEmbed resource, e.g. `https://www.youtube.com/watch?v=x2bqscVkGxk`

##### `params` *optional*

Optional argument `params` can be useful when you want to specify some additional customizations.

Here are several popular params:

- `maxwidth`: max width of embed size
- `maxheight`: max height of embed size
- `theme`: e.g, `dark` or `light`
- `lang`: e.g, 'en', 'fr', 'cn', 'vi', etc

Note that some params are supported by these providers but not by the others.
Please see the provider's oEmbed API docs carefully for exact information.

##### `fetchOptions` *optional*

You can use this param to set request headers to fetch.

For example:

```js
import { extract } from '@extractus/oembed-extractor'

const url = 'https://codepen.io/ndaidong/pen/LYmLKBw'
extract(url, null, {
  headers: {
    'user-agent': 'Opera/9.60 (Windows NT 6.0; U; en) Presto/2.1.1'
  }
})
```

You can also specify a proxy endpoint to load remote content, instead of fetching directly.

For example:

```js
import { extract } from '@extractus/oembed-extractor'

const url = 'https://codepen.io/ndaidong/pen/LYmLKBw'
extract(url, null, {
  headers: {
    'user-agent': 'Opera/9.60 (Windows NT 6.0; U; en) Presto/2.1.1'
  },
  proxy: {
    target: 'https://your-secret-proxy.io/loadJson?url=',
    headers: {
      'Proxy-Authorization': 'Bearer YWxhZGRpbjpvcGVuc2VzYW1l...'
    }
  }
})
```

With the above setting, request will be forwarded to `https://your-secret-proxy.io/loadJson?url={OEMBED_ENDPOINT}`.


### `.setProviderList()`

Apply a list of providers to use, overriding the [default](src/utils/providers.orginal.json).

#### Syntax

```ts
setProviderList(Array providers)
```

#### Parameters

##### `providers` *required*

List of providers to apply.

For example:

```js
import { setProviderList } from '@extractus/oembed-extractor'

const providers = [
  {
    provider_name: 'Alpha',
    provider_url: 'https://alpha.com',
    endpoints: [
      // endpoint definition here
    ]
  },
  {
    provider_name: 'Beta',
    provider_url: 'https://beta.com',
    endpoints: [
      // endpoint definition here
    ]
  }
]

setProviderList(providers)
```

Default list of resource providers is synchronized from [oembed.com](http://oembed.com/providers.json).

If you want to modify providers list, please make pull request on [iamcal/oembed](https://github.com/iamcal/oembed) then create issue/pr here to ask for sync.


## Facebook and Instagram

In order to work with the links from Facebook and Instagram, you need a [reviewed Facebook's app](https://developers.facebook.com/docs/app-review) with [oEmbed Read](https://developers.facebook.com/docs/features-reference/oembed-read) permission.

When seeing a link from Facebook or Instagram, `oembed-parser` will look for environment variables `FACEBOOK_APP_ID` and `FACEBOOK_CLIENT_TOKEN` to retrieve oembed data using your app credentials.

For example:

```bash
export FACEBOOK_APP_ID=your_app_id
export FACEBOOK_CLIENT_TOKEN=your_client_token

npm run eval https://www.instagram.com/tv/CVlR5GFqF68/
```


## Quick evaluation

```bash
git clone https://github.com/extractus/oembed-extractor.git
cd oembed-extractor
npm i

npm run eval {URL_TO_PARSE_OEMBED}
```

## License
The MIT License (MIT)

---
