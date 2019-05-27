Adapted from [github-developer/using-the-github-api-in-your-app](https://github.com/github-developer/using-the-github-api-in-your-app).

This is an example GitHub App that automates the protection of the master branch upon repository creation. The creator of the new repository will be notified with an [@mention](https://help.github.com/en/articles/basic-writing-and-formatting-syntax#mentioning-people-and-teams) in an issue within the repository that outlines the protections that were added.

This project listens for [organization events](https://developer.github.com/webhooks/#events) and uses the [Octokit.rb](https://github.com/octokit/octokit.rb) library to make REST API calls.

## Prerequisites

This project requires you to register a new GitHub App and install in your GitHub organization. The app will need the following permissions:
* **Repository administration** (Read & Write)
* **Issues** (Read & Write)

You can learn how to configure a GitHub App by following the "[Setting up your development environment](https://developer.github.com/apps/quickstart-guides/setting-up-your-development-environment/)" quickstart guide on developer.github.com.

After creating your app, generate a private key for your app and save the resulting PEM file (called something like `app-name`-`date`-private-key.pem) in a directory where you can find it again. Also note the app ID GitHub has assigned your app. You'll need these to [set your environment variables](#Set-environment-variables).

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
2. Add your GitHub App's private key, app ID, and webhook secret to the `.env` file.
    
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
2. View the Sinatra app at `localhost:3000` to verify your app is connected to the server.

## Resources

* [Quickstart guides | GitHub Developer Guide](https://developer.github.com/apps/quickstart-guides/)
* [Branches | GitHub Developer Guide](https://developer.github.com/v3/repos/branches/#update-branch-protection)
* [github-developer/using-the-github-api-in-your-app](https://github.com/github-developer/using-the-github-api-in-your-app)
* https://octokit.github.io/octokit.rb/Octokit/Client.html
