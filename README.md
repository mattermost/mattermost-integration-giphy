# Giphy Integration Service for Mattermost

This integrations service is used to enable an external search engine ([Giphy](https://en.wikipedia.org/wiki/Giphy)) to be queried based on commands issued in a Mattermost channel using Mattermost [outgoing webhooks](https://github.com/mattermost/platform/blob/master/doc/integrations/webhooks/Outgoing-Webhooks.md). 

Once installed, users can type `gif: keyword` to send a query to the Giphy search engine and return with a post containing one non-deterministic search result from the Giphy database of animated GIF files matching `keyword`. The animation will appear below in the posted message. 

Powered by [Giphy](http://giphy.com/).

## Project Goal

The goal of this project is to provide a fully-functional template on which the Mattermost community can create their own integration services. Community members are invited to fork this repo to add improvements and to create new integrations. 

To have your work included on the [Mattermost integrations page](http://www.mattermost.org/community-applications/), please mail info@mattermost.com or tweet to [@MattermostHQ](https://twitter.com/mattermosthq). 
## Requirements

To run this integration you need:

1. A **web server** supporting Python 2.7 or compatible versions.
2. A **[Mattermost account](http://www.mattermost.org/)** [where outgoing webhooks are enabled](https://github.com/mattermost/platform/blob/master/doc/integrations/webhooks/Outgoing-Webhooks.md#enabling-outgoing-webhooks)

Many web server options will work, below we provide instructions for [**Heroku**](README.md#heroku-based-install) and a general [**Linux/Ubuntu**](README.md#linuxubuntu-1404-web-server-install) server.

### Heroku-based Install

To install this project using Heroku, you will need: 

1. A Heroku account, available for free from [Heroku.com](http://heroku.com)
2. A GitHub account, available for free from [GitHub.com](http://github.com)

Here's how to start:

1. **Create a copy of this project to manipulate**
  1. Log in to your GitHub account. Go to the [Github repository of this project](https://github.com/mattermost/mattermost-integration-giphy/) click **Fork** in the top-right corner to create a copy of this project that you can control and manipulate

2. **Deploy your project copy to Heroku**
  1. Go to your [Heroku Dashboard](https://dashboard.heroku.com/apps) and click **+** in the top-right corner then **Create New App** 
  2. Give your app a unqiue name (like `mattermost-giphy-[YOUR_GITHUB_USERNAME]`), select your region and click **Create App**
  2. Heroku directs you to the *Deploy* tab of the dashboard for your new app, select **GitHub** as your connection option, then click **Connect to GitHub** at the bottom of the screen to authorize Herkou to access your GitHub account
  3. In the pop up window, click **Authorize Application** to allow Heroku to access your accounts repositories. This step does not apply if you've already connected your GitHub account to Heroku. 
  4. On your Heroku dashboard, select your GitHub account in the first drop-down, type `mattermost-integration-giphy` in the *repo-name* field, then click **Search** and then the **Connect** button once Heroku finds your repository
  4. Scroll to the bottom of the new page. Under the *Manual Deploy* section, make sure the **master** branch is selected then click **Deploy Branch**. After a few seconds you'll see a confirmation that the app has been deployed
  5. At the top of your app dashboard, click on the **Settings** tab and scroll down to the *Domains* section. Copy the URL below *Heroku Domain* (we'll refer to this as `http://<your-heroku-domain>/` and we'll need it in the next step)
  6. Leave your Heroku interface open as we'll come back to it to finish the setup

3. **Connect your project to your Mattermost account for outgoing webhooks**
 1. Log in to your Mattermost account. Click the three dot menu at the top of the left-hand side and go to **Account Settings** > **Integrations** > **Outgoing Webhooks**
 2. Under *Add a new outgoing webhook*, leave the *Channel* unselected and enter `gif:` into **Trigger Words**. You may select a channel if you only want this integration to be available in a specified channel
 3. Paste your Heroku domain into *Callback URLs*, making sure to add `http://` to the beginning and `/new_post` to the end so it looks similar to `http://myapp.heroku.com/new_post` and click **Add**
 4. Copy the *Token* from your newly created webhook that appears under the *Existing outgoing webhooks* section
 5. Go back to your Heroku app dashboard under the *Settings* tab. Under the *Config Variables* section, click **Reveal Config Vars**
 6. Type `MATTERMOST_TOKEN` as the *KEY* and paste in the token you copied as the *VALUE*, and click **Add**

That's it! Waiting a few minutes for the Heroku process to restart you should be able to type `gif: hello` into any channel and see a GIF from Gihpy's translate service.

### Linux/Ubuntu 14.04 Web Server Install

The following procedure shows how to install this project on a Linux web server running Ubuntu 14.04. The following instructions work behind a firewall so long as the web server has access to your GitLab and Mattermost instances. 

To install this project using a Linux-based web server, you will need a Linux/Ubuntu 14.04 web server supporting Python 2.7 or a compatible version. Other compatible operating systems and Python versions should also work. 

Here's how to start:

1. **Set up this project to run on your web server**
 1. Set up a **Linux Ubuntu 14.04** server either on your own machine or on a hosted service, like AWS.
 2. **SSH** into the machine, or just open your terminal if you're installing locally
 3. Confirm **Python 2.7** or a compatible version is installed by running:
    - `python --version` If it's not installed you can find it [here](https://www.python.org/downloads/)
 4. Install **pip** and other essentials
    - `sudo apt-get install python-pip python-dev build-essential`
 5. Clone this GitHub repo with
    - `git clone https://github.com/mattermost/mattermost-integration-giphy.git`
    - `cd mattermost-integration-giphy`
 6. Install integration requirements
    - `sudo pip install -r requirements.txt`

2. **Set up your Mattermost outgoing webhooks**
 1. Log in to your Mattermost account. Click the three dot menu at the top of the left-hand side and go to **Account Settings** > **Integrations** > **Outgoing Webhooks**
 2. Under *Add a new outgoing webhook*, leave the *Channel* unselected and enter `gif:` into *Trigger Words*. You may select a channel if you only want this integration to be available in a specified channel
 3. Paste your Web Server domain into *Callback URLs*, making sure to add `http://` to the beginning and `/new_post` to the end so it looks similar to `http://<your-web-server-domain>/new_post` and click **Add**
 4. Copy the *Token* from your newly created webhook that will under the *Existing outgoing webhooks* section

3. **Run the server with the correct configuration**
 7. Back on SSH or your terminal, add the following lines to your `~/.bash_profile`
    - `export MATTERMOST_GIPHY_TOKEN=<your-token-here>` This is the token you copied in the last section
    - `export MATTERMOST_GIPHY_PORT=<your-port-number>` The port number you want the integration to listen on (defaults to 5000)
 8. Source your bash profile
    - `source ~/.bash_profile`
 9. Run the server
    - `python server.py`

That's it! You should be able to type `gif: hello` into any channel and see a GIF from Gihpy's translate service.

### Production Setups

If you'd like to use this integration in a production envrionment, it is strongly recommended that you get a production Giphy API key from [here](http://api.giphy.com/submit). Once you have that you can configure the integration to use it:

##### On Heroku
1. Go to your [Heroku Dashboard](https://dashboard.heroku.com/apps) and click on your app
2. Click the **Settings** tab. Under the *Config Variables* section, click **Reveal Config Vars**
3. For *KEY* type in `GIPHY_API_KEY` and for *VALUE* paste in your Giphy API key, then click **Add**
4. Wait a minute for the Heroku process to restart

##### On Linux/Ubuntu 14.04 Web Server
1. Stop the process currently running the integration
1. Add the following lines to your `~/.bash_profile` or `~/.bashrc` 
   - `export GIPHY_API_KEY=<your-api-key-here>` With your Giphy API key
2. Source your bash profile
   - `source ~/.bash_profile` or `source ~/.bashrc`
3. Run the server again
   - `python server.py`
