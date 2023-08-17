---
title: Use the Azure Maps indoor maps module to develop iOS applications with Microsoft Creator services
description: Learn how to use the Microsoft Azure Maps indoor maps module for the iOS SDK to render maps by embedding the module's JavaScript libraries.
author: dubiety
ms.author: yuchungchen 
ms.date: 12/10/2021
ms.topic: how-to
ms.service: azure-maps
ms.custom: devx-track-js
services: azure-maps
---

# Indoor maps in the iOS SDK (Preview)

The Azure Maps iOS SDK allows you to render indoor maps created in Azure Maps Creator services.

## Prerequisites

1. Complete the steps in the [Quickstart: Create an iOS app]. Code blocks in this article can be inserted into the `viewDidLoad` function of `ViewController`.
1. A [Creator resource]
1. Get a `tilesetId` by completing the [Tutorial: Use Creator to create indoor maps]. The tileset ID is used to render indoor maps with the Azure Maps iOS SDK.

## Instantiate the indoor manager

To load the indoor tilesets and map style of the tiles, you must instantiate an `IndoorManager` and keep a strong reference to it.

```swift
guard let indoor = try? IndoorManager(azureMap: map, options: [.tilesetID({Your-tilesetID})]) else { return }
self.indoorManager = indoor
```

> [!IMPORTANT]
> This guide assumes that your Creator service was created in the United States. If your Creator service was created in Europe, add the following code:
>
> ```swift
> self.indoorManager.setOptions([.geography(.eu)])
> ```

## Indoor level picker control

The *Indoor Level Picker* control allows you to change the level of the rendered map. You can optionally initialize an `IndoorControl` and set to the appropriate option on the `IndoorManager` as in the following code:

```swift
let levelControl = IndoorControl(options: [.controlPosition(.topRight)])
self.indoorManager.setOptions([.levelControl(levelControl)])
```

> [!TIP]
> The *level picker* appears when you tap on a facility.

## Indoor events

Add a delegate to the `IndoorManager` to listen to indoor map events:

```swift
self.indoorManager.addDelegate(self)
```

`IndoorManagerDelegate` has one method, which is invoked when a facility or floor changes.

```swift
func indoorManager(
    _ manager: IndoorManager,
    didSelectFacility selectedFacility: IndoorFacilitySelection,
    previousSelection: IndoorFacilitySelection
) {
    // code that you want to run after a facility or floor has been changed
    print("New selected facility's ID:", selectedFacility.facilityID)
    print("New selected floor:", selectedFacility.levelsOrdinal)
}
```

## Example

The following screenshot shows the above code displaying an indoor map.

![A screenshot that displays an indoor map created using the above sample code.](./media/ios-sdk/indoor-maps/indoor.png)

## Additional information

- [Creator for indoor maps]
- [Drawing package requirements]

[Quickstart: Create an iOS app]: quick-ios-app.md
[Creator resource]: how-to-manage-creator.md
[Tutorial: Use Creator to create indoor maps]: tutorial-creator-indoor-maps.md
[Creator for indoor maps]: creator-indoor-maps.md
[Drawing package requirements]: drawing-requirements.md
