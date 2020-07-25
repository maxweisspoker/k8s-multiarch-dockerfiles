```
Built with buildx Docker plugin using QEMU emulation to build natively. (I have
found that to be more reliable.)

The below "all in one" Dockerfile sometimes fails, so I have a "staged_builds"
folder where you can build the components one by one, and then the last stage
just sticks them all in the same container. I have found the staged build to
be more reliable and recommend using that, although occasionally using the
single build Dockerfile does work.

Build command for the single-shot Dockerfile:

$ docker buildx build -f ./Dockerfile -t ${DOCKER_TAG_NAME} --platform=linux/arm/v6 --no-cache --build-arg BUILD_DATE=$(date -u +'%Y-%m-%dT%H:%M:%SZ') --load .

Layered caching can be used by removing the --no-cache flag and using:
--cache-to="dest=/path/to/cachefolder,type=local" --cache-from src="/path/to/cachefolder,type=local"
```

