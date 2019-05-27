Adapted from [github-developer/using-the-github-api-in-your-app](https://github.com/github-developer/using-the-github-api-in-your-app).

This is an example GitHub App that automates the protection of the master branch upon creation of new repositories within a GitHub organization. The creator of the new repository will be notified with an [@mention](https://help.github.com/en/articles/basic-writing-and-formatting-syntax#mentioning-people-and-teams) in an issue within the repository that outlines the protections that were added.

This project listens for [organization events](https://developer.github.com/webhooks/#events) and uses the [Octokit.rb](https://github.com/octokit/octokit.rb) library to make REST API calls.

## Prerequisites

To run this web service on your local machine, you will need to use a tool like Smee to send webhooks to your local machine without exposing it to the internet. If you're already comfortable with other tools that expose your local machine to the internet like [ngrok](https://ngrok.com/) or [localtunnel](https://localtunnel.github.io/www/), feel free to use those.

### Start a new Smee channel

Go to https://smee.io and click **Start a new channel**.

Starting a new Smee channel creates a unique domain where GitHub can send webhook payloads. This domain is called a Webhook Proxy URL and looks something like this: `https://smee.io/qrfeVRbFbffd6vD`

**Note:** The following steps are slightly different than the "Use the CLI" instructions you'll see on your Smee channel page. You do **not** need to follow the "Use the Node.js client" or "Using Probot's built-in support" instructions.

1. Install the Smee client
    ```
    npm install --global smee-client
    ```
    
1. Run the client (replacing `https://smee.io/qrfeVRbFbffd6vD` with your own domain):
    ```
    smee --url https://smee.io/qrfeVRbFbffd6vD --path /event_handler --port 3000
    ```
    
    You should see output like the following:
    ```
    Forwarding https://smee.io/qrfeVRbFbffd6vD to http://127.0.0.1:3000/event_handler
    Connected https://smee.io/qrfeVRbFbffd6vD
    ```

Next, you will need to register a new GitHub App and install it in your GitHub organization.

### Register a new GitHub App
1. Visit the settings page in your GitHub organization's profile, and click on GitHub Apps under Developer settings.
1. Click **New GitHub App**. You'll see a form where you can enter details about your app.
1. Give your app a name. This can be anything you'd like.
1. For the "Homepage URL", use the domain issued by Smee. For example: `https://smee.io/qrfeVRbFbffd6vD`
1. For the "Webhook URL", again use the domain issued by Smee.
1. For the "Webhook secret", create a password to secure your webhook endpoints. 
    
    You can use the following command to generate a random string with high entropy:
    ```
    ruby -rsecurerandom -e 'puts SecureRandom.hex(20)'
    ```
    You'll need this secret again later, so make note of it somewhere before moving on.

1. Under Permissions, specify the following set of permissions for your app:
    * **Repository administration** (Read & Write)
    * **Issues** (Read & Write)

1. At the bottom of the page, specify whether this is a private app or a public app. For now, leave the app as private by selecting **Only on this account**.

1. Click **Create GitHub App** to create your app!

### Save your private key and App ID

After you create your app, you'll be taken back to the app settings page. You have two more things to do here:

1. **Generate a private key for your app**. This is necessary to authenticate your app later on. Scroll down on the page and click **Generate a private key**. Save the resulting PEM file in a directory where you can find it again.

1. **Note the app ID GitHub has assigned your app**. You'll need this later when you [set your environment variables](#Set-environment-variables).

### Install the app on your organization account

Now it's time to install the app. From your app's settings page, do the following:

1. Click **Install App** in the sidebar. Next to your organization name, click **Install**.

1. You'll be asked whether to install the app on all repositories or selected repositories. Select **All repositories**.

1. Click **Install**.


## Install

Run the following command to clone this repository:

```
git clone https://github.com/parkerbxyz/master-branch-protector.git
```

Install dependencies by running the following command from the project directory:

```
gem install bundler && bundle install
```

With the dependencies installed, you can [start the server](#Start-the-server).

## Set environment variables

1. Create a copy of the `.env-example` file called `.env`.

    ```
    cp .env-example .env
    ```
    
1. Add your GitHub App's private key, app ID, and webhook secret to the `.env` file.
    
    **Note:** Copy the entire contents of your PEM file as the value of `GITHUB_PRIVATE_KEY` in your `.env` file. 
    <br />
    Because the PEM file is more than one line you'll need to add quotes around the value like the example below:

    ```
    PRIVATE_KEY="-----BEGIN RSA PRIVATE KEY-----
    ...
    HkVN9...
    ...
    -----END RSA PRIVATE KEY-----"
    GITHUB_APP_IDENTIFIER=12345
    GITHUB_WEBHOOK_SECRET=your-webhook-secret
    ```

## Start the server

1. Run `ruby server.rb` on the command line. You should see a response like:

    ```
    == Sinatra (v2.0.3) has taken the stage on 3000 for development with backup from Puma
    Puma starting in single mode...
    * Version 3.11.2 (ruby 2.4.0-p0), codename: Love Song
    * Min threads: 0, max threads: 16
    * Environment: development
    * Listening on tcp://localhost:3000
    Use Ctrl-C to stop
    ```

1. View the Sinatra app at `localhost:3000` to verify your app is connected to the server.

The web service should now running and watching for new repositories to be created within your organization! 🚀

When you create a new repository in your organization, you should see some output in the Terminal tab where you started `server.rb` that looks something like this:

```
D, [2019-05-27T16:59:24.136072 #56585] DEBUG -- : ---- received event repository
D, [2019-05-27T16:59:24.136107 #56585] DEBUG -- : ----    action created
D, [2019-05-27T16:59:25.351392 #56585] DEBUG -- : Protecting master branch
D, [2019-05-27T16:59:25.739671 #56585] DEBUG -- : Creating a new issue
140.82.115.69 - - [27/May/2019:16:59:26 -0400] "POST /event_handler HTTP/1.1" 200 - 2.4251
127.0.0.1 - - [27/May/2019:16:59:24 EDT] "POST /event_handler HTTP/1.1" 200 0
- -> /event_handler
```

This means your app is running on the server as expected. 🙌

If you don't see the output, make sure Smee is running correctly in another Terminal tab.

## Troubleshooting

If you run into any problems, check out the Troubleshooting section in the "[Setting up your development environment](https://developer.github.com/apps/quickstart-guides/setting-up-your-development-environment/#troubleshooting)" quickstart guide on developer.github.com. If you run into any other trouble, you can [open an issue](https://github.com/parkerbxyz/master-branch-protector/issues/new) in this repository.


## References

* [Setting up your development environment | GitHub Developer Guide](https://developer.github.com/apps/quickstart-guides/setting-up-your-development-environment/)
* [Using the GitHub API in your app | GitHub Developer Guide](https://developer.github.com/apps/quickstart-guides/using-the-github-api-in-your-app/)
* [Branches | GitHub Developer Guide](https://developer.github.com/v3/repos/branches/#update-branch-protection)
* [github-developer/using-the-github-api-in-your-app](https://github.com/github-developer/using-the-github-api-in-your-app)
* https://octokit.github.io/octokit.rb/Octokit/Client.html
