[![Board Status](https://dev.azure.com/knoxbits/d0100851-2dc5-46c5-8610-8f42f950967e/00d7143c-c583-40ef-8f49-0ea7203dc194/_apis/work/boardbadge/bb29497a-6342-4b3b-adf7-5867f33fa495)](https://dev.azure.com/knoxbits/d0100851-2dc5-46c5-8610-8f42f950967e/_boards/board/t/00d7143c-c583-40ef-8f49-0ea7203dc194/Microsoft.RequirementCategory/)

# BryanKnox's GitHub Pages


Uses the official Jekyll Docker Image ([jekyll/jekyll](https://hub.docker.com/r/jekyll/jekyll)) and Docker Compose.

## Testing

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
docker image ls jekyll/jekyll
```
That will list all of the docker images on the local machine. Grab the `jekyll/jekyll` image Id and use it in the following command.

```
docker image rm imageId
```


## Files Excluded from Jekyll

This `README.md` file and the `docker-compose.yml` are excluded from processing by Jekyll, and being copied to the destination.

From `_config.yml`:
```yaml
exclude:
   - docker-compose.yml
   - README.md
```

