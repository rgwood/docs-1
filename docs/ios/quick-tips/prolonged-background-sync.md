---
title: 'Prolonged Background Sync'
sidebar_position: 5
---

iOS apps will stop Ditto's sync protocol when the application is put into the
background. If you need Ditto to continue synchronizing while the application is
in the background, you need to tell iOS to keep the app active. To do this, we
recommend playing silent music in the background.

Firstly, make sure you have enabled audio and Bluetooth background capabilities:
![](./background_capabilities.png)

```swift
final public class BackgroundSync {
    public static let shared = BackgroundSync()
    private let player: AVAudioPlayer
    private let base64AudioString = "UklGRiYAAABXQVZFZm10IBAAAAABAAEARKwAAIhYAQACABAAZGF0YQIAAAD8/w=="

    public var isOn = false

    private init() {
        let audioData = Data(base64Encoded: base64AudioString)!
        try! AVAudioSession.sharedInstance().setCategory(AVAudioSession.Category.playback,
                                                         mode: .default,
                                                         options: .mixWithOthers)
        try! AVAudioSession.sharedInstance().setActive(true)
        player = try! AVAudioPlayer(data: audioData, fileTypeHint: "wav")
        player.numberOfLoops = -1
        player.volume = 0.01
        player.prepareToPlay()
    }

    public func start() {
        if (isOn) { return }
        isOn = true

        NotificationCenter.default.addObserver(self, selector: #selector(interuptedAudio), name: AVAudioSession.interruptionNotification, object: AVAudioSession.sharedInstance())
        self.player.play()
        print("BackgroundSync started")
    }

    public func stop() {
        NotificationCenter.default.removeObserver(self, name: AVAudioSession.interruptionNotification, object: nil)
        if player.isPlaying {
            player.stop()
            isOn = false
            print("BackgroundSync stopped")
        }
    }

    @objc private func interuptedAudio(_ notification: Notification) {
        if notification.name == AVAudioSession.interruptionNotification && notification.userInfo != nil {
            let info = notification.userInfo!
            var intValue = 0
            (info[AVAudioSessionInterruptionTypeKey]! as AnyObject).getValue(&intValue)
            if intValue == 1 { self.player.play() }
        }
    }
}
```

Then, call ```BackgroundSync.shared.start()``` when your application goes into the background. For example,

```swift
@UIApplicationMain
final class AppDelegate: UIResponder, UIApplicationDelegate {
    var window: UIWindow?

    func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
        // ... your app
    }

    func applicationWillResignActive(_ application: UIApplication) {
        BackgroundSync.shared.start()
    }

    func applicationWillEnterForeground(_ application: UIApplication) {
        BackgroundSync.shared.stop()
    }
}
```

:::caution
If you're using scenes, UIKit will not call this method. Use `sceneWillEnterForeground()` instead to prepare your app to enter the foreground. ([source](https://developer.apple.com/documentation/uikit/uiapplicationdelegate/1623076-applicationwillenterforeground))
:::
