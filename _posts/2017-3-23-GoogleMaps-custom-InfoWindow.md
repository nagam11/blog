
## Creating an interactive InfoWindow in Google Maps (Swift)
This tutorial is based on Kevin Hu's [idea](http://kevinxh.github.io/swift/custom-and-interactive-googlemaps-ios-sdk-infowindow.html) on creating a custom InfoWindow for GoogleMaps.
![_config.yml]({{ site.baseurl }}/images/config.png)

#### Setting up a simple app with GoogleMaps
Start off by creating a Single View Application in XCode.
![1]({{ site.baseurl }}/images/1.png)


Name the application GM-CustomInfoWindow-Button. Don't forget to put the Organization identifier too.
![2](https://github.com/nagam11/nagam11.github.io/blob/master/images/2.png)


XCode will generate a new project for you. To work with GoogleMaps you firstly need to import its library. This can easily be done using CocoaPods. Check out their [site](https://cocoapods.org/) to find out more about the installation. Using Atom or Notepad create a new file and write the following.
![5](https://github.com/nagam11/nagam11.github.io/blob/master/images/5.png)


Save the file in the main project directory and name it Podfile. Then in Terminal, navigate to your main project directory and run `pod install`. CococaPods will install all needed dependencies for you and create a workspace file in your main project directory. After install open your App through the workspace file.

To access GoogleMaps you need an API Key. If you are not familiar with this then you should have a look at [this page](https://developers.google.com/maps/documentation/ios-sdk/get-api-key). To use GoogleMaps you firstly need to register your application in the API Console in Google and then copy the API Key into the App. So, fistly navigate to the App's general settings by clicking on the App name on the Navigation area. In general, you will find you Bundle Identifier.
![4](https://github.com/nagam11/nagam11.github.io/blob/master/images/4.png)


Copy it and paste it into the list of bundle identifiers for you API key in the API Console.
![3](https://github.com/nagam11/nagam11.github.io/blob/master/images/3.png)
By doing this, you have registered your app for this specific API key. Now, you need to tell you App which API Key it has to use. Go to the `AppDelegate` class and `import GoogleMaps`. In the method `func application(_ application: UIApplication, didFinishLaunchingWithOptions:....)` replace the "YOUR-API-KEY" string with the API Key from the Console. 


Next step is creating a mapView. Firstly, start by changing your `ViewController ` file. Start by importing  `import GoogleMaps ` and implementing the  `GMSMapDelegate `.
```
import UIKit
import GoogleMaps
class ViewController: UIViewController, GMSMapViewDelegate {
}
```

Now you have to connect you ViewController to the view in the Storyboard. Using the Assistant Editor, connect the view of the ViewController in the Storyboard with the ViewController file by creating an `IBOutlet` named mapView and change its type to GMSMapView.
    @IBOutlet weak var mapView: GMSMapView!

In your Storyboard, click the view which you linked and change its type in the Identity Inspector to GMSMapView.
![8](https://github.com/nagam11/nagam11.github.io/blob/master/images/8.png)
![7](https://github.com/nagam11/nagam11.github.io/blob/master/images/7.png)

Now that we have linked the view, we have to load the Google Maps view. To do so, if we have to set its delegate to `self`.
In the end you ViewController class should look like this.
![10](https://github.com/nagam11/nagam11.github.io/blob/master/images/10.png)


Now try running your application. You should see something like this. If not then make sure you have not missed any step before and read the error message in the console. 
![11](https://github.com/nagam11/nagam11.github.io/blob/master/images/11.png)

---


