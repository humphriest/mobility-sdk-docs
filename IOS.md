## IOS Integration

MobilitySDK has been implemented as an iOS dynamic Framework that is installed using CocoaPods.

1.  Add the following line to your `Podfile` and run `pod install` or `pod update`:

```
pod 'MobilitySDK', :git => 'https://github.com/cartrawler/mobility-sdk-pods'
```

Please note that you may need to install `git-lfs`. On Mac, install this with `brew install git-lfs`. Then, initialise your repo for `lfs` by using the command `git lfs install`.

2. Add the following property to your `Info.plist` file (This is necessary for the Mobility App's location based features):

```
    <key>NSLocationWhenInUseUsageDescription</key>
    <string>This app needs access to your location to provide ride hailing services.</string>
```

3. `MobilitySDKManager` should be initialised in your application delegate's init method or similar. This is required to send messages to the Mobility App such as profile updates.

```objectivec
#import <MobilitySDK/MobilitySDK.h>

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    [[MobilitySDKManager sharedManager] initWithOptions:nil partnerId:@"<partner-id>"];
    ...
}
```

4. To display the Mobility App view, send the `openMobility` message to the instance of `MobilitySdkManager` in a `ViewController`.

```objectivec
#import <MobilitySDK/MobilitySDK.h>

// when you want to open mobility ViewController (where self is a ViewController):
[[MobilitySDKManager sharedManager] openMobility:self type:@"cross-sell" source:@"deeplink source" campaign:@"deeplink campaign" medium:@"deeplink medium"];
```

These paramaters will be used for tracking purposes, any string will do for testing.

5. To enable the opening and closing of Mobility App, the delegate protocol `MobilityDelegate` should be applied to the class which presents `MobilityViewController`.

Mandatory delegate methods:

- `-(void)shouldCloseMobilityApp` - Implement this method to enable the `MobilityViewController` to close itself.

* `-(void)shouldOpenMobilityApp` - Implement this method to enable the `MobilityViewController` to open itself (eg on click of a deep linking push notification). This is used extremely sparingly by the MobilitySdk, and only on user interaction.

```objectivec
@interface ViewController: UIViewController<MobilityDelegate>

@end

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    [MobilitySDKManager sharedManager].delegate = self;
}

- (void)shouldCloseMobilityApp {
    [self dismissViewControllerAnimated:NO completion:nil];
};

- (void)shouldOpenMobilityApp {
    [[MobilitySDKManager sharedManager] openMobility:self type:@"cross-sell" source:@"deeplink source" campaign:@"deeplink campaign" medium:@"deeplink medium"];
};
@end
```
