FROM ubuntu:18.04

USER root

# Default arguments (links to download models from)
# there are 3 so users are given a choice which to use at runtime

ARG model_link_1=http://download.tensorflow.org/models/object_detection/ssdlite_mobilenet_v2_coco_2018_05_09.tar.gz

ARG model_link_2=http://download.tensorflow.org/models/object_detection/ssd_mobilenet_v2_quantized_300x300_coco_2019_01_03.tar.gz

ARG model_link_3=http://download.tensorflow.org/models/object_detection/ssd_mobilenet_v1_0.75_depth_quantized_300x300_coco14_sync_2018_07_18.tar.gz

RUN apt-get update && apt-get -y --no-install-recommends install \
    sudo \
    vim \
    wget \
    build-essential \
    pkg-config \
    python3.6 \
    python3-pip \
    python3.6-dev \
    python3.6-venv \
    python-dev \
    python3-dev \
    python3-setuptools


RUN apt-get -y --no-install-recommends install \
    git \
    cmake \
    autoconf \
    automake \
    libtool \
    gstreamer-1.0 \
    gstreamer1.0-dev \
    libgstreamer1.0-0 \
    gstreamer1.0-plugins-base \
    gstreamer1.0-plugins-good \
    gstreamer1.0-plugins-bad \
    gstreamer1.0-plugins-ugly \
    gstreamer1.0-libav \
    gstreamer1.0-doc \
    gstreamer1.0-tools \
    gstreamer1.0-x \
    gstreamer1.0-alsa \
    gstreamer1.0-gl \
    gstreamer1.0-gtk3 \
    gstreamer1.0-qt5 \
    gstreamer1.0-pulseaudio \
    python-gst-1.0 \
    libgirepository1.0-dev \
    libgstreamer-plugins-base1.0-dev \
    libcairo2-dev \
    gir1.2-gstreamer-1.0 \
    python3-gi \
    python-gi-dev \
    curl

RUN cd /home && \
    git clone https://github.com/ajbensmancfa/gst-plugins-tf.git

RUN cd /home/gst-plugins-tf && \
    python3 -m venv venv && \
    . venv/bin/activate && \
    pip install --upgrade wheel pip==19.3.0 setuptools && \
    pip install --upgrade --requirement requirements.txt && \
    pip install tensorflow-cpu==1.15

RUN cd /home/gst-plugins-tf && \
    export GST_PLUGIN_PATH=$GST_PLUGIN_PATH:$PWD/venv/lib/gstreamer-1.0/:$PWD/gst/

WORKDIR /home/gst-plugins-tf

# copy videos from local machine (can be used for testing purposes
COPY 160820_052_NYC_TaxisInRain2_1080p.mp4 ./data/videos/trailer.mp4
COPY 160820_052_NYC_TaxisInRain2_1080p.mp4 ./data/videos/car_video.mp4

# download the chosen models
RUN curl $model_link_1 --output ./data/models/model_1.tar.gz
RUN curl $model_link_3 --output ./data/models/model_2.tar.gz
RUN curl $model_link_2 --output ./data/models/model_3.tar.gz

# make empty (for now) directory that the chosen model will be stored in
RUN mkdir -p ./data/models/mymodel

# store the downloaded models in these directories
RUN mkdir -p ./data/models/mymodel_1
RUN mkdir -p ./data/models/mymodel_2
RUN mkdir -p ./data/models/mymodel_3

WORKDIR /home/gst-plugins-tf/data/models

# unpack the models
RUN tar -xzvf model_1.tar.gz -C mymodel_1
RUN tar -xzvf model_2.tar.gz -C mymodel_2
RUN tar -xzvf model_3.tar.gz -C mymodel_3

# the commands below take the files and directories that are part of the model 
# 	and move them up one level in the directory structure.
#	Before: files in /home/gst-plugins-tf/data/models/mymodel_1/long_model_name/
#	After: files in /home/gst-plugins-tf/data/models/mymodel_1/
# Does this for all 3 models

WORKDIR /home/gst-plugins-tf/data/models/mymodel_1
RUN foldername=$(ls) && \
	echo $foldername >> model_name.txt && \
	cd $foldername && \
	for FILE in *; do cp -r $FILE ../$FILE; done

WORKDIR /home/gst-plugins-tf/data/models/mymodel_2
RUN foldername=$(ls) && \
	echo $foldername >> model_name.txt && \
	cd $foldername && \
	for FILE in *; do cp -r $FILE ../$FILE; done

WORKDIR /home/gst-plugins-tf/data/models/mymodel_3
RUN foldername=$(ls) && \
	echo $foldername >> model_name.txt && \
	cd $foldername && \
	for FILE in *; do cp -r $FILE ../$FILE; done

# store names of models in text files
WORKDIR /home/gst-plugins-tf/data/models
RUN model_name_1=${model_link_1##*/} ; echo ${model_name_1%.tar.gz} >> mymodel_1/model_name.txt
RUN model_name_2=${model_link_2##*/} ; echo ${model_name_2%.tar.gz} >> mymodel_2/model_name.txt
RUN model_name_3=${model_link_3##*/} ; echo ${model_name_3%.tar.gz} >> mymodel_3/model_name.txt

WORKDIR /home/gst-plugins-tf
