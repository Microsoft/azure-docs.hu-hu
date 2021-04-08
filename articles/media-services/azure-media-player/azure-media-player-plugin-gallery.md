---
title: Azure Media Player beépülő modul gyűjteménye
description: Ez a cikk a Azure Media Playerhoz elérhető beépülő modulok listáját tartalmazza.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: 71fa79cb8847d16ac0890f9aba647cb8f5e2e444
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99089342"
---
# <a name="azure-media-player-plugin-gallery"></a>Azure Media Player beépülő modul gyűjteménye #

## <a name="plugins"></a>Beépülő modulok ##

| Beépülő modul neve                         | Bemutató URL-címe                    | Forráskód                | Leírás    |
|-------------------------------------|-----------------------------|----------------------------|----------------|
| További funkciók                 | | | |
| **Új!** AMP360Video                | [Bemutató](http://www.babylonjs.com/demos/amp360video/)                        | [GitHub](https://github.com/BabylonJS/Extensions/tree/master/Amp360Video)                     | A beépülő modul lehetővé teszi a 360 videó megjelenítését az amp vagy az asztalon vagy a VR-kompatibilis eszközökön. A teljes dokumentáció [itt](https://doc.babylonjs.com/extensions/amp360video)érhető el: |
|  Sprite tipp                         | [Bemutató](https://www.smwcentral.net/?p=section&a=details&id=10301)                        | [GitHub](https://github.com/RickShahid/SpriteTip)                    | Azure Media Player (AMP) beépülő modul a videó miniatűr képének megjelenítéséhez, amely Azure Media Services (AMS) Media Encoder Standard (MES) alapján jön létre. |
| Diagnosztika átfedésben                 | [Bemutató](https://openidconnectweb.azurewebsites.net/Diagnoverlay.html)                        | [GitHub](https://github.com/willzhan/diagnoverlay)                     | Ez a beépülő modul a következőt jeleníti meg: az összes kulcsfontosságú paraméter, a videó statisztikái, a videolejátszás életciklusának összes eseménye, valamint a DRM-védelemmel kapcsolatos információk, például a Key ID, a licenc-megszerzési URL-címek,                                                                                                                                                                      |
| Frame rate és időkód kalkulátor | Nincs elérhető bemutató | [GitHub](https://github.com/mconverti/media-services-javascript-azure-media-player-framerate-timecode-calculator-plugin)                     | Ez a beépülő modul kiszámítja a videó képkocka arányát az `tfhd` / `trun` első MPEG-Dash videó töredékének MP4-mezői alapján, elemezi az időméretezési értéket az MPEG-Dash ügyfél-jegyzékfájlból, valamint lehetővé teszi az időkód létrehozását egy adott abszolút időtartamra a lejátszótól (valamint a játékosnak az időkód által megadott abszolút időt is biztosít) |
| <strike>Lejátszás sebessége</strike>                      | [Bemutató](https://azure-samples.github.io/media-services-javascript-Azure-Media-Player-playback-rate-plugin/)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-time-tip-plugin)                     | Ez a beépülő modul lehetővé teszi a nézők számára a videó sebességének szabályozását. *Vegye figyelembe, hogy ez a funkció automatikusan elérhető az AMP v 2.0.0 + verzióban, de alapértelmezés szerint le van tiltva.* Ha szeretné megtudni, hogyan engedélyezheti, tekintse meg a mintákat [itt](https://github.com/Azure-Samples/azure-media-player-samples) |
| Egérmutató időbeli hegye                      | [Bemutató](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/timetip/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-time-tip-plugin)                     | Időpontot jelenít meg a folyamatjelző sáv fölé az egérmutatón az idő pontos keresésekor. *Megjegyzés: Ez a beépülő modul már integrálva van az amp-be* , de ha szeretné megismerni, hogy milyen módon van programozva, nyugodtan tekintse meg.                                                                                                                 |
| Cím átfedésben                       | [Bemutató](https://azure-samples.github.io/media-services-javascript-azure-media-player-title-overlay-plugin)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-title-overlay-plugin)                     | Átfedésben lévő videó címe a képernyőn |
| Idősor-jelölők                    | [Bemutató](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/timelinemarkers/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-timeline-markers-plugin)                     | Ez a beépülő modul az időpontok tömbjét veszi igénybe, és a folyamatjelző sáv fölé rendezi az egyes időpontokat. |
| Elemzés                           | | | |
| Application Insights                | [Blogbejegyzés](https://azure.microsoft.com/blog/player-analytics-azure-media-player-plugin/)                   | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-application-insights-plugin)                     | Ez a beépülő modul, amely nyomon követi a lejátszó metrikáit és portjait, hogy Power BI a megtekintők játékosi élményének intuitív grafikus ábrázolását. |
| Google Analytics                    | N/A                         | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-google-analytics-plugin)                     | Google Analytics beépülő modul a Azure Media Playerhoz |
| Diagnosztika                         | | | |
| Diagnosztika kimenete                  | [Bemutató](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/diagnosticslogger/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-diagnostic-logger-plugin)                     | Ez a beépülő modul a saját lejátszójában a diagnosztika tömbjét jeleníti meg, hogy a működés közben megtekintse a bemutató hivatkozást, és megnyitja a JavaScript-konzolt. |
| Könnyű hozzáférés                      | | | |
| Nagyítás                             | [Bemutató](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/zoom/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-zoom-plugin)                     | Ez a beépülő modul egy húzással használható nagyítási skálát jelenít meg a játékosok képernyőjén, így a nézők nagyítani tudják a tartalmat |
| Élő feliratok                       | [Azure blogbejegyzés](https://azure.microsoft.com/blog/live-real-time-captions-with-azure-media-services-and-player/),[SubPly post](http://www.subply.com/en/Products/AzureLiveCaptions.htm) | N/A | *További információ: post.* Teljes körű munkafolyamatok, amelyek élő feliratozásra készültek Azure Media Playerhoz, a bal szélső hivatkozásra kattintva megtekintheti a SubPly webhelyét, és további információkat tudhat meg a megoldásról |
| Gyorsbillentyűk                            | <strike>[Bemutató](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/hotkeys/example.html)</strike>                        | <strike>[GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-hot-keys-plugin)</strike>                     | A gyorsbillentyűk beépülő modul lehetővé teszi, hogy a nézők a lejátszó különböző szempontjait vezérelve általános beépülő modulok, például F teljes képernyős, M az Elnémítás és a nyílbillentyűkkel a folyamatjelző sáv vezérlőelemhez. *Megjegyzés: Ez a beépülő modul már integrálva van az AMP-es verzióba, de ingyenesen használhatja erőforrásként* |
| Közösségi                              | | | |
| Megosztás                               | [Bemutató](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/share/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-social-share-plugin)                     | Ez a beépülő modul egy megosztás gombot ad hozzá a lejátszó vezérlőelem sávjához, így a nézők megoszthatják a barátaikat a Facebook, a Twitter vagy a LinkedIn használatával. |

## <a name="next-steps"></a>Következő lépések ##

- [Azure Media Player rövid útmutató](azure-media-player-quickstart.md)
