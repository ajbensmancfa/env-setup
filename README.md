# Install xquartz

```brew install -cask xquartz```

# Configure xquartz

From the XQuartz preferences, in the security tab, make sure Allow connections from network clients is enabled. Restart XQuartz.

In a terminal on the host, run ```xhost +localhost```


# Building the dockerfile

```docker build -t gstreamer-python:ubuntu-18 . ```           

# Create the image

```
sudo docker run --name gstreamer-python-ubuntu-18-38 -it \
-e DISPLAY=host.docker.internal:0 \
-v /tmp/.X11-unix/:/tmp/.X11-unix \
gstreamer-python:ubuntu-18
```

