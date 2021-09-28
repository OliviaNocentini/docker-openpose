# docker-openpose
```
docker pull ghcr.io/olivianocentini/docker-openpose:cuda
```
```
xhost +
docker run -it --rm  \
           --device=/dev/dri:/dev/dri \
           --user user \
           --env="DISPLAY=$DISPLAY"  \
           --net=host \
           --runtime=nvidia \
           ghcr.io/olivianocentini/docker-openpose:cuda
 ```          
           
           


