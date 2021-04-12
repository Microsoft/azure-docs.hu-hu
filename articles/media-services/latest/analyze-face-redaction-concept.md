---
title: Arcok keresése és kivonása Azure Media Services V3 API-ban | Microsoft Docs
description: A Azure Media Services v3 olyan arcfelismerés és-kivonási (életlenítési) készletet biztosít, amely lehetővé teszi egy videofájl beküldését, az arcok észlelését, valamint opcionálisan a kivonást (elmosódást) egyetlen kombinált menetben, vagy egy kétlépéses művelettel, amely lehetővé teszi a szerkesztést. Ez a cikk bemutatja, hogyan keresheti meg és takarja el az arcokat a V3 API-ban lévő Arcfelismerés készlettel.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/25/2021
ms.author: johndeu
ms.custom: devx-track-csharp
ms.openlocfilehash: 4907a81fc8cb55499fa97f2b02a3e19e7117bbbc
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286385"
---
# <a name="find-and-redact-blur-faces-with-the-face-detector-preset"></a>Arcok keresése és kivonása (életlenítés) a Face detektor készlettel

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services V3 API tartalmaz egy Arcfelismerés-készletet, amely skálázható arcfelismerés és kivonást (homályos) biztosít a felhőben. Az Arcfelismerés lehetővé teszi a videó módosítását, hogy a kijelölt személyek ne legyenek elmosódottak. Érdemes lehet a Face kivonási szolgáltatást használni a közbiztonság és a hírek adathordozóján. Néhány perces felvétel, amely több arcot tartalmaz, manuálisan is eltarthat, de ezzel az előre beállított értékkel csak néhány egyszerű lépést kell elvégeznie.

Ez a cikk részletesen ismerteti a **Face detektor beállításkészletét** , és bemutatja, hogyan használható a .net-hez készült Azure Media Services SDK-val.

[!INCLUDE [regulation](../video-indexer/includes/regulation.md)]

## <a name="compliance-privacy-and-security"></a>Megfelelőség, adatvédelem és biztonság
 
Fontos megjegyezni, hogy be kell tartania az elemzések Azure Media Servicesban való használatának összes vonatkozó törvényét. Nem használhat Azure Media Services vagy bármely más Azure-szolgáltatást olyan módon, amely megsért mások jogait. Mielőtt bármilyen videót feltölt, beleértve a biometrikus adatokat is a Azure Media Services szolgáltatásba feldolgozásra és tárolásra, az összes megfelelő jogosultsággal kell rendelkeznie, beleértve a megfelelő hozzájárulásokat is a videóban található személyektől. A megfelelőség, az adatvédelem és a biztonság Azure Media Services az Azure [Cognitive Services feltételeinek](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/)megismerése. A Microsoft adatvédelmi kötelezettségeivel és az adatok kezelésével kapcsolatban tekintse át a Microsoft [adatvédelmi nyilatkozatát](https://privacy.microsoft.com/PrivacyStatement), az [online szolgáltatások használati feltételeit](https://www.microsoft.com/licensing/product-licensing/products) (OST) és az [adatfeldolgozási kiegészítést](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) ("DPA"). Az adatmegőrzéssel, törléssel és megsemmisítéssel kapcsolatos további adatvédelmi információk az OST-ben és [itt](../video-indexer/faq.md)érhetők el. A Azure Media Services használatával Ön vállalja, hogy a Cognitive Services feltételek, az OST, a DPA és az adatvédelmi nyilatkozat köti.

## <a name="face-redaction-modes"></a>Szembenéző kivonási módok

Az arc-kivonás úgy működik, hogy a videó minden képkockájában felderíti az arcokat, és nyomon követi a Face objektumot az időben és visszafelé, így ugyanazokat a személyeket más nézőpontokból is el lehet tekinteni. Az automatikus kivonási folyamat összetett, és nem minden esetben 100%-os garantált. Emiatt az előre definiált kétlépéses mód használható az elmosódás minőségének és pontosságának javítására a szerkesztési fázisban, mielőtt elküldi a fájlt a végső életlenítési menethez. 

A kétlépcsős, teljes mértékben automatikus **kombinált** mód mellett a kétlépéses munkafolyamat lehetővé teszi, hogy kiválassza az elmosódást (vagy nem életlenítést) a Face ID-EK listáján keresztül. A kereten belüli tetszőleges beállítások megadásához az előre megadott metaadat-fájlt JSON formátumban adja meg a második pass bemenetként. A munkafolyamat **elemzése** és **kivonási** módokra van bontva.

A két módot is egyszerűen egyesítheti egyetlen olyan menetben, amely egy feladatban mindkét feladatot futtatja; ezt a módot **Összevontnak** nevezzük.  Ebben a cikkben a mintakód bemutatja, hogyan használható az egyszerűsített egymenetes **kombinált** mód egy minta forrásfájlban.

### <a name="combined-mode"></a>Kombinált mód

Ezzel a művelettel egy kivont MP4-videofájl egyetlen menetben, a szükséges JSON-fájl manuális szerkesztése nélkül hozható létre. A feladatokhoz tartozó Asset mappában lévő kimenet egyetlen. mp4-fájl lesz, amely elmosódott arcokat tartalmaz a kijelölt életlenítési effektus használatával. A kivonás legjobb eredményeinek eléréséhez használja a felbontás tulajdonságot a **SourceResolution** értékre.

| Fázis | Fájlnév | Jegyzetek |
| --- | --- | --- |
| Bemeneti eszköz |"ignite-sample.mp4" |Videó WMV, MOV vagy MP4 formátumban |
| Előre beállított konfiguráció |Arcfelismerés-konfiguráció | **Mode**: FaceRedactorMode. combined,  **blurType**: blurType. med, **felbontás**: AnalysisResolution. SourceResolution |
| Kimeneti eszköz |"ignite-redacted.mp4 |Az arcokra alkalmazott homályos effektusú videó |

### <a name="analyze-mode"></a>Elemzési mód

A kétlépéses munkafolyamat **elemzése** átveszi a videó bemenetét, és egy JSON-fájlt hoz létre az összes észlelt arc, a Face azonosító és a jpg-lemezképek listájával. 

| Fázis | Fájlnév | Jegyzetek |
| --- | --- | --- |
| Bemeneti eszköz |"ignite-sample.mp4" |Videó WMV, MPV vagy MP4 formátumban |
| Előre beállított konfiguráció |Arcfelismerés-konfiguráció |**Mode**: FaceRedactorMode. elemzés, **felbontás**: AnalysisResolution. SourceResolution|
| Kimeneti eszköz |ignite-sample_annotations.jsbekapcsolva |Az arc helyeinek megjegyzései JSON formátumban. Ezt a felhasználó módosíthatja az elmosódást határoló mezők módosításához. Lásd az alábbi mintát. |
| Kimeneti eszköz |foo_thumb% 06d.jpg [foo_thumb000001.jpg, foo_thumb000002.jpg] |Az egyes észlelt arcoknál az összes megjelenített jpg, ahol a szám az arc labelId jelöli. |

#### <a name="output-example"></a>Példa kimenetre

```json
{
  "version": 1,
  "timescale": 24000,
  "offset": 0,
  "framerate": 23.976,
  "width": 1280,
  "height": 720,
  "fragments": [
    {
      "start": 0,
      "duration": 48048,
      "interval": 1001,
      "events": [
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [
          {
            "index": 13,
            "id": 1138,
            "x": 0.29537,
            "y": -0.18987,
            "width": 0.36239,
            "height": 0.80335
          },
          {
            "index": 13,
            "id": 2028,
            "x": 0.60427,
            "y": 0.16098,
            "width": 0.26958,
            "height": 0.57943
          }
        ],

    ... truncated
```

### <a name="redact-blur-mode"></a>Kivonási (életlenítési) mód

A munkafolyamat második lépése nagyobb mennyiségű bemenetet igényel, amelyeket egyetlen objektumba kell összevonni.

Ide tartozik az életlenítés, az eredeti videó és a jegyzetek JSON-azonosítóinak listája. Ez a mód a jegyzeteket használja a bemeneti videó elmosódásának alkalmazásához.

Az elemzés menetének kimenete nem tartalmazza az eredeti videót. A videót fel kell tölteni a bemeneti eszközbe a kivonási mód feladathoz, és az elsődleges fájlként kell kiválasztani.

| Fázis | Fájlnév | Jegyzetek |
| --- | --- | --- |
| Bemeneti eszköz |"ignite-sample.mp4" |Videó WMV-, MPV-vagy MP4-formátumban. Ugyanaz a videó, mint az 1. lépésben. |
| Bemeneti eszköz |"ignite-sample_annotations.jsbekapcsolva" |Megjegyzések metaadat-fájlja az első fázisból, választható módosításokkal, ha módosítani szeretné az arcokat. Ezt külső alkalmazásban, kódban vagy szövegszerkesztőben kell szerkeszteni. |
| Bemeneti eszköz | "ignite-sample_IDList.txt" (nem kötelező) | A kibontani kívánt arc-azonosítók új, sorba tagolt listája. Ha üresen hagyja, a forrásban lévő összes arc el lesz alkalmazva. A listával szelektíven választhatja ki, hogy az egyes arcok ne legyenek elmosódottak. |
| Arcfelismerés előre beállított  |Előre beállított konfiguráció  | **Mode**: FaceRedactorMode. derövidítése, **blurType**: blurType. med |
| Kimeneti eszköz |"ignite-sample-redacted.mp4" |Videó az elmosódást alkalmazva a jegyzetek alapján |

#### <a name="example-output"></a>Példa kimenetre

Egy kiválasztott AZONOSÍTÓval rendelkező IDList kimenete.

Példa foo_IDList.txt

```
1
2
3
```

## <a name="blur-types"></a>Életlenítési típusok

A **kombinált** vagy a **kivonási** módban öt különböző életlenítési mód közül választhat a JSON bemeneti konfigurációjának használatával: **alacsony**, **Med**, **magas**, **doboz** és **fekete**. Alapértelmezés szerint a **Med** használatos.

Az alábbi életlenítési típusok mintáit találhatja meg.


#### <a name="low"></a>Alacsony

![Kis felbontású életlenítés beállításának példája.](./media/media-services-face-redaction/blur-1.png)

#### <a name="med"></a>Med

![Közepes felbontású életlenítési beállítási példa.](./media/media-services-face-redaction/blur-2.png)

#### <a name="high"></a>Magas

![Példa a nagy felbontású életlenítés beállítására.](./media/media-services-face-redaction/blur-3.png)

#### <a name="box"></a>Box

![Box mód a kimenet hibakereséséhez.](./media/media-services-face-redaction/blur-4.png)

#### <a name="black"></a>Fekete

![A fekete Box mód a fekete mezőkkel rendelkező összes arcot magában foglalja.](./media/media-services-face-redaction/blur-5.png)

## <a name="elements-of-the-output-json-file"></a>A kimeneti JSON-fájl elemei

A kivonási MP magas pontosságú arcfelismerés és-követést biztosít, amely a videó keretén belül akár 64 emberi arcot képes észlelni. Az elülső arcok biztosítják a legjobb eredményeket, míg az arcok és a kis arcok (24x24 képpontnál kisebb vagy egyenlő) kihívást jelentenek.

[!INCLUDE [media-services-analytics-output-json](../../../includes/media-services-analytics-output-json.md)]

## <a name="net-sample-code"></a>.NET-mintakód

A következő program bemutatja, hogyan használható a **kombinált** egyfázisú kivonási mód:

- Hozzon létre egy adategységet, és töltsön fel egy médiafájlt az eszközre.
- Konfigurálja a Mode és a blurType beállításokat használó Arcfelismerés-készletet.
- Hozzon létre egy új átalakítót a Face detektor beállításkészletének használatával
- Töltse le a kimeneti kitakart videofájl.

## <a name="download-and-configure-the-sample"></a>A minta letöltése és konfigurálása

Klónozza a gépre a .NET-mintát tartalmazó GitHub-adattárat a következő paranccsal:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet.git
 ```

A minta a [FaceRedactor](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoAnalytics/FaceRedactor) mappában található. Nyissa meg [appsettings.jsa](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/VideoAnalytics/FaceRedactor/appsettings.json) letöltött projektben. Cserélje le az értékeket az API-khoz [való hozzáféréshez](./access-api-howto.md)kapott hitelesítő adatokkal.

 Igény szerint átmásolhatja a **[minta. env](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/sample.env)** fájlt a tárház gyökerére, és kitöltheti a benne található adatokat, és átnevezheti a fájlt **. env** (az első pontra, az elején lévő pontra), hogy az a tárházban lévő összes minta projektben használható legyen.  Így nincs szükség az egyes mintákban található fájlokra vonatkozó kitöltött appsettings.js, valamint a saját git hub klónozott adattárakban lévő beállítások ellenőrzésére is.

#### <a name="examples"></a>Példák

Ez a kód azt mutatja be, hogyan lehet beállítani a **FaceDetectorPreset** egy **kombinált** mód életlenítése számára.

[!code-csharp[Main](../../../media-services-v3-dotnet/VideoAnalytics/FaceRedactor/Program.cs#FaceDetectorPreset)]

Ez a mintakód azt mutatja be, hogy a rendszer hogyan továbbítja a készletet egy átalakítási objektumba a létrehozás során. Az átalakítás létrehozása után közvetlenül is elküldheti a feladatokat.

[!code-csharp[Main](../../../media-services-v3-dotnet/VideoAnalytics/FaceRedactor/Program.cs#FaceDetectorPresetTransform)]

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

