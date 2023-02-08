## Useful

Clone all repos from a github organization

```python
#!/usr/bin/python
# https://docs.github.com/en/free-pro-team@latest/rest/reference/repos

import requests
import json
import subprocess

GITHUB_ORGANIZATION = "xxxxxxx"
ACCESS_TOKEN="xxxxxxx"
CLONE_DIR="./"

page = 0
while True:
	print("============ Started")
	url = "https://api.github.com/orgs/" + GITHUB_ORGANIZATION + "/repos?per_page=100&access_token=" + ACCESS_TOKEN + "&page=" + str(page)
	print(url)
	page = page + 1

	headers = {'Authorization': 'token ' + ACCESS_TOKEN}
	r = requests.get(url, headers=headers, auth=('username', ACCESS_TOKEN))

	data = json.loads(r.text)
	if len(data) == 0:
		print("============ Finished")
		break

	for repo in data:
	    print("============ Cloning repo: " + repo['full_name'])
	    p = subprocess.Popen(["git","clone", repo['clone_url']], cwd=CLONE_DIR)  
	    p.wait()
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
>
> https://cli.github.com/manual



Install

```shell
asdf plugin-add github-cli https://github.com/bartlomiejdanek/asdf-github-cli.git
asdf install github-cli 1.10.2
asdf global github-cli 1.10.2
```

Auth

```shell
gh auth login
```
