---
title: Accessing Your App Icon in Code
date: 2025-11-23 00:20:00 +0200
description: "How to access your iOS app icon in code to use it as an image inside your app"
categories: [Practical Solutions]
tags: [ios, swiftui, uikit]     # TAG names should always be lowercase
---
If you ever find yourself wanting to use your app icon inside your app - for example, in an About screen or a top bar branding similar to Facebook -, but you don't want to import it into your project as a regular image, because, after all, it's already there, isn't it? - this is how you can do it:
```swift
import SwiftUI  // or UIKit, both work for UIImage

extension Bundle {
    var appIcon: UIImage? {
        guard let icons = infoDictionary?["CFBundleIcons"] as? [String: Any],
              let primaryIcon = icons["CFBundlePrimaryIcon"] as? [String: Any],
              let iconFiles = primaryIcon["CFBundleIconFiles"] as? [String],
              let lastIcon = iconFiles.last else {
            return nil
        }

        return UIImage(named: lastIcon)
    }
}
```

Example usage in SwiftUI:
```swift
if let icon = Bundle.main.appIcon {
    Image(uiImage: icon)
        .resizable()
        .scaledToFit()
        .frame(width: 30, height: 30)
        .clipShape(RoundedRectangle(cornerRadius: 7, style: .continuous))
        .shadow(color: .black.opacity(0.15), radius: 2, x: 0, y: 1)
}
```

Sorry, no pirate theme fit into this one.
