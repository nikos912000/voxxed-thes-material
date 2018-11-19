### Repos to index
| Name             | Git url                                         |
| ---------------- |:-----------------------------------------------:|
| Hound            | https://github.com/etsy/hound.git               |
| Zoekt            | https://github.com/google/zoekt.git             |
| SearchcodeServer | https://github.com/boyter/searchcode-server.git |

### Hound
1. `mkdir data`
2. `cd data` 
3. `touch config.json`
4. `subl config.json`
5. Write the following configs
```
{
    "max-concurrent-indexers" : 1,
    "dbpath" : "data",
    "repos" : {
        "Hound" : {
            "url" : "https://github.com/etsy/hound.git"
        },
        "Zoekt" : {
            "url" : "https://github.com/google/zoekt.git"
        },
        "SearchcodeServer" : {
            "url" : "https://github.com/boyter/searchcode-server.git"
        }
    }
}
```
6. Run using the command below:
`docker run -p 6080:6080 --name hound --rm -v /Users/<user>/voxxed_demo/hound:/data etsy/hound`

Service running under: [http://localhost:6080/](http://localhost:6080/)

**Queries**:
- Simple + deeplinks + load all matches:
  - match
- Regex + special chars: 
  - match.*:
- Repo filter:
  - match.*:
  - repo=Hound
- Filter filter:
  - match.*: 
  - repo=Hound
  - files=.*.go
- Case sensitive:
  - Match.*:
  - repo=Hound
  - files=.*.go
  - ignore case (i=fosho)

### Searchcode Server
1. Run using the command below:
`docker run -p 8080:8080 --name searchcode --rm searchcode/searchcode-server-community`
Service running under: [http://localhost:8080/](http://localhost:8080/)
2. Go to [http://localhost:8080/admin/repo/](http://localhost:8080/admin/repo/) (pass: *Adm1n234*) 
3. Add the following repos:
- Hound
  - *Repository name*: Hound
  - *Repository location*: https://github.com/etsy/hound.git
- SearchcodeServer 
  - *Repository name*: SearchcodeServer 
  - *Repository location*: https://github.com/boyter/searchcode-server.git
- Zoekt 
  - *Repository name*: Zoekt 
  - *Repository location*: https://github.com/google/zoekt.git

*Repolist*: [http://localhost:8080/admin/repolist/](http://localhost:8080/admin/repolist/)
*Admin*: [http://localhost:8080/admin/](http://localhost:8080/admin/)

**Queries**:
- Simple + click on file (stored locally) + file tree + repo info:
  - match
- No case sensitive, regex
- Wildcard + Repo/language filter:
  - match*
  - repo=SearchcodeServer
  - lan=java
- Filter filter:
  - match* fl:/src/test
  - repo=SearchcodeServer
  - lan=java
- Fuzzy and proximity searches

*Repositories*: [http://localhost:8080/repository/list/](http://localhost:8080/repository/list/)
*Documentation*: [http://localhost:8080/documentation/](http://localhost:8080/documentation/)

## Zoekt

Clone the repos locally into `/zoekt-demo`
1. `export GOPATH=/Users/<user>/go`
2. `export REPODIR=/Users/<user>/zoekt-demo`
3. `cd zoekt`
4. Index repos:
    - `$GOPATH/bin/zoekt-git-index -branches master $REPODIR/zoekt/`
    - `$GOPATH/bin/zoekt-git-index -branches master $REPODIR/hound/`
    - `$GOPATH/bin/zoekt-git-index -branches master $REPODIR/searchcode-server/`
5. Run webserver:
    - `$GOPATH/bin/zoekt-webserver -listen :6070`
    *Service running under*: [http://localhost:6070](http://localhost:6070)

**Queries**:
- Repos:
  - r:
- Simple + deeplinks + max results + branch:
  - match
- Regex + special chars: 
  - match.*:
  - strings.TrimPrefix\\(fMatch.FileName\\[len\\(f.SubRepositoryPath\\):\\], \\"/\\"\)
- Repo filter:
  - match.*: r:zoekt
- File filter:
  - match.*: r:zoekt f:md
  - or match.*: r:zoekt -f:go
- Case sensitive:
  - match.*: r:zoekt case:yes
- Line skipped:
  - match f:featherlight.min.js
- Duplicate detection

### Sourcegraph

1. Run service using the command below:
`docker run --publish 7080:7080 --name sourcegraph --rm -v ~/.sourcegraph/config:/etc/sourcegraph -v ~/.sourcegraph/data:/var/opt/sourcegraph -v /var/run/docker.sock:/var/run/docker.sock sourcegraph/server:2.11.2`
*Service running under*: [http://localhost:7080](http://localhost:7080)

2. Go to admin page: [http://localhost:7080/site-admin](http://localhost:7080/site-admin).
3. Add repos if not there:
```
{
  "auth.providers": [
    {
      "type": "builtin"
    }
  ],
  "disablePublicRepoRedirects": true,
  "maxReposToSearch": 50,
  "repos.list": [
    {
      "url": "https://github.com/etsy/hound.git",
      "path": "Hound"
    },
    {
      "url": "https://github.com/google/zoekt.git",
      "path": "Zoekt"
    },
    {
      "url": "https://github.com/boyter/searchcode-server.git",
      "path": "SearchcodeServer"
    }
  ]
}
```

**Queries**:
- Simple + autocomplete:
  - match
- Regex + special chars: 
  - match.*:
  - strings.TrimPrefix\\(fMatch.FileName\\[len\\(f.SubRepositoryPath\\):\\], \\"/\\"\)
- Repo filter:
  - match.*: repo:^Zoekt$ 
- File filter:
  - match.*: repo:^Zoekt$ file:\.md$ 
  - or match.*: repo:^Zoekt$ -file:\.go$
- Case sensitive:
  - match.*: repo:^Zoekt$ case:yes