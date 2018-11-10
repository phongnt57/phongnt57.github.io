---
layout: post
title: How to update location in background (iOS) even user kill by swipe up
subtitle: My first tech article
bigimg: /img/path.jpg
tags: [ios, dev]
---

Recently, I receive a task from my boss that develop a feature allow update
 and receive location information in a ios app using Unity(with small ios plugin) even when user put the app in background
 and  manual kill it by double tap in home and swipe up.
 I have no experience in ios before. It seems hard to me so i spend a day to research with Google 
 and stackoverfow and found that background task only works in some case:
 In this background modes tutorial, you’ll investigate four ways of doing background processing:
 * Play audio: The app can continue playing and/or recording audio in the background.
 
 * Receive location updates: The app can continue to get callbacks as the device’s location changes.
 
 * Perform finite-length tasks: The generic “whatever” case, where the app can run arbitrary code for a limited amount of time.
 * Background Fetch: Get updates to the latest content scheduled by iOS.
 Ok, so how to implement location update in ios. I start with some tutorial on internet:
 * Step 1: Define `Privacy — Location Always and When In Use Usage Description` and 
 `Privacy — Location When In Use Usage Description` string in Info.plist with whatever string
 you want.
 * Step 2:  Enable location update in your project.
 ![image tooltip here](https://koenig-media.raywenderlich.com/uploads/2016/09/BM-EnableLocationInBG-650x354.png)

 [logo]:  "enable it"
 * Step 3: 
   We use CLLocationManagerDelegate in ViewController:
  `@interface ViewController () <CLLocationManagerDelegate>`
  * Step 4: In
             `(void)viewDidLoad` of ViewController.m we starting code:
                   
              `  locationManager = [[CLLocationManager alloc] init];
                 locationManager.desiredAccuracy = kCLLocationAccuracyNearestTenMeters;
                 locationManager.distanceFilter = 10; // meters
                 locationManager.pausesLocationUpdatesAutomatically = NO; // YES is default
                 locationManager.allowsBackgroundLocationUpdates = true;
                 [locationManager requestAlwaysAuthorization];
                 locationManager.activityType = CLActivityTypeAutomotiveNavigation;
                 locationManager.delegate = self;
                 [locationManager startUpdatingLocation];`
                 
              
   Define function: didUpdateLocations              
  ```- (void)locationManager:(CLLocationManager *)manager didUpdateLocations:(NSArray *)locations
  {
      CLLocation *location = [locations lastObject];
      
      // I learned this method of getting a time interval from xs2bush on stackoverflow and wanted to give that person
      // credit for this, thanks. http://stackoverflow.com/a/6466152/125615
     
  }  
  ```     
  After implement those bunch of code, it works well, but when i kill app, location seems 
  stop update. Yes, we has not done with this. We have to process with UnityAppController (in Unity it is parent of UIApplicationDelegate in native ios)
  Let go!
  * Step 5:
  In AppDelegate we define:
  ```
  @property (strong, nonatomic) LocationManager *locationManager;         

  - (BOOL)application:(UIApplication*)application willFinishLaunchingWithOptions:(NSDictionary*)launchOptions {
     [self.locationManager startMonitoringLocation];
     return YES;
  }
   - (void)applicationDidBecomeActive:(UIApplication *)application {  
          [self.locationManager startMonitoringLocation];
          [super applicationDidBecomeActive: application];
  
      }
   - (void)applicationWillTerminate:(UIApplication *)application {
           [self.locationManager startMonitoringLocation];
           [super applicationWillTerminate: application];
   
       }    
   ```
   LocationManager can be found here :https://gist.github.com/phongnt57/d290fd11fd4113b7d70ffb28e9d0294f
      
  
 * Conclusion: I kill app but   with some test, found that location still not update.
Don't be worry. When you move and change the location, the condition is trigger, and the app will wake up with location will be update:
              `locationManager.distanceFilter = 10; // meters`
              
   I am beginner with ios developement and beginner with writing tutorial (so if you have trouble 
   with reading this article, comment and send to ngothaiphong94@gmail.com)           
      
    

   

    

    
    

          

  
  

 
   