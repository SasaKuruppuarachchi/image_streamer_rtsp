# image_streamer_rtsp

1. Setup the server 
	- Proxy server with public IP (server_ip) (If the reciever is in the same network skip this)
	- Download and extract a standalone binary from the https://github.com/bluenviron/mediamtx/releases
	- Start the server: ./mediamtx

2. Make sure you have opencv with gstreamer built. otherwise
```
sudo apt install -y libgstreamer1.0-dev libgstreamer-plugins-base1.0-dev gstreamer1.0-plugins-ugly gstreamer1.0-rtsp python3-dev python3-numpy
git clone --depth=1 -b 4.5.4 https://github.com/opencv/opencv
cd opencv
mkdir build && cd build
cmake -D CMAKE_INSTALL_PREFIX=/usr -D WITH_GSTREAMER=ON ..
make -j$(nproc)
sudo make install
```
4. Ping and verify connection (server_ip):8554

5. run on the client(source)
```
import cv2

out = cv2.VideoWriter('appsrc ! videoconvert' + \
    ' ! x264enc speed-preset=ultrafast bitrate=600 ' + \
    ' ! rtspclientsink location=rtsp://(server_ip):8554/mystream protocols=tcp',
    cv2.CAP_GSTREAMER, 0, 30, (300, 300), True)
    
# frame = raw_image
while true:
	out.write(frame)
```
5.Open the stream on reciever. For instance, you can open the stream 

with VLC:

```vlc --network-caching=50 rtsp://(server_ip):8554/mystream```

or GStreamer:

```gst-play-1.0 rtsp://(server_ip):8554/mystream```

or FFmpeg:

```ffmpeg -i rtsp://(server_ip):8554/mystream -c copy output.mp4```
