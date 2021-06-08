# Github and Slack - DevOps Management

## Overview

The provided sample includes:

- Generating GitHub API access tokens via OAuth 2.0.
- Authenticating and connecting to the GitHub API via Linx.
- Get a bot user token in Slack
- Post messages to Slack using Bot User for GitHub commits for a time period. 

This sample shows how Linx automatically post messages to Slack.
Once this GitHub-Slack integration is active, the sample posts messages to Slack Channel. 

---

### Additional resources

- [GitHub-Linx integration guide](https://community.linx.software/community/t/oauth-2-0-authentication-github-example/487)
- [GitHub API authentication documentation](https://docs.github.com/en/rest)
- [GitHub API documentation](https://docs.github.com/en/rest/reference/repos#list-organization-repositories)
- [Create New App in Slack](https://api.slack.com/apps)
- [Slack API documentation](https://api.slack.com/apis)
- [Test apis from browser](https://api.slack.com/methods/chat.postMessage/test)
- [Slack reference for block kit](https://api.slack.com/reference/block-kit)
---

## Dependencies

### Pre-requisites

- Linx Designer
- Github account
- Slack account

### Linx Designer

This solution was developed in the Linx Designer `v5.21.0.0`

---

## Setting up the sample

#### Register a new app on GitHub:

1. Register a new 'OAuth application' on GitHub
1. Configure the 'Authorization callback URL' to be: `http://localhost:8080/oauth/token`
1. Save your app.
1. Generate your **client secret**.
1. Copy the **Client ID** and **Client Secret**.

#### Configure the Solution's $.Settings for GitHub :

1. Open the solution in your Linx Designer.
1. Edit the $.Settings values:

   - `GitHubClientId`: Your GitHub app’s **Client ID**
   - `GitHubClientSecret`: Your GitHub app’s **Client Secret**
   - `GitHubOwner`: Insert the owner of your repository.  E.g From the link https://github.com/linx-software, linx-software is the owner.   

1. Save the Solution.

Generate access tokens:

1. Start the debugger on the `RESTHost service` in `Generic_OAuth2` Folder in the Linx Designer.
2. Make a request in your browser to `http://localhost:8080/oauth/authorize`
3. You will be redirected to the GitHub OAuth 2.0 access consent screen.
4. Authorize the connected application.
5. View success message.
Note that access token generated is stored in the file's path defined in `TokenLogPath` in Solution's $.Settings
---
#### Get a bot user token from Slack
1. [Create a new app](https://api.slack.com/apps)
2. Choose from Scratch.  
      - Enter App Name, e.g linx-bot-github
      - Choose a workspace and click on create app

3. Under 'Add features and functionality' 
      - Click on **Bots**

4. Under 'First, assign a scope to your bot token'
      - Click on **Review Scopes to Add** 
5. Go to `Scopes` section
      - Click on **Add an OAuth Scope**
       - Choose the following from the list:
       	- im:history
       	- chat:write
       	- channels:history
       	- im:write
       	- chat:write.public
6. As the above is done, a message `You can now show tabs on App HomeManage which tabs your user sees in your app’s home. Go to App Home` will appear.
7. Scroll the page up and go to the section `OAuth Tokens for Your Workspace`
8. Click on the button **Install to workspace** and click on **Allow**
9. Once successful, you will be directed to the `OAuth Tokens for Your Workspace` section
10. Click on the **Copy** Button to copy the Bot User OAuth Token. Note that it starts with `xoxb-`

#### Configure the Solution's $.Settings for Slack :
1. `SlackBotUserOAuth`: Paste the **token** copied above
2. `SlackChannel` : Enter **channel Id or channel name**. [How to find your Channel Id in Slack](https://stackoverflow.com/questions/40940327/what-is-the-simplest-way-to-find-a-slack-team-id-and-a-channel-id)   
       
---

## Using the sample

### Authentication for GitHub

After your app has been successfully authorized, the access token is stored in a local text file.

Requests are authenticated via a Bearer access token included in the `Authorization` header of each request.

The token is retrieved from the file before each request is made.

### Making requests

When making requests to the GitHub, you must also include the following header:

```http
User-Agent: Mozilla/5.0 (Windows NT 6.1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/47.0.2526.111 YaBrowser/16.3.0.7146 Yowser/2.5 Safari/537.36
```
### Authentication for Slack

Requests are authenticated via a Bearer access token included in the `Authorization` header of each request.

When making requests to the GitHub, you must also include the following header:

```
Accept: application/x-www-form-urlencoded , application/json
```
---

## GitHub Utilities samples 

### GetReposForOwner

Lists repositories for the specified organization.

https://docs.github.com/en/rest/reference/repos#list-organization-repositories

### GetCommits

The Repo Commits API supports listing, viewing, and comparing commits in a repository.

https://docs.github.com/en/rest/reference/repos#list-commits

Parameters:

| Parameter      |    Type            |   					    |
| -------------  |------------- | ------------------------------------------|
| repo         |string query   | Repositories' name                    |
| since         |string query   | Only show notifications updated after the given time. This is a timestamp in ISO 8601 format: YYYY-MM-DDTHH:MM:SSZ.|
| until        |string query   | Only commits before this date will be returned. This is a timestamp in ISO 8601 format: YYYY-MM-DDTHH:MM:SSZ.|
| per_page     |integer query  | Results per page (max 100). Default: 30. |

---
## Slack Utilities samples 
### AuthorizationTest
A function to test if you've entered the right token and the right channel.  Tests the api https://slack.com/api/auth.test.

### GetCustomizedCommitsForRepos
Reads commits from GitHub and populates cutomized types.
- Parameters:
   - Same parameters as in GetCommits above.
- Result:
   - `commitList` : List of customized commits
### BuildBlocks
Messages can be sent to Slack with different parameters.  In this section, we build blocks that are sent as JSON to Slack.
- Parameters:
   - Same parameters as in GetCustomizedCommitsForRepos above.
   - 'commitList' from 'GetCustomizedCommitsForRepos' above   
- Result:
   - `blocks` : String type that stores the JSON blocks
### PostMessageAPI
Calls the Post message API https://api.slack.com/methods/chat.postMessage
- Parameters:
   - `blocks` : String type in JSON format  
   - `text`:  String type.     
---
## Running the Sample

In the Slack Folder, Click on the function named PostMessageToSlack
Enter parameters as follows:

- `Per_page`: 1
- `Since`: start date of commits (e.g Yesterday's date or any other date before ‘until date below’: 2021-05-26)
- `Until`: end date of commits (e.g Today's date : 2021-05-27)
---
If you want to post message to a private channel or and receiving **channel_not_found** message, you should invite the bot to the channel. A bot can't post in a private channel if it's not a member. 



