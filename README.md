# Intel-Real-Sense
# Implementation of the Soil Roughness Index using the Intel Real Sense sensor.
 The code used for the given project in determining the soil roughness parameter is as follows :
 First import the library
import pyrealsense2 as rs
import numpy as np
import cv2
import math
pipeline = rs.pipeline()
config = rs.config()
config.enable_stream(rs.stream.depth, 1280, 720, rs.format.z16, 30)
config.enable_stream(rs.stream.color, 1280, 720, rs.format.bgr8, 30)
pipeline.start(config)
try:
    # Create a context object. This object owns the handles to all connected realsense devices
    sum=0.0
    sum1=0.0
    sum2=0.0
    sum3=0.0
    area=480*640
    i=0
    while (i<1):
        # This call waits until a new coherent set of frames is available on a device
        # Calls to get_frame_data(...) and get_frame_timestamp(...) on a device will return stable values until wait_for_frames(...) is called
        frames = pipeline.wait_for_frames()
        i=i+1
        depth = frames.get_depth_frame()
        color_frame = frames.get_color_frame()
        if not depth: continue
        # Print a simple text-based representation of the image, by breaking it into 10x20 pixel regions and approximating the coverage of pixels within one meter
       # coverage = [0] * 64
        depth_image = np.asanyarray(depth.get_data())
        color_image = np.asanyarray(color_frame.get_data())
        depth_colormap = cv2.applyColorMap(cv2.convertScaleAbs(depth_image, alpha=0.03), cv2.COLORMAP_JET)
        # Stack both images horizontally
        images = np.hstack((color_image, depth_colormap))
        cv2.namedWindow('RealSense', cv2.WINDOW_AUTOSIZE)
        cv2.imshow('RealSense', images)
        cv2.waitKey(8000)
        # Show images
        for y in range(480):
            for x in range(640):
                dist = depth.get_distance(x, y)
                sum +=dist
                sum1+=(dist*dist)
                sum2 +=(dist*dist*dist)
                sum3 += (dist*dist*dist*dist)

        Sa=sum/area
        Sq=math.sqrt(sum1/area)
        Ssk=(sum2/area)/(Sq*Sq*Sq)
        Sku=(sum3/area)/(Sq*Sq*Sq*Sq)

        print("Sa = ",Sa)
        print("Sq = ",Sq)
        print("Ssk = ",Ssk)
        print("Sku = " ,Sku)

   #finally:

    #pipeline.stop()
    exit(0)
# except rs.error as e:
#    # Method calls agaisnt librealsense objects may throw exceptions of type pylibrs.error
#    print("pylibrs.error was thrown when calling %s(%s):\n", % (e.get_failed_function(), e.get_failed_args()))
#    print("    %s\n", e.what())
#    exit(1)
except Exception as e:
   print(e)
   pass
