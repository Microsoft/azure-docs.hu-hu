---
title: Offline PlayReady streaming konfigurálása
description: Ez a cikk bemutatja, hogyan konfigurálhatja az Azure Media Services v3-fiókját a Windows 10-es streaming PlayReady.
services: media-services
keywords: DASH, DRM, Widevine offline üzemmód, ExoPlayer, Android
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2020
ms.author: willzhan
ms.custom: devx-track-csharp
ms.openlocfilehash: ea3d895970c6a18286d719471a693f774efaa387
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98955229"
---
# <a name="offline-playready-streaming-for-windows-10-with-media-services-v3"></a>Offline PlayReady streaming a Windows 10 rendszerhez Media Services v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services támogatja az offline letöltést/lejátszást a DRM-védelemmel. Ez a cikk a Azure Media Services Windows 10-es/PlayReady-ügyfelekhez való offline támogatását ismerteti. Az alábbi cikkekben olvashat az iOS/FairPlay és az Android/Widevine rendszerű eszközök offline üzemmódjának támogatásáról:

- [Offline FairPlay-streamelés iOS-hez](offline-fairplay-for-ios.md)
- [Offline Widevine streaming Android rendszerhez](offline-widevine-for-android.md)

> [!NOTE]
> Az offline DRM-t csak akkor számítjuk fel, ha egy licencre vonatkozó kérést készítenek a tartalom letöltésekor. A hibák számlázása nem történik meg.

## <a name="overview"></a>Áttekintés

Ez a szakasz az offline üzemmód lejátszásának hátterét ismerteti, különösen miért:

* Egyes országokban/régiókban az Internet rendelkezésre állása és/vagy sávszélessége továbbra is korlátozott. A felhasználók úgy dönthetnek, hogy először letöltik a tartalmat, hogy a megfelelő megjelenítési élmény érdekében elég nagy felbontásban láthassák a tartalmakat. Ebben az esetben a probléma gyakran nem a hálózat rendelkezésre állása, hanem korlátozott hálózati sávszélesség. Az OTT/OVP szolgáltatók az offline üzemmód támogatását kérik.
* A Netflix 2016 Q3 részvényesi konferencián közzétett tartalom letöltése a "gyakran igényelt szolgáltatás", a "nyitottak vagyunk", és a Netflix, a Netflix VEZÉRIGAZGATÓJA.
* Egyes tartalomszolgáltatók nem engedélyezhetik az ország/régió szegélyén túli DRM-licencek kézbesítését. Ha a felhasználónak külföldön kell utaznia, és továbbra is szeretné megtekinteni a tartalmat, offline letöltésre van szükség.
 
Az offline mód megvalósítása során feltett kihívás a következő:

* Az MP4-t sok játékos, kódoló eszköz támogatja, de az MP4-tároló és a DRM között nincs kötés.
* Hosszú távon a CFF és a CENC a következő módon érhető el:. Napjainkban azonban az eszközök/lejátszó-támogatás ökoszisztémája még nem létezik. Jelenleg egy megoldásra van szükségünk.
 
Az ötlet a következő: Smooth streaming ([PIFF](/iis/media/smooth-streaming/protected-interoperable-file-format)) fájlformátum (h264/aac) PLAYREADY (AES-128 CTR) kötéssel rendelkezik. Az egyéni Smooth streaming. ismv-fájl (feltéve, hogy a hang egyesített a videóban) önmagában egy fMP4, és használható a lejátszáshoz. Ha egy zökkenőmentes adatfolyam-továbbítási tartalom PlayReady-titkosításon halad át, akkor mindegyik. ismv fájl PlayReady védett töredezett MP4-re vált. Kiválaszthat egy. ismv-fájlt az előnyben részesített bitrátával, és átnevezheti. mp4-ként a letöltéshez.

A PlayReady Protected MP4 a progresszív letöltéshez két lehetőség áll rendelkezésre:

* Ezt az MP4-t ugyanabba a tárolóba/adathordozó szolgáltatásba helyezheti el, és kihasználhatja Azure Media Services streaming-végpontot a progresszív letöltéshez;
* A közvetlenül az Azure Storage-ból, a Azure Media Services megkerülésével az SAS-lokátort használhatja a progresszív letöltéshez.
 
Kétféle PlayReady-licencet használhat:

* PlayReady-licenc kézbesítési szolgáltatása a Azure Media Services;
* A bárhol üzemeltetett PlayReady-kiszolgálók.

Az alábbi két PlayReady-licencet használja az AMS-ben, a másodikban pedig egy Azure-beli virtuális gépen üzemeltetett PlayReady-licenckiszolgálót:

Eszköz #1:

* Progresszív letöltési URL-cím: [https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4](https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4)
* PlayReady LA_URL (AMS): `https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/`

Eszköz #2:

* Progresszív letöltési URL-cím: [https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4](https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4)
* PlayReady LA_URL (helyszíni): `https://willzhan12.cloudapp.net/playready/rightsmanager.asmx`

A lejátszás teszteléséhez egy univerzális Windows-alkalmazást használtunk a Windows 10 rendszeren. A [Windows 10-es univerzális példákban](https://github.com/Microsoft/Windows-universal-samples)van egy [adaptív adatfolyam-minta](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/AdaptiveStreaming)nevű alapszintű lejátszó minta. Mindössze annyit kell tennie, hogy hozzáadjuk a kódot a letöltött videó kiválasztásához és forrásként való használatához, az adaptív adatfolyam-forrás helyett. A változtatások gombon kattintson az eseménykezelő elemre:

```csharp
private async void LoadUri_Click(object sender, RoutedEventArgs e)
{
    //Uri uri;
    //if (!Uri.TryCreate(UriBox.Text, UriKind.Absolute, out uri))
    //{
    // Log("Malformed Uri in Load text box.");
    // return;
    //}
    //LoadSourceFromUriTask = LoadSourceFromUriAsync(uri);
    //await LoadSourceFromUriTask;

    //willzhan change start
    // Create and open the file picker
    FileOpenPicker openPicker = new FileOpenPicker();
    openPicker.ViewMode = PickerViewMode.Thumbnail;
    openPicker.SuggestedStartLocation = PickerLocationId.ComputerFolder;
    openPicker.FileTypeFilter.Add(".mp4");
    openPicker.FileTypeFilter.Add(".ismv");
    //openPicker.FileTypeFilter.Add(".mkv");
    //openPicker.FileTypeFilter.Add(".avi");

    StorageFile file = await openPicker.PickSingleFileAsync();

    if (file != null)
    {
       //rootPage.NotifyUser("Picked video: " + file.Name, NotifyType.StatusMessage);
       this.mediaPlayerElement.MediaPlayer.Source = MediaSource.CreateFromStorageFile(file);
       this.mediaPlayerElement.MediaPlayer.Play();
       UriBox.Text = file.Path;
    }
    else
    {
       // rootPage.NotifyUser("Operation cancelled.", NotifyType.ErrorMessage);
    }

    // On small screens, hide the description text to make room for the video.
    DescriptionText.Visibility = (ActualHeight < 500) ? Visibility.Collapsed : Visibility.Visible;
}
```

![PlayReady által védett fMP4 kapcsolat nélküli módban való lejátszása](./media/offline-playready-for-windows/offline-playready1.jpg)

Mivel a videó a PlayReady védelme alatt áll, a képernyőkép nem fogja tudni felvenni a videót.

Összefoglalva, a Azure Media Services offline üzemmódját is elértük:

* A tartalom átkódolása és PlayReady titkosítása Azure Media Services vagy más eszközökön végezhető el;
* A tartalom a progresszív letöltéshez Azure Media Services vagy Azure Storage-ban is üzemeltethető.
* A PlayReady-licencek kézbesítése Azure Media Services vagy máshol is lehet.
* Az előkészített Smooth streaming-tartalom továbbra is használható az online streaminghez DASH vagy Smooth használatával a PlayReady-mel.

## <a name="next-steps"></a>Következő lépések

[Hozzáférés-vezérléssel ellátott Multi-DRM-rendszerek tervezése](design-multi-drm-system-with-access-control.md)
