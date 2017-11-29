---
layout: post
title:  "One touch cloud photo frame and spotify jukebox"
date:   2017-11-30 11:24:00 +1100
categories: android spotify cloud photo frame
---
We all take a lot of photos nowadays. What can be easier to snap the photo with your smartphone and sync it to the [Google photos](https://photos.google.com/) or [Dropbox](https://www.dropbox.com/). But how often do you view them... Then answer is - not very often. To combat this problem you can use digital photo frame - but you have to buy the device, select the photos you want to view on it and update them from time to time. Many of us also like to listen to the music while doing something around the house. Wouldn't it be nice if you had a device that would combine digital photo frame and music jukebox? Well, you can actually make one yourself without spending too much time or money.

I have put together a guide that will help you make such a device.
To begin I will list hardware and apps that you will need.
Hardware

* Android tablet. (I'm using Samsung Galaxy Tab S 8" but will eventually upgrade to something with a bigger display)
* Speakers (You can get anything starting from active computer speakers to a nice set of bookshelf speakers with an amp)
* Switch to turn everything on or of at once. This is optional as you might have conveniently located wall socket with a switch. In my personal setup I'm using [Eco Switch : Smart Power Switch](http://www.ecoswitch.com.au/). If I was living in US I would have bought something like  [Belkin Conserve Surge](http://www.belkin.com/us/CNS08-T-06-Belkin/p/P-CNS08-T-06/) it has much nicer design but not available in Australia.

Apps:

* [Tasker](https://play.google.com/store/apps/details?id=net.dinglisch.android.taskerm&hl=en). This will help you to achieve one touch on/off.
* [Fotoo - Cloud Photo Frame](https://play.google.com/store/apps/details?id=com.bo.fotoo&hl=en). You can replace this with some other photo frame app. I personally like the simplicity of Fotoo, ability to display photos from various sources i.e. Dropbox, Google Drive, Google Photos, local network (Samba) or the folder on the tablet. It has nice animations and it allows you to pause, play next/previous photo. 
* [Overlays - Floating Automation](https://play.google.com/store/apps/details?id=com.applay.overlay&hl=en). This would enable you to overlay your music player widget in on top of photos.
* Your favourite music player in my case [Spotify](https://play.google.com/store/apps/details?id=com.spotify.music&hl=en). This app doesn't require any explanations. I would add that you can replace it with Apple Music, Google Play Music or any other music app.

Configuration:

* Install all apps from the list above.
* Configure Fotoo. I'm using Google Photos as a source. I also prefer to control start and stop of Fotoo from Tasker. So go to Settings -> Auto Start -> Charging and unselect "Launch when start charging" and "Exit when charger unplugged". ![settings]({{ site.url }}/assets/images/photoframe/fotoo_settings.png){: .image-in-list }

* Configure Spotify Widget overlay. 
    * Launch Overlays app.
    * Click on Get Started button. ![overlays get started]({{ site.url }}/assets/images/photoframe/overlays_get_started.png){: .image-in-list }
    * Click on "+" icon. ![add overlay]({{ site.url }}/assets/images/photoframe/overlays_add.png){: .image-in-list }
    * From the list select "Widget". ![select overlay type]({{ site.url }}/assets/images/photoframe/overlays_widget.png){: .image-in-list }
    * Click on Save.
    * From the list of triggers select 
    * From the list of apps select Fotoo
* Configure Tasker. I will share profile that I have created. To import profile
    * Download profile I've shared here. Or create xml file and copy and paste profile xml. Remember the folder on the table where you have placed profile xml.
    * Open Tasker and tap Profiles
    * Select Import 
    * Select file that you have downloaded/created in the first step
    * Review profile and click the tick icon 

Now you can enjoy your Cloud photo frame / Spotify jukebox. And don't forget that this is still a table and you can use it for other purposes. I will give you few examples the use it for:
* Google assistant. Configure tablet to listen to "Ok Google" and you've got yourself "Smart Speaker"
* Display recipes while you are cooking
* Check the weather
* Kitchen timer
* To-do list
The only thing that limits you is your imagination.
