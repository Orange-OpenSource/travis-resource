# Travis Resource

Track travis builds and can trigger them.

## Source Configuration

* `repository`: *Required.* The name of the repository, e.g.
`ArthurHlt/travis-resource`.

* `branch`: *Optional.* The branch to track. Defaults to the last build found on travis.

* `check-all-builds`: *Optional.* If set to `true` it will retrieve all builds (not just succeeded ones). Defaults to `false`.

* `github-token`: *if `travis-token` it becomes unnecessary* A github token to authenticate to travis.

* `travis-token`: *if `github-token` it becomes unnecessary* A travis token to authenticate to travis.


## Behavior

### `check`: Check for new builds.

Find the last build triggered on travis for the repository set and branch set (if set).


### `in`: Fetch the last build triggered on travis and retrieve information about it.

Find information the last build triggered on travis for the repository set and branch set (if set) and place information about the build inside the destination folder.

The name of this file is `build-info.json` and will give information in this format:

```json
{
	"builds": [
		{
			"id": 93712979,
			"repository_id": 6711424,
			"commit_id": 26643017,
			"number": "18",
			"state": "passed",
			"started_at": "2015-11-28T23:02:01Z",
			"finished_at": "2015-11-28T23:04:07Z",
			"duration": 126,
			"job_ids": [
				93712980
			],
			"event_type": "push"
		}
	],
	"commits": [
		{
			"id": 26643017,
			"sha": "b3a3f680f3a3d02d2a677322b24a13aa63d2a2f6",
			"branch": "master",
			"message": "fix install script",
			"committed_at": "2015-11-28T23:01:40Z",
			"author_name": "ArthurHlt",
			"author_email": "arthurh.halet@gmail.com",
			"committer_name": "ArthurHlt",
			"committer_email": "arthurh.halet@gmail.com",
			"compare_url": "https://github.com/ArthurHlt/github-blob-sender/compare/712868bf61f0...b3a3f680f3a3"
		}
	]
}
```


### `out`: Restart a build on travis.

Restart a particular build set by user.
By default it will restart the build give by the file `build-info.json`.

#### Parameters

* `build`: *Optional.* A build number associated to a repository to restart. It can be value `latest` to trigger the latest finished build.

* `branch`: *Optional.* A branch associated to a repository to restart (It will restart the last build found in this branch). **Note:** if `build` is set this parameter will be ignored

* `repository`: *Optional.* The repository where we can found the build number in travis. If it's not set `build` parameter will be associated to the repository given in source configuration

## Example

``` yaml
resources:

- name: travis-resource
  type: travis
  source:
    repository: user/main-project
    github-token: mygithubtoken
    #or travis-token: mytravistoken
    #branch: master
    #check-all-builds: false

jobs:
- name: build-rootfs
  plan:
  - get: travis-resource
    trigger: true
  - put: travis-resource
    params:
        build: latest
        repository: user/project-with-main-project-in-dependence
        #branch: master
```