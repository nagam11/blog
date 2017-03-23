
## Creating an interactive InfoWindow in Google Maps (Swift)
This tutorial is based on Kevin Hu's [idea](http://kevinxh.github.io/swift/custom-and-interactive-googlemaps-ios-sdk-infowindow.html) on creating a custom InfoWindow for GoogleMaps.

#### Setting up a simple app with GoogleMaps
Start off by creating a Single View Application in XCode.

![_1]({{ site.baseurl }}/images/1.png)

Name the application GM-CustomInfoWindow-Button. Don't forget to put the Organization identifier too.
![2]({{ site.baseurl }}/images/2.png)

XCode will generate a new project for you. To work with GoogleMaps you firstly need to import its library. This can easily be done using CocoaPods. Check out their [site](https://cocoapods.org/) to find out more about the installation. Using Atom or Notepad create a new file and write the following.
![2]({{ site.baseurl }}/images/5.png)
