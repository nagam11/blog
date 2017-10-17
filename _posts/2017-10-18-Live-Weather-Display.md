## Creating a live weather Dashboard with ARKit

So recently I stumbled upon [this](http://www.augment.com/blog/4-ways-augmented-reality-will-change-everyday-life/) image, which looks indeed pretty fancy. Since I had been experimenting with ARKit 
for the last 2 months, I thought about recreating the weather display on the right-hand side. Maybe the To-do list on 
the right is going to be next ;)

The final result looks like this.
[![FlatWeatherARKit]({{ site.baseurl }}/images/FlatWeather.png)](https://www.youtube.com/watch?v=iuSspC7fXGY "Watch the video")

### Challenges 
* Creating a nice UI (similar to the one in the image) using ARKit image and text nodes.
* Making the whole display load exactly when all the data have been loaded (the background is rendered before on purpose though)

### Thought on improvements
I would like to integrate this in a full 'personal dashboard' as you can see in the picture I was inspired from. Also, one to-do
is adding the display upon tap to the location the user wants. (this can be done in a few lines of code but I guess am too lazy :D)
