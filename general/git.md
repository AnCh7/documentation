## Useful

#### Clone all repos from a github organization

```python
#!/usr/bin/python

import requests
import json
import subprocess
from git import Repo
import os


github_org = "xxxxxxx"
access_token="xxxxxxx"
current_dir = os.path.dirname(os.path.realpath(__file__))

page = 0
while True:
	print("Started")

	url = "https://api.github.com/orgs/" + github_org + "/repos?per_page=100&access_token=" + access_token + "&page=" + str(page)
	page = page + 1

	headers = {'Authorization': 'token ' + access_token}
	response = requests.get(url, headers=headers, auth=('username', access_token))

	repos = json.loads(response.text)
	if len(repos) == 0:
		break

	for repo in repos:
		try:
			Repo.clone_from(repo['clone_url'], current_dir + "/" + repo['name'])
			print("Cloning repository: " + repo['name'])
		except Exception as error:
			if "already exists and is not an empty directory" in error.stderr:
				continue
			else:
				raise

print("Finished")
```

#### Update all repositories

```python
#!/usr/bin/python

import git
import os

if __name__ == "__main__":
    sub_dirs = next(os.walk('.'))[1]
    for sub_dir in sub_dirs:
        try:
            print("============= Processing repository: " + sub_dir)
            repo = git.Repo(sub_dir)
        except git.InvalidGitRepositoryError as error:
            print("Invalid repository. Skip")
            continue

        if len(repo.branches) == 0:
            print("No branches found. Skip")
            continue

        print("Discard any current changes")
        repo.git.reset('--hard')

        try:
            origins = repo.remotes.origin.pull(allow_unrelated_histories=True)
            print("Pull remote branches:")
            for origin in origins:
                print("- " + str(origin))
        except git.GitCommandError as error:
            print("Repository not found. Skip")
            continue

        print("Active branch is: "+repo.active_branch.name)

print("Finished")
```

#### Add a team to all repositories

```go
func main() {
	// Replace with your personal access token, organization and team names.
	accessToken := "xxxxxx"
	orgName := "xxxxxx"
	teamName := "xxxxxx"

	// Init the GitHub client
	oauth2Client := oauth2.NewClient(context.Background(), oauth2.StaticTokenSource(&oauth2.Token{AccessToken: accessToken}))
	ghClient := gh.NewClient(oauth2Client)

	ctx := context.Background()

	// List all teams
	var allTeams []*gh.Team
	teamsOpts := &gh.ListOptions{PerPage: 100}
	fmt.Printf("Listing all teams\n")
	for {
		teams, resp, err := ghClient.Teams.ListTeams(ctx, orgName, teamsOpts)
		if err != nil {
			fmt.Printf("Error listing teams: %v\n", err)
			os.Exit(1)
		}
		allTeams = append(allTeams, teams...)
		if resp.NextPage == 0 {
			break
		}
		teamsOpts.Page = resp.NextPage
		fmt.Printf("Page %d of teams (total %d) \n", teamsOpts.Page, len(allTeams))
	}

	var teamID int64
	// Get teamID and print teams
	for _, team := range allTeams {
		fmt.Printf("Team ID: %d, Name: %s\n", *team.ID, *team.Name)
		if *team.Name == teamName {
			teamID = *team.ID
			fmt.Printf("Found team %s with ID %d\n", teamName, teamID)
		}
	}
	if teamID == 0 {
		fmt.Printf("Team %s not found\n", teamName)
		os.Exit(1)
	}

	var orgID int64
	// Get organization details
	org, _, err := ghClient.Organizations.Get(ctx, orgName)
	if err != nil {
		fmt.Printf("Error getting organization details: %v\n", err)
		os.Exit(1)
	}

	// Print organization ID
	fmt.Printf("Organization ID: %d, Name: %s\n", *org.ID, *org.Name)
	orgID = *org.ID

	// List all repos in org
	var allRepos []*gh.Repository
	repoOpts := &gh.RepositoryListByOrgOptions{ListOptions: gh.ListOptions{PerPage: 100}}
	fmt.Printf("Listing all repositories\n")
	for {
		repos, resp, err := ghClient.Repositories.ListByOrg(ctx, orgName, repoOpts)
		if err != nil {
			fmt.Printf("Error listing repositories: %v\n", err)
			os.Exit(1)
		}
		allRepos = append(allRepos, repos...)
		if resp.NextPage == 0 {
			break
		}
		repoOpts.Page = resp.NextPage
		fmt.Printf("Page %d of repositories (total %d) \n", repoOpts.Page, len(allRepos))
	}

	// Iterate through each repo
	for _, repo := range allRepos {
		if repo.Archived != nil && *repo.Archived {
			fmt.Printf("Repo %s is archived, skipping\n", *repo.Name)
			continue
		}

		// Check if team is already managing the repo
		repository, resp, err := ghClient.Teams.IsTeamRepoByID(ctx, orgID, teamID, *repo.Owner.Login, *repo.Name)
		if err != nil {
			if resp.StatusCode == 404 {
				fmt.Printf("Team %s is not managing repo %s\n", teamName, *repo.Name)
			} else {
				fmt.Printf("Error checking team membership: %v\n", err)
				continue
			}
		}
		if repository != nil {
			fmt.Printf("Team %s is already managing repo %s\n", teamName, *repo.Name)
			continue
		}

		// Add team to this repo, with read-only rights
		opt := &gh.TeamAddTeamRepoOptions{Permission: "pull"}
		_, err = ghClient.Teams.AddTeamRepoByID(ctx, orgID, teamID, *repo.Owner.Login, *repo.Name, opt)
		if err != nil {
			fmt.Printf("Error adding team to repo: %v\n", err)
			continue
		}

		fmt.Printf("Successfully added team %s to repo %s with read-only rights\n", teamName, *repo.Name)
	}
}
```


## Codeowners

Code owners are automatically requested for review when someone opens a pull request that modifies code that they own.

To use a CODEOWNERS file, create a new file called `CODEOWNERS` in the root, `docs/`, or `.github/` directory of the repository, in the branch where you'd like to add the code owners.

A CODEOWNERS file uses a pattern that follows most of the same rules used in [gitignore](https://git-scm.com/docs/gitignore#_pattern_format) files, with [some exceptions](https://docs.github.com/en/github/creating-cloning-and-archiving-repositories/creating-a-repository-on-github/about-code-owners#syntax-exceptions). The pattern is followed by one or more GitHub usernames or team names using the standard `@username` or `@org/team-name` format. You can also refer to a user by an email address that has been added to their GitHub account, for example `user@example.com`.

```
# These owners will be the default owners for everything in
# the repo. Unless a later match takes precedence,
# @global-owner1 and @global-owner2 will be requested for
# review when someone opens a pull request.
*       @global-owner1 @global-owner2

# Order is important; the last matching pattern takes the most
# precedence. When someone opens a pull request that only
# modifies JS files, only @js-owner and not the global
# owner(s) will be requested for a review.
*.js    @js-owner

# You can also use email addresses if you prefer. They'll be
# used to look up users just like we do for commit author
# emails.
*.go docs@example.com

# In this example, @doctocat owns any files in the build/logs
# directory at the root of the repository and any of its
# subdirectories.
/build/logs/ @doctocat

# The `docs/*` pattern will match files like
# `docs/getting-started.md` but not further nested files like
# `docs/build-app/troubleshooting.md`.
docs/*  docs@example.com

# In this example, @octocat owns any file in an apps directory
# anywhere in your repository.
apps/ @octocat

# In this example, @doctocat owns any file in the `/docs`
# directory in the root of your repository and any of its
# subdirectories.
/docs/ @doctocat
```

## Github CLI

> References:
> https://cli.github.com/manual

##### Install
```shell
asdf plugin-add github-cli https://github.com/bartlomiejdanek/asdf-github-cli.git
asdf install github-cli 1.10.2
asdf global github-cli 1.10.2
```

##### Auth
```shell
gh auth login
```

##### Commands
```shell
gh pr create --title "xxxxxxxx" --body "yyyyyyyyy"
```