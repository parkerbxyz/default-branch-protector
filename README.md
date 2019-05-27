Adapted from [github-developer/using-the-github-api-in-your-app](https://github.com/github-developer/using-the-github-api-in-your-app).

This is an example GitHub App that automates the protection of the master branch upon repository creation. The creator of the new repository will be notified with an [@mention](https://help.github.com/en/articles/basic-writing-and-formatting-syntax#mentioning-people-and-teams) in an issue within the repository that outlines the protections that were added.

This project listens for [organization events](https://developer.github.com/webhooks/#events) and uses the [Octokit.rb](https://github.com/octokit/octokit.rb) library to make REST API calls.

## Prerequisites

Before you begin, you'll need to clone this repository to a directory where you'd like to store the code:

```
git clone https://github.com/parkerbxyz/master-branch-protector.git
```

To run this web service on your local machine, you will need to use a tool like Smee to send webhooks to your local machine without exposing it to the internet. If you're already comfortable with other tools that expose your local machine to the internet like [ngrok](https://ngrok.com/) or [localtunnel](https://localtunnel.github.io/www/), feel free to use those.

### Start a new Smee channel

1. Go to https://smee.io and click **Start a new channel**.

    Starting a new Smee channel creates a unique domain where GitHub can send webhook payloads. This domain is called a Webhook Proxy URL and looks something like this: `https://smee.io/qrfeVRbFbffd6vD`

    > **Note:** The following steps are slightly different than the "Use the CLI" instructions you'll see on your Smee channel page. You do **not** need to follow the "Use the Node.js client" or "Using Probot's built-in support" instructions.

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

You will also need to register a new GitHub App and install it in your GitHub organization.

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

## Resources

* [Quickstart guides | GitHub Developer Guide](https://developer.github.com/apps/quickstart-guides/)
* [Branches | GitHub Developer Guide](https://developer.github.com/v3/repos/branches/#update-branch-protection)
* [github-developer/using-the-github-api-in-your-app](https://github.com/github-developer/using-the-github-api-in-your-app)
* https://octokit.github.io/octokit.rb/Octokit/Client.html
