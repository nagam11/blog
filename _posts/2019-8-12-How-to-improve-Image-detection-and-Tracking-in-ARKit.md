## How to improve Image Detection and Tracking in ARKit

![0]({{ site.baseurl}}/images/arkit.jpg)

Recently, I had to work on a project that involved image detection and tracking using ARKit. After a few tries, I still had a lot of drift and overall very bad tracking. After looking around online and reading up, I managed to improve the tracking by adjusting a couple of important variables. If you’re having problems with drifting virtual objects, you can go through these points and make sure you fulfill all these requirements. Then, you’re good to go!

#### **Big markers vs Small markers**

There’s a limit to how small the marker can be. The limit is somewhere near 2,5 centimeter in width or height. However, after several tries, I came to the conclusion that big markers perform way better than small ones, both for World or Image Tracking configuration. This may seem to be trivial, however this may not be the case in some scenarios. Vuforia tracking for example does not have this limitation. I have been able to track very small markers with great accuracy. Sadly, this is yet not possible in ARKit.

#### **Good markers are not the ones we think are good**

When diving into ARKit markers, I assumed the word “marker” could be used universally across frameworks. However, this was not the case. I used a couple of online tools to generate QR Codes and ‘good’ markers for Augmented Reality and all of these markers either performed very bad or were already ‘discarded’ by XCode as inappropriate images. Some complaints that were shown were the following:

1. The histogram is too narrow and not well distributed. Wider flatter histograms are better.
2. Image resolution is low. Minimum is 480. (very detailed QRCode image)

Overall, simple/normal pictures with many colors and details, thus many features, seem to work great. There’s many articles online that describe good ARKit markers. However, it seemed that complex and too detailed pictures actually performed worse. In my case, I just used a postcard!

#### **Size, Size and … Size**

When I first used markers, I just put the approximate size. Oh, was I wrong! The wrong size greatly affected the tracking and detection, even the smallest change. Make sure to measure your image accurately and put the correct size in XCode. Also pay attention at the units in XCode !

#### **Surroundings**

Even though we don’t know the inner workings of Apple’s framework, it is advisable to pay attention to the surroundings (Where is the marker placed).

In my case, the marker was placed on a uniform-colored table. When adding texture to the background, something like a pavement or rocks etc. , the tracking seemed to improve a little. However, in some cases this might not help. It depends on the use case. But it may be worth a try!

#### **Size.. AGAIN**

In my current setting, I was working with a 2x2 centimeter marker and 3x3 centimeter virtual objects that had to be placed very accurately. Firstly, the marker was too small. Secondly, regardless of all the possible combinations and different markers and backgrounds I used, the detection with World Tracking Configuration was never perfect. This is on one hand, due to the phone’s camera and in-built sensors and on the other hand also due to the small size of the objects I was trying to place. When placing bigger objects, a drift of 0,5 cm is not perceived as much as it was perceived when using a 3x3 cm object. The only way to improve this, was to use Image Tracking and track the marker on every frame. However, depending on the use case it may not be possible. So, this is kind of a limitation in terms of tracking. In my case, I had the opportunity to scale up the whole model.

#### **World Origin**

Also, one things that helps is setting the world origin to the position of the marker (and thus replacing the one based on where the app was started).

This can be done using:
```
sceneView.session.setWorldOrigin(relativeTransform: anchor.transform)
```

where anchor is the ARImageAnchor of the reference images found by ARKit. Put this in the renderer function and that’s it. Now your world origin is set to the marker. Since the marker is not tracking, this point will not change even if you remove the marker. This is helpful if you want to remove the marker in order to have a better view.