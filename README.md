# Giphy Integration Service for Mattermost

Mattermost community members are invited to fork this repo to create new integrations. To have your integration referenced on http://www.mattermost.org/webhooks/, please mail info@mattermost.com or tweet to [@MattermostHQ](https://twitter.com/mattermosthq). 

Powered by [Giphy](http://giphy.com/).

## Requirements

To run this integration you need:

1. A **web server** supporting Python 2.7 or a compatible version to run this software (optionally, you could use a service provider like [Heroku](http://heroku.com) - see instructions below)
3. A **[Mattermost account](http://www.mattermost.org/)** [where outgoing webhooks are enabled](https://github.com/mattermost/platform/blob/master/doc/integrations/webhooks/Outgoing-Webhooks.md#enabling-outgoing-webhooks)

Regarding 1. there are many options for web servers you can use, below we provide instructions for both [**Heroku**](README.md#heroku-based-install) and a general [**Linux/Ubuntu**](README.md#linuxubuntu-1404-web-server-install) server to get something running:

### Heroku-based Install

To install this project using Heroku, you will need: 

1. A **Heroku account**, available for free from [Heroku.com](http://heroku.com)
2. A **GitHub account**, available for free from [GitHub.com](http://github.com)

Here's how to start:

1. **Create a copy of this project to manipulate**
  1. From the [Github repository of this project](https://github.com/mattermost/mattermost-integration-giphy/) click **Fork** in the top-right corner to create a copy of this project that you control and can update as you like.
2. **Deploy your project copy to Heroku**
  1. Go to your [**Heroku Dashboard**](https://dashboard.heroku.com/apps) and click **+** in the top-right corner then **New App**. Give your app a unqiue name (like `mattermost-giphy-[YOUR_GITHUB_USERNAME]`), select your region and click **Create App**.
  2. On the **Deploy** screen, select **GitHub** at the top, then click **Connect to GitHub** to authorize Herkou to access your GitHub account
  3. Select your account and search for your repo name by typing `mattermost-integration-giphy` in the **repo-name** field, then click **Search** and then the **Connect** button next to your repository
  4. Scroll to the bottom of the new page and under the **Manual Deploy** section click **Deploy Branch**, making sure the `master` branch is selected
  5. Go to **Settings** > **Domains** > **Settings** and copy the URL below **Heroku Domain** (we'll refer to this as `http://<your-heroku-domain>/` and we'll need it in the next step)
  6. Leave your Heroku interface open as we'll come back to it to finish the setup

4. **Connect your project to your Mattermost account for outgoing webhooks**
 1. Log in to your Mattermost account, and from three dot icon at the top of the left-hand menu go to **Account Settings** > **Integrations** > **Outgoing Webhooks** > **Edit**
 2. Under **Add a new outgoing webhook**, leave the **Channel** unselected and enter `/gif` into **Trigger Words**
 3. Paste your **Heroku Domain** into **Callback URLs**, making sure to add `http://` to the beginning and `/new_post` to the end so it looks similar to `http://myapp.heroku.com/new_post` and click **Add**
 4. Copy and highlight the **Token** from you newly created webhook that should appear in the **Existing outgoing webhooks** section below
 5. Back on your Heroku dashboard, under **Settings** -> **Reveal Config Vars**, add `MATTERMOST_TOKEN` as the **KEY** and paste in the token you copied as the **VALUE**, and click **Add**

That's it! After waiting a minute for the Heroku process to restart you should be able to type `/gif hello` into any channel and see a GIF from Gihpy's translate service.

### Linux/Ubuntu 14.04 Web Server Install

The following procedure shows how to install this project on a Linux web server running Ubuntu 14.04. The following instructions work behind a firewall so long as the web server has access to your GitLab and Mattermost instances. 

To install this project using a Linux-based web server, you will need:

1. A Linux/Ubuntu 14.04 web server supporting Python 2.7 or a compatible version. Other compatible operating systems and Python versions should also work. 

Here's how to start:

1. **Set up this project to run on your web server**
 1. Set up a **Linux Ubuntu 14.04** server either on your own machine or on a hosted service, like AWS.
 2. **SSH** into the machine, or just open your terminal if you're installing locally.
 3. Confirm **Python 2.7** or a compatible version is installed by running:
    - `python --version`
    -  If it's not installed you can find it [here](https://www.python.org/downloads/)
 4. Install **pip** and other essentials
    - `sudo apt-get install python-pip python-dev build-essential`
 5. Clone this GitHub repo with
    - `git clone https://github.com/mattermost/mattermost-integration-giphy.git`
    - `cd mattermost-integration-giphy`
 6. Install integration requirements
    - `sudo pip -r requirements.txt`

2. **Set up your Mattermost outgoing webhooks**
 1. Log in to your Mattermost account, and from three dot icon at the top of the left-hand menu go to **Account Settings** > **Integrations** > **Outgoing Webhooks** > **Edit**
 2. Under **Add a new outgoing webhook**, leave the **Channel** unselected and enter `/gif` into **Trigger Words**
 3. Paste your **Heroku Domain** into **Callback URLs**, making sure to add `http://` to the beginning and `/new_post` to the end so it looks similar to `http://myapp.heroku.com/new_post` and click **Add**
 4. Copy and highlight the **Token** from you newly created webhook that should appear in the **Existing outgoing webhooks** section below

3. **Run the server with the correct configuration**
 7. Back on SSH or your terminal, add the following lines to your `~/.bash_profile`
    - `export MATTERMOST_TOKEN=<your-token-here>` This is the token you copied in the last section
    - `export PORT=<your-port-number>` The port number you want the integration to listen on (defaults to 5000)
 8. Source your bash profile
    - `source ~/.bash_profile`
 9. Run the server
    - `python server.py`

That's it! You should be able to type `/gif hello` into any channel and see a GIF from Gihpy's translate service.

### Production Setups

If you'd like to use this integration in a production envrionment, it is strongly recommended that you get a production Giphy API key from [here](http://api.giphy.com/submit). Once you have that you can configure the integration to use it:

##### On Heroku
1. Go to your [**Heroku Dashboard**](https://dashboard.heroku.com/apps) and click on your app
2. Go to **Settings** -> **Config Variables** and click **Reveal Config Vars**
3. For **KEY** type in `GIPHY_API_KEY` and for **VALUE** paste in your Giphy API key, then click **Add**
4. Wait a minute for the Heroku process to restart

##### On Linux/Ubuntu 14.04 Web Server
1. Stop the process currently running the integration
1. Add the following lines to your `~/.bash_profile`
   - `export GIPHY_API_KEY=<your-api-key-here>` With your Giphy API key
2. Source your bash profile
   - `source ~/.bash_profile`
3. Run the server again
   - `python server.py`
