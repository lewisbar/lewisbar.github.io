---
title: Custom Back Button in SwiftUI
date: 2025-11-23 04:50:00 +0200
description: "How to use a custom back button in SwiftUI without losing the back swiping gesture"
categories: [Practical Solutions]
tags: [ios, swiftui]     # TAG names should always be lowercase
---
If you don't like the default iOS back button prior to iOS 26 (blue with the word “Back”), you can use your own. Here is the best method I found after trying various strategies that didn't work quite right.

This post was written with iOS 18 (and earlier) in mind. iOS 26 has changed the design to icon-only, so you might not need to change it. But if you're supporting older iOS versions, read on. As far as I could see, this method does not break anything for iOS 26.

## Step 1: Remove the default back button
Hide the default back button with `.navigationBarBackButtonHidden(true)`.
```swift
var body: some View {
    PirateView()
        .navigationBarBackButtonHidden()
}
```

## Step 2: Access the `dismiss` environment value
Inject the `dismiss` environment value so our custom back button can use it.
```swift
@Environment(\.dismiss) private var dismiss
```

## Step 3: Create a custom back button
Let's put our custom button into a method returning `some View`. You can style it any way you want. I often like it like this:
```swift
private func chevronOnlyBackButton() -> some View {
    Button {
        dismiss()
    } label: {
        Image(systemName: "chevron.left")
    }
    .tint(.secondary)
}
```

## Step 4: Place it in the toolbar
Now we'll put our button in the toolbar with a `.topBarLeading` placement.
```swift
var body: some View {
    PirateView()
        .navigationBarBackButtonHidden()
        .toolbar {
            ToolbarItem(placement: .topBarLeading, content: chevronOnlyBackButton)
        }
}
```

Great, now we've got a custom back button. But wait, what's that? Swiping from the left edge doesn't work anymore! We broke this in step 1. Let's fix it!

## Step 5: Fix swiping back
I'm not entirely satisfied with this step, but it's the best way I found. It works, but it relies on the fact that SwiftUI is using UIKit under the hood. Should that change at some point in the future, this solution might break. So be aware of that and keep an eye on it.
We'll create an extension on `UINavigationController`, which is used under the hood by SwiftUI navigation, taking several measures to ensure everything works nicely together with other gestures.
```swift
import UIKit

extension UINavigationController: @retroactive UIGestureRecognizerDelegate {
    override open func viewDidLoad() {
        super.viewDidLoad()
        interactivePopGestureRecognizer?.delegate = self
    }

    public func gestureRecognizerShouldBegin(_: UIGestureRecognizer) -> Bool {
        guard viewControllers.count > 1, presentedViewController == nil else { return false }
        return true
    }

    public func gestureRecognizer(_ gestureRecognizer: UIGestureRecognizer, shouldRecognizeSimultaneouslyWith otherGestureRecognizer: UIGestureRecognizer) -> Bool {
        viewControllers.count > 1
    }

    public func gestureRecognizer(_ gestureRecognizer: UIGestureRecognizer, shouldBeRequiredToFailBy otherGestureRecognizer: UIGestureRecognizer) -> Bool {
        viewControllers.count > 1
    }
}
```
It doesn't really matter where you put this code, as long as it's loaded at app startup. I recommend creating a new file in the main app module for it and name it something like `UINavigationController+SwipeBack.swift`.

If you find a better solution, feel free to contact me, I'd like to know!

---

## Links and References
I found the solution for restoring the swipe gesture (step 5) here: [Reddit comment by Tabonx](https://www.reddit.com/r/SwiftUI/comments/1g0pmst/comment/mx2sy0s).