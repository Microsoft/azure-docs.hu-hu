---
title: '& létrehozása az Objective-C-ben'
description: Részletesen ismerteti, hogyan hozhatók létre és találhatók az Azure térbeli horgonyok az Objective-C szolgáltatásban.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: c4de1bf048284a630b5f07be53445404eeb450fb
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2020
ms.locfileid: "92097490"
---
# <a name="how-to-create-and-locate-anchors-using-azure-spatial-anchors-in-objective-c"></a>Horgonyok létrehozása és megkeresése az Objective-C-ben az Azure térbeli horgonyok használatával

> [!div  class="op_single_selector"]
> * [Unity](create-locate-anchors-unity.md)
> * [Objective-C](create-locate-anchors-objc.md)
> * [Swift](create-locate-anchors-swift.md)
> * [Android Java](create-locate-anchors-java.md)
> * [C++/NDK](create-locate-anchors-cpp-ndk.md)
> * [C++/WinRT](create-locate-anchors-cpp-winrt.md)

Az Azure térbeli horgonyok lehetővé teszik, hogy a különböző eszközök között ossza meg a világbeli horgonyokat. Számos különböző fejlesztési környezetet támogat. Ebben a cikkben bemutatjuk, hogyan használhatja az Azure térbeli horgonyok SDK-t az Objective-C-ben:

- Az Azure térbeli horgonyok munkamenetének megfelelő beállítása és kezelése.
- Hozzon létre és állítson be tulajdonságokat a helyi horgonyokon.
- Töltse fel őket a felhőbe.
- Felhőbeli térbeli horgonyok megkeresése és törlése.

## <a name="prerequisites"></a>Előfeltételek

Az útmutató elvégzéséhez győződjön meg arról, hogy rendelkezik a következővel:

- Olvassa el az [Azure térbeli horgonyok áttekintése című témakört](../overview.md).
- Az [5 perces rövid](../index.yml)útmutatók egyikét fejezte be.
- Az Objective-C alapszintű ismerete.
- A <a href="https://developer.apple.com/arkit/" target="_blank">ARKit</a>alapszintű ismerete.

[!INCLUDE [Start](../../../includes/spatial-anchors-create-locate-anchors-start.md)]

További információ a [ASACloudSpatialAnchorSession](/objectivec/api/spatial-anchors/asacloudspatialanchorsession) osztályról.

```objc
    ASACloudSpatialAnchorSession *_cloudSession;
    // In your view handler
    _cloudSession = [[ASACloudSpatialAnchorSession alloc] init];
```

[!INCLUDE [Account Keys](../../../includes/spatial-anchors-create-locate-anchors-account-keys.md)]

További információ a [ASASessionConfiguration](/objectivec/api/spatial-anchors/asasessionconfiguration) osztályról.

```objc
    _cloudSession.configuration.accountKey = @"MyAccountKey";
```

[!INCLUDE [Access Tokens](../../../includes/spatial-anchors-create-locate-anchors-access-tokens.md)]

```objc
    _cloudSession.configuration.accessToken = @"MyAccessToken";
```

[!INCLUDE [Access Tokens Event](../../../includes/spatial-anchors-create-locate-anchors-access-tokens-event.md)]

További információ a [tokenRequired](/objectivec/api/spatial-anchors/asacloudspatialanchorsessiondelegate#tokenrequired) protokoll módszeréről.

```objc
    - (void)tokenRequired:(ASACloudSpatialAnchorSession *)cloudSession :(ASATokenRequiredEventArgs *)args {
        args.accessToken = @"MyAccessToken";
    }
```

[!INCLUDE [Asynchronous Tokens](../../../includes/spatial-anchors-create-locate-anchors-asynchronous-tokens.md)]

```objc
    - (void)tokenRequired:(ASACloudSpatialAnchorSession *)cloudSession :(ASATokenRequiredEventArgs *)args {
        ASACloudSpatialAnchorSessionDeferral *deferral = [args getDeferral];
        [myGetTokenAsync callback:^(NSString *myToken) {
            if (myToken) args.accessToken = myToken;
            [deferral complete];
        }];
    }
```

[!INCLUDE [Azure AD Tokens](../../../includes/spatial-anchors-create-locate-anchors-aad-tokens.md)]

```objc
    _cloudSession.configuration.authenticationToken = @"MyAuthenticationToken";
```

[!INCLUDE [Azure AD Tokens Event](../../../includes/spatial-anchors-create-locate-anchors-aad-tokens-event.md)]

```objc
    - (void)tokenRequired:(ASACloudSpatialAnchorSession *)cloudSession :(ASATokenRequiredEventArgs *)args {
        args.authenticationToken = @"MyAuthenticationToken";
    }
```

[!INCLUDE [Asynchronous Tokens](../../../includes/spatial-anchors-create-locate-anchors-asynchronous-tokens.md)]

```objc
    - (void)tokenRequired:(ASACloudSpatialAnchorSession *)cloudSession :(ASATokenRequiredEventArgs *)args {
        ASACloudSpatialAnchorSessionDeferral *deferral = [args getDeferral];
        [myGetTokenAsync callback:^(NSString *myToken) {
            if (myToken) args.authenticationToken = myToken;
            [deferral complete];
        }];
    }
```

[!INCLUDE [Setup](../../../includes/spatial-anchors-create-locate-anchors-setup-ios.md)]

További információ az [indítási](/objectivec/api/spatial-anchors/asacloudspatialanchorsession#start) módszerről.

```objc0
    _cloudSession.session = self.sceneView.session;
    _cloudSession.delegate = self;
    [_cloudSession start];
```

[!INCLUDE [Frames](../../../includes/spatial-anchors-create-locate-anchors-frames.md)]

További információ a [processFrame](/objectivec/api/spatial-anchors/asacloudspatialanchorsession#processframe) metódusról.

```objc
    [_cloudSession processFrame:_sceneView.session.currentFrame];
```

[!INCLUDE [Feedback](../../../includes/spatial-anchors-create-locate-anchors-feedback.md)]

További információ a [sessionUpdated](/objectivec/api/spatial-anchors/asacloudspatialanchorsessiondelegate#sessionupdated) protokoll módszeréről.

```objc
    - (void)sessionUpdated:(ASACloudSpatialAnchorSession *)cloudSession :(ASASessionUpdatedEventArgs *)args {
        ASASessionStatus *status = [args status];
        if (status.userFeedback == ASASessionUserFeedbackNone) return;
        _feedback = [NSString
            stringWithFormat:@"Feedback: %@ - Recommend Create=%.0f%%",
            FeedbackToString(status.userFeedback),
            status.recommendedForCreateProgress * 100.f];
    }
```

[!INCLUDE [Creating](../../../includes/spatial-anchors-create-locate-anchors-creating.md)]

További információ a [ASACloudSpatialAnchor](/objectivec/api/spatial-anchors/asacloudspatialanchor) osztályról.

```objc
    // Create a local anchor, perhaps by hit-testing and creating an ARAnchor
    NSArray<ARHitTestResult *> *hits = [_sceneView.session.currentFrame hitTest:CGPointMake(0.5, 0.5) types:ARHitTestResultTypeEstimatedHorizontalPlane];
    if ([hits count] == 0) return;
    // The hitTest method sorts the resulting list by increasing distance from the camera
    // The first hit result will usually be the most relevant when responding to user input
    ARAnchor *localAnchor = [[ARAnchor alloc] initWithTransform:hits[0].worldTransform];
    [_sceneView.session addAnchor:localAnchor];

    // If the user is placing some application content in their environment,
    // you might show content at this anchor for a while, then save when
    // the user confirms placement.
    ASACloudSpatialAnchor *cloudAnchor = [[ASACloudSpatialAnchor alloc] init];
    cloudAnchor.localAnchor = localAnchor;
    [_cloudSession createAnchor:cloudAnchor withCompletionHandler:^(NSError *error) {
        if (error) {
            _feedback = [NSString stringWithFormat:@"Save Failed:%@", error.localizedDescription];
            return;
        }
        _feedback = [NSString stringWithFormat:@"Created a cloud anchor with ID=%@", cloudAnchor.identifier];
    }];
```

[!INCLUDE [Session Status](../../../includes/spatial-anchors-create-locate-anchors-session-status.md)]

További információ a [getSessionStatusWithCompletionHandler](/objectivec/api/spatial-anchors/asacloudspatialanchorsession#getsessionstatus) metódusról.

```objc
    [_cloudSession getSessionStatusWithCompletionHandler:^(ASASessionStatus *value, NSError *error) {
        if (error) {
            _feedback = [NSString stringWithFormat:@"Session status error:%@", error.localizedDescription];
            return;
        }
        if (value.recommendedForCreateProgress < 1.0f) return;
        // Issue the creation request ...
    }];
```

[!INCLUDE [Setting Properties](../../../includes/spatial-anchors-create-locate-anchors-setting-properties.md)]

További információ a [appProperties](/objectivec/api/spatial-anchors/asacloudspatialanchor#appproperties) tulajdonságról.

```objc
    ASACloudSpatialAnchor *cloudAnchor = [[ASACloudSpatialAnchor alloc] init];
    cloudAnchor.localAnchor = localAnchor;
    cloudAnchor.appProperties = @{ @"model-type" : @"frame", @"label" : @"my latest picture" };
    [_cloudSession createAnchor:cloudAnchor withCompletionHandler:^(NSError *error) {
        // ...
    });
```

[!INCLUDE [Update Anchor Properties](../../../includes/spatial-anchors-create-locate-anchors-updating-properties.md)]

További információ a [updateAnchorProperties](/objectivec/api/spatial-anchors/asacloudspatialanchorsession#updateanchorproperties) metódusról.

```objc
    ASACloudSpatialAnchor *anchor = /* locate your anchor */;
    [anchor.appProperties setValue:@"just now" forKey:@"last-user-access"];
    [_cloudSession updateAnchorProperties:anchor withCompletionHandler:^(NSError *error) {
        if (error) _feedback = [NSString stringWithFormat:@"Updating Properties Failed:%@", error.localizedDescription];
    }];
```

[!INCLUDE [Getting Properties](../../../includes/spatial-anchors-create-locate-anchors-getting-properties.md)]

További információ a [getAnchorProperties](/objectivec/api/spatial-anchors/asacloudspatialanchorsession#getanchorproperties) metódusról.

```objc
    [_cloudSession getAnchorProperties:@"anchorId" withCompletionHandler:^(SCCCloudSpatialAnchor *anchor, NSError *error) {
        if (error) {
            _feedback = [NSString stringWithFormat:@"Getting Properties Failed:%@", error.localizedDescription];
            return;
        }
        if (anchor) {
            [anchor.appProperties setValue:@"just now" forKey:@"last-user-access"];
            [_cloudSession updateAnchorProperties:anchor withCompletionHandler:^(NSError *error) {
                // ...
            }];
        }
    }];
```

[!INCLUDE [Expiration](../../../includes/spatial-anchors-create-locate-anchors-expiration.md)]

További információ a [lejárat](/objectivec/api/spatial-anchors/asacloudspatialanchor#expiration) tulajdonságról.

```objc
    int secondsInAWeek = 60 * 60 * 24 * 7;
    NSDate *oneWeekFromNow = [[NSDate alloc] initWithTimeIntervalSinceNow: (NSTimeInterval) secondsInAWeek];
    cloudAnchor.expiration = oneWeekFromNow;
```

[!INCLUDE [Locate](../../../includes/spatial-anchors-create-locate-anchors-locating.md)]

További információ a [createWatcher](/objectivec/api/spatial-anchors/asacloudspatialanchorsession#createwatcher) metódusról.

```objc
    ASAAnchorLocateCriteria *criteria = [ASAAnchorLocateCriteria new];
    criteria.identifiers = @[ @"id1", @"id2", @"id3" ];
    [_cloudSession createWatcher:criteria];
```

[!INCLUDE [Locate Events](../../../includes/spatial-anchors-create-locate-anchors-locating-events.md)]

További információ a [anchorLocated](/objectivec/api/spatial-anchors/asacloudspatialanchorsessiondelegate#anchorlocated) protokoll módszeréről.

```objc
    - (void)anchorLocated:(ASACloudSpatialAnchorSession *)cloudSession :(ASAAnchorLocatedEventArgs *)args {
        ASALocateAnchorStatus status = [args status];
        switch (status) {
        case ASALocateAnchorStatusLocated: {
            ASACloudSpatialAnchor *foundAnchor = [args anchor];
            // Go add your anchor to the scene...
        }
            break;
        case ASALocateAnchorStatusAlreadyTracked:
            // This anchor has already been reported and is being tracked
            break;
        case ASALocateAnchorStatusNotLocatedAnchorDoesNotExist:
            // The anchor was deleted or never existed in the first place
            // Drop it, or show UI to ask user to anchor the content anew
            break;
        case ASALocateAnchorStatusNotLocated:
            // The anchor hasn't been found given the location data
            // The user might in the wrong location, or maybe more data will help
            // Show UI to tell user to keep looking around
            break;
    }
```

[!INCLUDE [Deleting](../../../includes/spatial-anchors-create-locate-anchors-deleting.md)]

További információ a [deleteAnchor](/objectivec/api/spatial-anchors/asacloudspatialanchorsession#deleteanchor) metódusról.

```objc
    [_cloudSession deleteAnchor:cloudAnchor withCompletionHandler:^(NSError *error) {
        // Perform any processing you may want when delete finishes
    }];
```

[!INCLUDE [Stopping](../../../includes/spatial-anchors-create-locate-anchors-stopping.md)]

További információ a [leállítási](/objectivec/api/spatial-anchors/asacloudspatialanchorsession#stop) módszerről.

```objc
    [_cloudSession stop];
```

[!INCLUDE [Resetting](../../../includes/spatial-anchors-create-locate-anchors-resetting.md)]

További információ az [alaphelyzetbe állítási](/objectivec/api/spatial-anchors/asacloudspatialanchorsession#reset) módszerről.

```objc
    [_cloudSession reset];
```

[!INCLUDE [Cleanup](../../../includes/spatial-anchors-create-locate-anchors-cleanup-others.md)]

```objc
    _cloudSession = NULL;
```

[!INCLUDE [Next Steps](../../../includes/spatial-anchors-create-locate-anchors-next-steps.md)]