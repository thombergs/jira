## Contributing

[code-of-conduct]: CODE_OF_CONDUCT.md
[license]: LICENSE

[configure-github-app]: https://probot.github.io/docs/development/#configuring-a-github-app
[jira-developer-instance]: https://developer.atlassian.com/platform/marketplace/getting-started/#free-developer-instances-to-build-and-test-your-app
[style]: https://standardjs.com/
[releases]: https://help.github.com/articles/github-terms-of-service/#6-contributions-under-repository-license

Hi there! We're thrilled that you'd like to contribute to this project. Your help is essential for keeping it great.

## Notices
Contributions to this product are [released][releases] to the public under the [project's open source license][license].

Please note that this project is released with a [Contributor Code of Conduct][code-of-conduct]. By participating in this project you agree to abide by its terms.

## Configuring a GitHub App

The first step for running the app locally is to configure a GitHub App. For that you will need to use [ngrok](https://ngrok.com) to expose a URL publicly - referred to as `DOMAIN` in this doc - which will tunnel traffic back to your computer. If you choose to proceed with ngrok, you can do that by running `ngrok http 4002` after installing it.

In order not to get a different domain each time you start ngrok, start it with a subomain (only works in the paid version, though):

```
ngrok http 9090 --subdomain SUBDOMAIN
```

ngrok will then print out your DOMAIN in the command line.

Create a new [GitHub App](https://github.com/settings/apps), setting the following config:

- **GitHub App name**: Anything you want, but it must be unique across GitHub. `E.g. my-test-app`
- **Homepage URL**: `https://github.com/apps/my-test-app`
- **Callback URL**: `https://DOMAIN/github/callback`
- **Setup URL**: `https://DOMAIN/github/configuration`
- **Webhook URL**: `https://DOMAIN/github/events`

Your new GitHub app will need the following repository permissions:

+ Contents: Read & write
+ Issues: Read & write
+ Metadata: Read-only
+ Pull requests: Read & write

It will also need to subscribe to the following events:

+ Commit comment
+ Create
+ Delete
+ Issue comment
+ Issues
+ Pull request
+ Pull request review
+ Push

## Setting up your `.env` file

Once you've set up your GitHub app and cloned this repo, copy the content from `.env.example` and paste it to a new file called `.env`, with the following configuration:

+ `APP_ID` and `GITHUB_CLIENT_ID`: Copy these values over from your new GitHub app page.
+ `GITHUB_CLIENT_SECRET`: You'll need to generate a new one on your GitHub app page by hitting the `Generate a new client secret` button. Copy and paste the generated secret.
+ `APP_URL`: `https://DOMAIN`
+ `STORAGE_SECRET`: It needs to be set to a 32 char secret (anything else fails). You can generate one by running `openssl rand -hex 32` in your terminal.
+  `PRIVATE_KEY_PATH`: You'll also need to generate a new private key on your GitHub app page, download it, move it to the source root of this repo, and set `PRIVATE_KEY_PATH=<your-private-key-name>.pem`
+ `ATLASSIAN_URL`: The URL for the Jira instance you're testing it. If you don't have one now, please set the value of this variable after going through the step 1 of "Configuring the Jira instance" section of this document.

## Running dependencies

Please ensure that you have [homebrew](https://brew.sh/) installed. Instructions for setting this application up on an OS other than OSX are currently not outlined.

This app is written in [ES6 JavaScript](https://nodejs.org/en/docs/es6/) and runs on [Node.js](https://nodejs.org/).

Required version of Node: v10.13.0

**WARNING:**  Newer versions of Node do not work with "pg": "^7.4.3". It will simply hang on every DB operation with no logs whatsoever. Check you version with `node --version`; Run `nvm use 10.13` if you need to change it.

Install the dependencies by running:

```
$ script/bootstrap
```

This will install all dependencies, including `node` and `postgres`, which are required to run the app. However, we recommend running `postgres` and `redis` in docker instead of doing it locally. You can do so by running:

`docker run -p 5432:5432 --name postgres -e POSTGRES_HOST_AUTH_METHOD=trust postgres` and `docker run -p 6379:6379 redis`.

**Note:** Most certainly, if you are running Postgres in docker, it won’t have any info about your local user, so the superuser will be named postgres instead of having the same name as your Mac user. You can fix this by:
* Open db/config.json
* Add "username": "postgres", inside "development" and "test".
* Be careful to not commit this file!

To set up the databases and keep their schemas up to date, run:

```
$ script/db_create
```

You can verify that your code is set up correctly by running:

```
$ npm test
```


#### Running the application

When you are developing you will prefer to run the app and automatically restart it when you do changes to the source code. In that case you should use:

```
$ script/server
```

For production, you should just use `npm run start`.


## Configuring the Jira instance

1. Create a new [free developer instance][jira-developer-instance] on Jira Cloud.
2. From the header menu, select **Apps** -> **Manage your apps**. (If you're using an older version of Jira, you won't have a left sidebar. Instead, click the **Gear Icon** in the top-right corner and select **Settings**. From there, select **Manage add-ons** from the left sidebar).
3. Verify the filter is set to `User-installed`, and select **Settings** beneath the User-installed apps table.
4. On the Settings pop-up, add **Enable development mode** and click **Apply**.
5. From the right header, select **Upload app** and enter `https://DOMAIN/jira/atlassian-connect.json`
6. Click **Upload**.
7. That's it! You're done. :tada:

## Submitting a pull request

1. [Fork](https://reflectoring.io/github-fork-and-pull/) and clone the repository.
1. Configure and install the dependencies: `npm install`
1. Make sure the tests pass on your machine: `npm test`, note: these tests also apply the linter, so no need to lint seperately
1. Create a new branch: `git checkout -b my-branch-name`
1. Make your change, add tests, and make sure the tests still pass
1. Push to your fork and submit a pull request
1. Pat your self on the back and wait for your pull request to be reviewed and merged.

Here are a few things you can do that will increase the likelihood of your pull request being accepted:

- Follow the [style guide][style] which is using standard. Any linting errors should be shown when running `npm test`
- Write and update tests.
- Keep your change as focused as possible. If there are multiple changes you would like to make that are not dependent upon each other, consider submitting them as separate pull requests.
- Write a [good commit message](http://tbaggery.com/2008/04/19/a-note-about-git-commit-messages.html).

Work in Progress pull request are also welcome to get feedback early on, or if there is something blocking you.

## Resources

- [How to Contribute to Open Source](https://opensource.guide/how-to-contribute/)
- [Using Pull Requests](https://help.github.com/articles/about-pull-requests/)
- [GitHub Help](https://help.github.com)
