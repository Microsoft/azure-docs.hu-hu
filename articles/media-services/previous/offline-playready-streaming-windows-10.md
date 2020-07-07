---
title: Fiók konfigurálása a PlayReady által védett tartalom offline folyamatos átviteléhez – Azure
description: Ez a cikk bemutatja, hogyan konfigurálhatja Azure Media Services-fiókját a Windows 10 rendszerű streaming PlayReady.
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
ms.date: 04/16/2019
ms.author: willzhan
ms.openlocfilehash: 001d408eaa7ce637bd7cc1f1183dd8748cddf539
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "82189522"
---
# <a name="offline-playready-streaming-for-windows-10"></a>Offline PlayReady-streamelés Windows 10-hez  

> [!div class="op_single_selector" title1="Válassza ki a használt Media Services verzióját:"]
> * [3-as verzió](../latest/offline-plaready-streaming-for-windows-10.md)
> * [2-es verzió](offline-playready-streaming-windows-10.md)

> [!NOTE]
> A Media Services v2 nem fog bővülni újabb funkciókkal és szolgáltatásokkal. <br/>Tekintse meg a legújabb, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/)verziót. Lásd még: [az áttelepítési útmutató v2-től v3-ig](../latest/migrate-from-v2-to-v3.md)

Azure Media Services támogatja az offline letöltést/lejátszást a DRM-védelemmel. Ez a cikk a Azure Media Services Windows 10-es/PlayReady-ügyfelekhez való offline támogatását ismerteti. Az alábbi cikkekben olvashat az iOS/FairPlay és az Android/Widevine rendszerű eszközök offline üzemmódjának támogatásáról:

- [Offline FairPlay-streamelés iOS-hez](media-services-protect-hls-with-offline-fairplay.md)
- [Offline Widevine streaming Android rendszerhez](offline-widevine-for-android.md)

## <a name="overview"></a>Áttekintés

Ez a szakasz az offline üzemmód lejátszásának hátterét ismerteti, különösen miért:

* Egyes országokban/régiókban az Internet rendelkezésre állása és/vagy sávszélessége továbbra is korlátozott.A felhasználók úgy dönthetnek, hogy először letöltik a tartalmat, hogy a megfelelő megjelenítési élmény érdekében elég nagy felbontásban láthassák a tartalmakat. Ebben az esetben a probléma gyakran nem a hálózat rendelkezésre állása, hanem korlátozott hálózati sávszélesség. Az OTT/OVP szolgáltatók az offline üzemmód támogatását kérik.
* A Netflix 2016 Q3 részvényesi konferencián közzétett tartalom letöltése a "gyakran igényelt szolgáltatás", a "nyitottak vagyunk", és a Netflix, a Netflix VEZÉRIGAZGATÓJA.
* Egyes tartalomszolgáltatók nem engedélyezhetik az ország/régió szegélyén túli DRM-licencek kézbesítését. Ha a felhasználónak külföldön kell utaznia, és továbbra is szeretné megtekinteni a tartalmat, offline letöltésre van szükség.
 
Az offline mód megvalósítása során feltett kihívás a következő:

* Az MP4-t sok játékos, kódoló eszköz támogatja, de az MP4-tároló és a DRM között nincs kötés.
* Hosszú távon a CFF és a CENC a következő módon érhető el:. Napjainkban azonban az eszközök/lejátszó-támogatás ökoszisztémája még nem létezik. Jelenleg egy megoldásra van szükségünk.
 
Az ötlet a következő: Smooth streaming ([PIFF](https://docs.microsoft.com/iis/media/smooth-streaming/protected-interoperable-file-format)) fájlformátum (h264/aac) PLAYREADY (AES-128 CTR) kötéssel rendelkezik. Az egyéni Smooth streaming. ismv-fájl (feltéve, hogy a hang egyesített a videóban) önmagában egy fMP4, és használható a lejátszáshoz. Ha egy zökkenőmentes adatfolyam-továbbítási tartalom PlayReady-titkosításon halad át, akkor mindegyik. ismv fájl PlayReady védett töredezett MP4-re vált. Kiválaszthat egy. ismv-fájlt az előnyben részesített bitrátával, és átnevezheti. mp4-ként a letöltéshez.

A PlayReady Protected MP4 a progresszív letöltéshez két lehetőség áll rendelkezésre:

* Ezt az MP4-t ugyanabba a tárolóba/adathordozó szolgáltatásba helyezheti el, és kihasználhatja Azure Media Services streaming-végpontot a progresszív letöltéshez;
* A közvetlenül az Azure Storage-ból, a Azure Media Services megkerülésével az SAS-lokátort használhatja a progresszív letöltéshez.
 
Kétféle PlayReady-licencet használhat:

* PlayReady-licenc kézbesítési szolgáltatása a Azure Media Services;
* A bárhol üzemeltetett PlayReady-kiszolgálók.

Az alábbi két PlayReady-licencet használja az AMS-ben, a másodikban pedig egy Azure-beli virtuális gépen üzemeltetett PlayReady-licenckiszolgálót:

Eszköz #1:

* Progresszív letöltési URL-cím:[https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4](https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4)
* PlayReady LA_URL (AMS):`https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/`

Eszköz #2:

* Progresszív letöltési URL-cím:[https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4](https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4)
* PlayReady LA_URL (helyszíni):`https://willzhan12.cloudapp.net/playready/rightsmanager.asmx`

A lejátszás teszteléséhez egy univerzális Windows-alkalmazást használok a Windows 10 rendszeren. A [Windows 10-es univerzális példákban](https://github.com/Microsoft/Windows-universal-samples)van egy [adaptív adatfolyam-minta](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/AdaptiveStreaming)nevű alapszintű lejátszó minta. Mindössze annyit kell tennie, hogy hozzáadjuk a kódot a letöltött videó kiválasztásához és forrásként való használatához, az adaptív adatfolyam-forrás helyett. A változtatások gombon kattintson az eseménykezelő elemre:

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

 ![PlayReady által védett fMP4 kapcsolat nélküli módban való lejátszása](./media/offline-playready/offline-playready1.jpg)

Mivel a videó a PlayReady védelme alatt áll, a képernyőkép nem fogja tudni felvenni a videót.

Összefoglalva, a Azure Media Services offline üzemmódját is elértük:

* A tartalom átkódolása és PlayReady titkosítása Azure Media Services vagy más eszközökön végezhető el;
* A tartalom a progresszív letöltéshez Azure Media Services vagy Azure Storage-ban is üzemeltethető.
* A PlayReady-licencek kézbesítése Azure Media Services vagy máshol is lehet.
* Az előkészített Smooth streaming-tartalom továbbra is használható az online streaminghez DASH vagy Smooth használatával a PlayReady-mel.

## <a name="additional-notes"></a>További megjegyzések

* A Widevine a Google Inc által biztosított szolgáltatás, és a Google, Inc. szolgáltatási és adatvédelmi szabályzatának feltételei vonatkoznak rá.

## <a name="next-steps"></a>További lépések

[Hibrid DRM-rendszer terve](hybrid-design-drm-sybsystem.md)
