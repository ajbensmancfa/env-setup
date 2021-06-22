# Install xquartz

```brew install --cask xquartz```

# Configure xquartz

From the XQuartz preferences, in the security tab, make sure Allow connections from network clients is enabled. Restart XQuartz.

In a terminal on the host, run ```xhost +localhost```

Run ```defaults write org.xquartz.X11 enable_iglx -bool true```

Now do ```export DISPLAY=:0.0```

Restart xquartz and log out and back in again.

# Building the dockerfile

When doing so, provide a link to download the desired model from, and supply it for the arguement ```model_link```. You can find a list of models at https://github.com/tensorflow/models/blob/master/research/object_detection/g3doc/tf1_detection_zoo.md#coco-trained-models\

```docker build --build-arg model_link=http://download.tensorflow.org/models/object_detection/ssdlite_mobilenet_v2_coco_2018_05_09.tar.gz -t gstreamer-python:ubuntu-18 .```           

# Create the image

```
sudo docker run --name gstreamer-python-ubuntu-18-38 -it \
-e DISPLAY=host.docker.internal:0 \
-v /tmp/.X11-unix/:/tmp/.X11-unix \
gstreamer-python:ubuntu-18
```

# Run the model

Run these commands inside the container
```
source venv/bin/activate
export GST_PLUGIN_PATH=$GST_PLUGIN_PATH:$PWD/venv/lib/gstreamer-1.0/:$PWD/gst/
```

Afterwards, the model should work. See the logs in the terminal using this command:
```GST_DEBUG=python:5 \
gst-launch-1.0 filesrc location=data/videos/trailer.mp4 ! decodebin ! videoconvert !  gst_tf_detection config=data/tf_object_api_cfg.yml ! videoconvert ! fakesink
```

See the results in the video using this one:
```./run_example.sh```

