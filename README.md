# Heroku Buildpack for npm Authentication

This is a Heroku buildpack that enables authenticated npm operations
within a Heroku dyno.

It detects an `NPM_AUTH_TOKEN` environment variable and creates a `.npmrc` file.

It is the soul sister of the [GitHub Buildpack](https://github.com/zeke/github-buildpack).

See the blog post: [npm and GitHub automation with Heroku](http://zeke.sikelianos.com/npm-and-github-automation-with-heroku)

## Usage

Use this one-liner to read your [npm auth token](http://blog.npmjs.org/post/118393368555/deploying-with-npm-private-modules):

```sh
cat ~/.npmrc | head -1 | sed 's/.*=//g'
```

[Optional] Save the URL of your custom registry to your Heroku app config:

```sh
heroku config:set NPM_REGISTRY_URL=yourdomain.example.local/npmRegistry/
```

Save the token in your Heroku app config:

```sh
heroku config:set NPM_AUTH_TOKEN=YOUR_TOKEN_HERE
```

Configure your app to use this buildpack:

```sh
heroku buildpacks:add --index 1 https://github.com/zeke/npm-buildpack
```

The next time you push your app to Heroku, this buildpack will create a
`.npmrc` file containing your npm token in the base directory of the app:

```sh
heroku run bash
cat .npmrc
//registry.npmjs.org/:_authToken=00000000-0000-0000-0000-000000000000
```

If you also specified the optional `NPM_REGISTRY_URL` variable as shown above, then:
```sh
heroku run bash
cat .npmrc
//yourdomain.example.local/npmRegistry/:_authToken=00000000-0000-0000-0000-000000000000
```

Now you can perform authenticated npm operations on the dyno, including
`npm publish`!

## Tips

Tip: If you ever change the token, you'll need to redeploy the app to
ensure a new .npmrc file is created:

```sh
heroku config:set NPM_AUTH_TOKEN=NEW_TOKEN
git commit --allow-empty -m "update dat npm token"
git push heroku master
```

Tip: Heroku's node buildpack will install `dependencies` from package.json
by default. If your app needs `devDependencies` to be installed too,
set the following in your app environment:

```sh
heroku config:set NPM_CONFIG_PRODUCTION=false
```
