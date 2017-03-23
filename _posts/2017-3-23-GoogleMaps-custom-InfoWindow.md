
## Creating an interactive InfoWindow in Google Maps (Swift)
This tutorial is based on Kevin Hu's [idea](http://kevinxh.github.io/swift/custom-and-interactive-googlemaps-ios-sdk-infowindow.html) on creating a custom InfoWindow for GoogleMaps.

#### Setting up a simple app with GoogleMaps
Start off by creating a Single View Application in XCode.
![1]({{ site.baseurl }}/images/1.png)


Name the application **GM-CustomInfoWindow-Button**. Don't forget to put the Organization identifier too.
![2]({{ site.baseurl }}/images/2.png)


XCode will generate a new project for you. To work with GoogleMaps you firstly need to import its library. This can easily be done using **CocoaPods**. Check out their [site](https://cocoapods.org/) to find out more about the installation. Using Atom or Notepad create a new file and write the following.
![5]({{ site.baseurl }}/images/5.png)


Save the file in the main project directory and name it **Podfile**. Then in Terminal, navigate to your main project directory and run `pod install`. CococaPods will install all needed dependencies for you and create a workspace file in your main project directory. After install open your App through the workspace file.




In order for you to work with GoogleMaps you need an **API Key**. If you are not familiar then you should definitely have a look at [this page](https://developers.google.com/maps/documentation/ios-sdk/get-api-key). To use GoogleMaps you firstly need to register your application in the **API Console** in Google and then copy the API Key into the App. So, firstly navigate to the App's general settings by clicking on the App name on the Navigation area. In the tab "General", you will find you **Bundle Identifier.**
![4]({{ site.baseurl }}/images/4.png)


Copy it and paste it into the list of bundle identifiers for you API key in the API Console.
![3]({{ site.baseurl }}/images/3.png)

By doing this, you have registered your app for this specific API key. As next, you need to tell you app which API Key it has to use. So go to the `AppDelegate` class and `import GoogleMaps`. In the method `func application(_ application: UIApplication, didFinishLaunchingWithOptions:....)` replace the "YOUR-API-KEY" string with the API Key from the Console(shown below). 
![6]({{ site.baseurl }}/images/6.png)


Next step is creating a **mapView**. Firstly, start by changing your `ViewController ` file. Start by importing  `import GoogleMaps ` and implementing the  `GMSMapDelegate `.
```
import UIKit
import GoogleMaps
class ViewController: UIViewController, GMSMapViewDelegate {
}
```

Now you have to connect you ViewController to the view in the Storyboard. Using the Assistant Editor, connect the view of the ViewController in the Storyboard with the ViewController file by creating an `IBOutlet` named mapView and change its type to `GMSMapView`.
```
@IBOutlet weak var mapView: GMSMapView!
```

In your Storyboard, click the view which you linked and change its type in the Identity Inspector to GMSMapView.
![8]({{ site.baseurl }}/images/8.png)
![7]({{ site.baseurl }}/images/7.png)


Now that we have linked the view, we have to set the delegate of the mapView to `self`. In the end, your ViewController class should look like this.
![10]({{ site.baseurl }}/images/10.png)


Now go ahead and run your application. You should see something like this. If not, then make sure **you have not missed any of the steps** before and read the **error message** in the console. 
![11]({{ site.baseurl }}/images/11.png)

---
#### Implementing a simple InfoWindow
Now we want to have a marker and show an **InfoWindow** when the user clicks on it. In `viewDidLoad` we position the camera in London by using the city's latitude and longitude and create a `GMSMarker` and add it to our map.
```
override func viewDidLoad() {
 super.viewDidLoad()
 let camera = GMSCameraPosition.camera(withLatitude: 51.5287352, longitude: -0.3817818, zoom: 8)
 mapView.camera = camera
        
 let marker = GMSMarker()
 marker.position = CLLocationCoordinate2D(latitude: 51.5287352, longitude: -0.3817818)
 marker.title = "My marker"
 marker.map = self.mapView
 
 self.mapView.delegate = self
 }
 ```
 
 If you run the app then you should probably see something like this. The app zooms in to London while using the zoom attribute from before(8).
 ![12]({{ site.baseurl }}/images/12.png)
 
 
 Next, we want to show an InfoWindow when the user clicks on the marker. To achieve this, we will use a **xib file**. In XCode create a new View file and name it CustomInfoWindow.
 ![13]({{ site.baseurl }}/images/13.png)
 
 
In the xib file, click on the Attributes inspector and change the view's size to Freeform. Then change the Size of the view using the Size inspector and set width to 300 and height to 200. 
 
 ![14]({{ site.baseurl }}/images/14.png)
 ![14b]({{ site.baseurl }}/images/14b.png)
 
 
 To add some functionality we add a `UILabel` and a `UIButton` by dragging them. Your view should in the end look like this:
 ![15]({{ site.baseurl }}/images/15.png)
 
Now, we need to create a class for our custom InfoWindow. Create a new CocoaTouch file and name it **CustomInfoWindow**. This class should be a subclass of UIView. 
 ![16]({{ site.baseurl }}/images/16.png)
 
 
In order to connect the view to our file we have to set it as a class in the Identity Inspector of the view.
 ![17]({{ site.baseurl }}/images/17.png)
 
Run you app to ensure that everything is set up correctly. However, you still won't be able to see an InfoWindow.

 ---
#### Presenting a simple InfoWindow
Now we want to be able to show our InfoWindow. To do this, we firstly should be able to instantiate the xib file. In you `CustomInfoWindow` file add following code. 
```
override init(frame: CGRect) {
 super.init(frame: frame)   
}
    
required init?(coder aDecoder: NSCoder) {
 super.init(coder: aDecoder)
}

func loadView() -> CustomInfoWindow{
 let customInfoWindow = Bundle.main.loadNibNamed("CustomInfoWindow", owner: self, options: nil)?[0] as! CustomInfoWindow
 return customInfoWindow
}
```

What we are doing here is initializing the view and loading the customized nib using the file name "CustomInfoWindow". After we have done this, we can now proceed in the `ViewController`. We declare two attributes : `tappedMarker` and `currentInfoWindow`. We need to keep track of the `tappedMarker` and have a reference to the `currentInfoWindow` in order to change it or customize it.
```
var tappedMarker : GMSMarker?
var customInfoWindow : CustomInfoWindow?
```
Now we have to instantiate them in `viewDidLoad`.
```
self.tappedMarker = GMSMarker()
self.customInfoWindow = CustomInfoWindow().loadView()
```

As next, we have to implement two methods of the GMSMapDelegate; namely `didTap marker` and `markerInfoWindow marker` in order to catch the tap events on markers and display an info window. Now you can add this to your ViewController file.
```
func mapView(_ mapView: GMSMapView, didTap marker: GMSMarker) -> Bool {
 return false
 }
 
func mapView(_ mapView: GMSMapView, markerInfoWindow marker: GMSMarker) -> UIView? {
 return self.customInfoWindow
}
```

In the end, your ViewController should look like this.
![23]({{ site.baseurl }}/images/23.png)


Go ahead and run your app. If you click on a marker, you should see something like this. However, you will notice that you cannot press the button you have added. So why is that ?
![24]({{ site.baseurl }}/images/24.png)


Now that's the main problem in using the InfoWindow functionality provided by GoogleMaps. The view is rendered as an **Image** and all elements in it **will not fire any events**. 
![25]({{ site.baseurl }}/images/25.png)

---
#### Implementing a custom InfoWindow
We want however, to be able to have a button in the InfoWindow and fire some event. To do this, we have to implement our own version of the InfoWindow functionality, meaning our InfoWindow will not behave the same as the GoogleMaps InfoWindow. It won't automatically dismiss if we click somewhere in the map or it won't be placed automatically on top of the markers. (and other standard behaviours)

So, we start by connecting the two elements(label and button) of the `CustomInfoWindow` (nib File) with the `CustomInfoWindow`(CocoaTouch class) by dragging them. Create one Outlet for the label and one action for the button. We want to change the text of the label on button click so we do something like this:
```
@IBOutlet weak var customWindowLabel: UILabel!
    
@IBAction func press(_ sender: UIButton) {
 self.customWindowLabel.text = "You just pressed the button ! "
}
```
![18]({{ site.baseurl }}/images/18.png)


Before proceeding, make sure your app still runs and that your classes look as follows. I have added another marker to test later the functionality.
![22]({{ site.baseurl }}/images/22.png)
![23]({{ site.baseurl }}/images/23.png)

---
#### Presenting the custom InfoWindow
Now we have to change the `ViewController` file. In order to implement our own behaviour for the `CustomInfoWindow`, we have to set to empty the behaviour of the mapInfoWindow of GoogleMaps. We do that as follows:
```
func mapView(_ mapView: GMSMapView, markerInfoWindow marker: GMSMarker) -> UIView? {
 return UIView()
}
```

Next, we implement the functionality in the method `didTap marker`. Add following code to the method:
![26]({{ site.baseurl }}/images/26.png)


Firstly, we keep track of the marker by assigning it to our variable `tappedMarker`. Then, by using the position of the marker, we focus the camera on the tapped location. Next, we customize our `customInfoWindow` and add it as a subView of the `mapView`. In order to imitate the behaviour of the InfoWindow, we have to place it correctly on the map(above the marker) and dismiss it if we click another marker or on the map. So we implement two other methods.

Firstly, we want to dismiss the window if we tap somewhere on the map. So we implement the method `mapView(_ mapView: GMSMapView, didTapAt coordinate`. 
```
func mapView(_ mapView: GMSMapView, didTapAt coordinate: CLLocationCoordinate2D) {
 customInfoWindow?.removeFromSuperview()
}
```
Then ,we want to keep track of camera change in order to change the position of our InfoWindow. That's why we implement following method:
```
func mapView(_ mapView: GMSMapView, didChange position: GMSCameraPosition) {
 let position = tappedMarker?.position
 customInfoWindow?.center = mapView.projection.point(for: position!)
 customInfoWindow?.center.y -= 140
}
```

In order for the InfoWindow to appear on top of the marker we have to fix the placement. In my case, I had to place to InfoWindow 140px higher. This variable depends however on your app and device.

---
In the end you should be able to run your app and have following functionality.
![26]({{ site.baseurl }}/images/CustomMarker.gif)


As next, you could pass data from the `ViewController` to the `View` and vice Versa by using the **Delegate pattern**. This tutorial just shows the basic functionality.
