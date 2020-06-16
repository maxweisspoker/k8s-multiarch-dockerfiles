```
Built with buildx Docker plugin using QEMU emulation to build natively. (I have
found that to be more reliable.)

Build command:

$ docker buildx build -f ./Dockerfile -t ${DOCKER_TAG_NAME} --platform=linux/amd64 --no-cache --build-arg BUILD_DATE=$(date -u +'%Y-%m-%dT%H:%M:%SZ') --load .

Layered caching can be used by removing the --no-cache flag and using:
--cache-to="dest=/path/to/cachefolder,type=local" --cache-from src="/path/to/cachefolder,type=local"
```
