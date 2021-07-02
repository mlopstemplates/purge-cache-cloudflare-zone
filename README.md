# GitHub Action to Purge Cloudflare Cache  🗑️ 

Based on [this Action](https://github.com/jakejarvis/cloudflare-purge-action) from jakejarvis which seems unmaintained, and merged [this PR](https://github.com/jakejarvis/cloudflare-purge-action/pull/2) from chabad360 to work with the new YAML syntax.

This simple action calls the [Cloudflare API](https://api.cloudflare.com/#zone-purge-all-files) to purge the cache of your website, which can be a helpful last step after deploying a new version.


## Usage

All sensitive variables should be [set as encrypted secrets](https://help.github.com/en/articles/virtual-environments-for-github-actions#creating-and-using-secrets-encrypted-variables) in the action's configuration.


### Configuration Variables

| Key | Value | Suggested Type | Required |
| ------------- | ------------- | ------------- | ------------- |
| `cloudflareZone` | The Zone ID of your domain, which can be found in the right sidebar of your domain's overview page on the Cloudflare dashboard. For example, `xyz321xyz321xyz321xyz321xyz321xy`. | `secret` | **Yes** |
| `cloudflareApiKey` | Your Cloudflare API key, which can be generated using [these instructions](https://support.cloudflare.com/hc/en-us/articles/200167836-Where-do-I-find-my-Cloudflare-API-key-). For example, `abc123abc123abc123abc123abc123abc123abc123abc`. | `secret` | **Yes** |
| `purgeURLs` | **Optional.** An array of **fully qualified URLs** to purge. For example: `'["https://jarv.is/style.css", "https://jarv.is/favicon.ico"]'`. If unset, the action will purge everything (which is [suggested](#purging-specific-files)). | `option` | No |


### Creating a restricted API token on Cloudflare

API Tokens are [a new feature](https://blog.cloudflare.com/api-tokens-general-availability/) as of August 2019. They allow you to restrict the scope of this action to only purging the cache of zones you specify. In other words, this is much safer than allowing this action complete control of your entire Cloudflare account. (I'm not evil though, I promise. 😊)

Creating a token can be tricky, so here's what you should enter [on this page](https://dash.cloudflare.com/profile/api-tokens) to create a token for purging the cache of a single domain on your account:

![Creating an API Token for purging](tokens.png)

### `workflow.yml` Example

Place in a `.yml` file such as this one in your `.github/workflows` folder. [Refer to the documentation on workflow YAML syntax here.](https://help.github.com/en/articles/workflow-syntax-for-github-actions)

```yaml
name: Deploy my website
on: push

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:

    # Put steps here to build your site, deploy it to a service, etc.

    - name: Purge cache
      uses: jakejarvis/cloudflare-purge-action@master
      with:
        cloudflareZone: ${{ secrets.CLOUDFLARE_ZONE }}
        cloudflareApiKey: ${{ secrets.CLOUDFLARE_API_KEY }}
```

### Purging specific files

To purge only specific files, you can pass an array of **fully qualified URLs** via a fourth environment variable named `PURGE_URLS`. Unfortunately, Cloudflare doesn't support wildcards (unless you're on the insanely expensive Enterprise plan) so in order to purge a folder, you'd need to list every file in that folder. It's probably safer to leave this out and purge everything, but in case you want really to, the syntax is as follows:

```yaml
purgeURLs: '["https://example.com/style.css", "https://example.com/favicon.ico"]'
```


## License

This project is distributed under the [MIT license](LICENSE.md).
