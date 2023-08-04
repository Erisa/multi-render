# multi-render

A Cloudflare Workers project showcasing how to have multiple [kotx/render](https://github.com/kotx/render) instances in a single Worker, allowing R2 buckets and settings to vary by domain within the same Worker.

## Setup

1. Clone the repository.
2. Install dependencies: `npm install`
3. Edit `wrangler.toml` to change default settings and specify domains and buckets. This repository is setup for my usage so remove my buckets you won't use.
4. Open `index.ts`, add the buckets to the `Env` type following the example, and add `config` for them below.

E.g. 

`wrangler.toml`
```toml
routes = [
  { pattern = "mybucket.example.com", custom_domain = true },
]

[[r2_buckets]]
binding = "MY_BUCKET"
bucket_name = "mybucket"
preview_bucket_name = "mybucket"
```

`index.ts`
```ts
interface Env {
	R2_BUCKET: R2Bucket,
	ALLOWED_ORIGINS?: string,
	CACHE_CONTROL?: string,
	PATH_PREFIX?: string
	INDEX_FILE?: string
	NOTFOUND_FILE?: string
	DIRECTORY_LISTING?: boolean
	HIDE_HIDDEN_FILES?: boolean
	DIRECTORY_CACHE_CONTROL?: string

	// put extra buckets here
	MY_BUCKET: R2Bucket
}
```

```ts
		const config: { [id: string]: RenderEnv; } = {
			"mybucket.example.com": {
				R2_BUCKET: env.MY_BUCKET,
				DIRECTORY_LISTING: true,
				INDEX_FILE: 'index_alt.html',
				ALLOWED_ORIGINS: "my-other-cool-site.example.com"
			},
```

Once done, `npm run deploy` and enjoy.