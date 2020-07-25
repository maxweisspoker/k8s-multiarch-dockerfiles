```
Built with buildx Docker plugin using QEMU emulation to build natively. (I have
found that to be more reliable.)

Despite this _more_ reliable success, I have still have issues with
compilation. Therefore, I broke apart the Dockerfile in the parent directory
into many separate image builds to distinctly save the process into images that
I could run and examine. Compilation success varies, and that's a "Heisenbug" I
haven't yet figured out. (Even multi-stage builds in the same Dockerfile
sometimes have issues.) So instead, I simply save progress and repeat until
success. I understand how dumb and hacky this is, but it's a working and
reproduceable process, so it's good enough for me for the time being.

Build command:

$ docker buildx build -f ./Dockerfile01 -t localhost:5000/armstage01 --platform=linux/arm/v6 --no-cache --load .
$ docker buildx build -f ./Dockerfile02 -t localhost:5000/armstage02 --platform=linux/arm/v6 --no-cache --load .
$ docker buildx build -f ./Dockerfile03 -t localhost:5000/armstage03 --platform=linux/arm/v6 --no-cache --load .
$ docker buildx build -f ./Dockerfile04 -t localhost:5000/armstage04 --platform=linux/arm/v6 --no-cache --load .
$ docker buildx build -f ./Dockerfile05 -t localhost:5000/armstage05 --platform=linux/arm/v6 --no-cache --load .
$ docker buildx build -f ./Dockerfile06 -t localhost:5000/armstage06 --platform=linux/arm/v6 --no-cache --load .
$ docker buildx build -f ./Dockerfile07 -t localhost:5000/armstage07 --platform=linux/arm/v6 --no-cache --load .
$ docker buildx build -f ./Dockerfile08 -t localhost:5000/armstage08 --platform=linux/arm/v6 --no-cache --load .
$ docker buildx build -f ./Dockerfile09 -t localhost:5000/armstage09 --platform=linux/arm/v6 --no-cache --load .
$ docker buildx build -f ./Dockerfile10 -t localhost:5000/armstage10 --platform=linux/arm/v6 --no-cache --load .
$ docker buildx build -f ./Dockerfile11 -t localhost:5000/armstage11 --platform=linux/arm/v6 --no-cache --load .
$ docker buildx build -f ./Dockerfile12 -t localhost:5000/armstage12 --platform=linux/arm/v6 --no-cache --load .
$ docker buildx build -f ./Dockerfile13 -t localhost:5000/armstage13 --platform=linux/arm/v6 --no-cache --load .


Then finally, the last build:
$ docker buildx build -f ./Dockerfile_aggregater -t ${DOCKER_TAG_NAME} --platform=linux/arm/v6 --no-cache --build-arg BUILD_DATE=$(date -u +'%Y-%m-%dT%H:%M:%SZ') --load .

(You can now also remove/delete the stage images from your local Docker
instance.)
```