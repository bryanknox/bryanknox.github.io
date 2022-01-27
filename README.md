# BryanKnox's GitHub Pages

## Testing in LocalHost

Uses the official Jekyll Docker Image ([jekyll/jekyll](https://hub.docker.com/r/jekyll/jekyll)) and Docker Compose.

### Serve Jekyll site

```bash
docker-compose up
```

That will start the docker-container and run Jekyll. Folders and ports are mapped and the homepage can be browsed to at:

http://localhost:4000/

Added or changed files will be automatically served.


### Stop Serving Jekyll site

`Ctrl+C` in the terminal will stop the Jekyll server.

### Clean up


```bash
docker-compose down
```
That will remove the docker-container.

### Remove Image

```bash
docker image rm jekyll/jekyll
```

In more complex Docker environments do the following.

```bash
docker image ls jekyll/jekyll
```
That will list all of the docker images on the local machine. Grab the `jekyll/jekyll` image Id and use it in the following command.

```bash
docker image rm imageId
```