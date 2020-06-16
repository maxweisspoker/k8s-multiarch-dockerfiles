```
Built with buildx Docker plugin using QEMU emulation to build natively. (I have
found that to be more reliable.)

Although the platform says arm v7, the produced static binaries will work on
both arm6 and arm7. For some reason, when creating 100% statically linked
binaries, Go keeps building arm6-compatible binaries. I haven't figured out
why, but since I want both arm6 and arm7, I'm happy to take the static arm6
ones (which also run on arm7 just fine). The sole confirmation I have that they
are arm6 and arm7 compatible is that the bins work on both my Pi Zero and my
Pi 3.

Build command:

$ docker buildx build -f ./Dockerfile -t ${DOCKER_TAG_NAME} --platform=linux/arm/v7 --no-cache --build-arg BUILD_DATE=$(date -u +'%Y-%m-%dT%H:%M:%SZ') --load .

Layered caching can be used by removing the --no-cache flag and using:
--cache-to="dest=/path/to/cachefolder,type=local" --cache-from src="/path/to/cachefolder,type=local"
```