# How to run
Instructions on how to set up and run the services for the demo.

## Prerequisites

Docker, GO

Has been tested on MacOS High Sierra using Docker for Mac (18.06.1-ce) and Go 10.2 darwin/amd64.

## Repos to index

| Name             | Git url                                         |
| ---------------- | ----------------------------------------------- |
| Hound            | https://github.com/etsy/hound.git               |
| Zoekt            | https://github.com/google/zoekt.git             |
| SearchcodeServer | https://github.com/boyter/searchcode-server.git |

## Hound
*Website*: [https://github.com/etsy/hound](https://github.com/etsy/hound)

### How to Run
1. Create `config.json` in `<data_dir>`:
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
2. ```docker run -p 6080:6080 --name hound --rm -v <data_dir>:/data etsy/hound```
    - Running under: [http://localhost:6080/](http://localhost:6080/)

## Searchcode Server
*Website*: [https://github.com/boyter/searchcode-server](https://github.com/boyter/searchcode-server)

### How to Run
1. ```docker run -p 8080:8080 --name searchcode --rm searchcode/searchcode-server-community```
    - Running under: *http://localhost:8080/*

2. To add repos, go to [http://localhost:8080/admin/repo/](http://localhost:8080/admin/repo/) (pass: *Adm1n234*) 
3. Add the following repos:
    - **Hound**
        - *Repository name*: Hound
        - *Repository location*: https://github.com/etsy/hound.git
    - **SearchcodeServer**
        - *Repository name*: SearchcodeServer 
        - *Repository location*: https://github.com/boyter/searchcode-server.git
    - **Zoekt** 
        - *Repository name*: Zoekt 
        - *Repository location*: https://github.com/google/zoekt.git 

## Zoekt
*Website*: [https://github.com/google/zoekt](https://github.com/google/zoekt)

### How to Run
First, clone the repos locally into `<repos_dir>`.

`export GOPATH=<GO_path>`

`export REPODIR=<repos_dir>`

#### Run indexer
`$GOPATH/bin/zoekt-git-index -branches master $REPODIR/zoekt/`

`$GOPATH/bin/zoekt-git-index -branches master $REPODIR/hound/`

`$GOPATH/bin/zoekt-git-index -branches master $REPODIR/searchcode-server/`

#### Run webserver
`$GOPATH/bin/zoekt-webserver -listen :6070`
- Service running under: [http://localhost:6070](http://localhost:6070)
- Indexes under: *$HOME/.zoekt*

### Sourcegraph
Website: [https://about.sourcegraph.com/docs](https://about.sourcegraph.com/docs)

### How to Run
1. `docker run -p 7080:7080 --name sourcegaph --rm -v ~/.sourcegraph/config:/etc/sourcegraph -v ~/.sourcegraph/data:/var/opt/sourcegraph -v /var/run/docker.sock:/var/run/docker.sock sourcegraph/server:2.11.2`
- Service running under: [http://localhost:7080](http://localhost:7080)

2. Update the repos.list in [http://localhost:7080/site-admin/configuration](http://localhost:7080/site-admin/configuration) as below:
```
{
  ...
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
  ...
}
```
