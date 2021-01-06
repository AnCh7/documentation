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

