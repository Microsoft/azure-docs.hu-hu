---
title: Media Services v3 terminológia és entitás változásai
description: Ez a cikk a Azure Media Services v2 és v3 közötti terminológiai különbségeket ismerteti.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: 6f677c8753f09e146d300186e19217568952b417
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101705398"
---
# <a name="terminology-and-entity-changes-between-media-services-v2-and-v3"></a>Terminológia és entitások változása Media Services v2 és v3 között

![Áttelepítési útmutató emblémája](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![2. áttelepítési lépések](./media/migration-guide/steps-2.svg)

Ez a cikk a Azure Media Services v2 és v3 közötti terminológiai különbségeket ismerteti.

## <a name="naming-conventions"></a>Elnevezési konvenciók

Tekintse át Media Services v3 erőforrásokra alkalmazott elnevezési konvenciókat. Tekintse át a [Blobok elnevezését](assets-concept.md#naming) is

## <a name="terminology-changes"></a>Terminológiai változások

- A *lokátor* neve " *streaming" lokátor*.
- Egy *csatornát* már *élő eseménynek* nevezünk.
- A *program* mostantól *élő kimenetnek* is nevezett.
- Egy *feladat* most már egy feladat részét képező *JobOutput* néven is ismert.

## <a name="entity-changes"></a>Entitások módosításai
| **V2 entitás**<!-- row --> | **V3 entitás** | **Útmutató** | **Elérhető a v3 számára** | **Frissítette: v3** |
|--|--|--|--|--|
| `AccessPolicy`<!-- row --> | <!-- empty --> |  Az entitás `AccessPolicies` nem létezik a v3-ben. | Nem | Nem |
| `Asset`<!-- row --> | `Asset` | <!-- empty --> | Igen | Igen |
| `AssetDeliveryPolicy`<!-- row --> | `StreamingPolicy` | <!-- empty --> | Igen | Nem |
| `AssetFile`<!-- row --> | <!-- empty --> |Az entitás `AssetFiles` nem létezik a v3-ben. Bár a feltöltött fájlok (tároló Blobok) továbbra is fájlok.<br/><br/> Használja helyette az Azure Storage API-kat egy tárolóban lévő Blobok enumerálásához. A fájlokra kétféleképpen alkalmazhat átalakításokat a feladatokkal:<br/><br/>A tárolóba már feltöltött fájlok: az URI tartalmazza a Storage-fiókban lévő eszközökön elvégzendő feladatok AZONOSÍTÓját.<br/><br/>Az átalakítási és a feladattípusi folyamat során feltöltendő fájlok: az eszköz a tárolóban jön létre, a rendszer egy SAS URL-címet ad vissza, a fájlok feltöltése a tárolóba, majd a rendszer alkalmazza a transzformációt a fájlokra. | Nem | Nem |
| `Channel`<!-- row --> | `LiveEvent` | Az élő események lecserélik a csatornákat a v2 API-ból. Többek között a legtöbb funkcióval rendelkeznek, és több új funkcióval rendelkeznek, mint például az élő átírások, a készenléti üzemmód és az RTMP-ket támogató funkciók. <br/><br/>Lásd: [élő esemény forgatókönyv-alapú élő közvetítéssel](migrate-v-2-v-3-migration-scenario-based-live-streaming.md) | Nem | Nem |
| `ContentKey`<!-- row --> | <!-- empty --> | `ContentKeys` már nem entitás, most egy streaming Locator tulajdonsága.<br/><br/>  A v3-as verzióban a tartalmi kulcs adatai társítva vannak a `StreamingLocator` (kimeneti titkosításhoz) vagy magához az objektumhoz (az ügyféloldali tároló-titkosításhoz). | Igen | Nem |
| `ContentKeyAuthorizationPolicy`<!-- row --> | `ContentKeyPolicy` | <!-- empty --> | Igen | Nem |
| `ContentKeyAuthorizationPolicyOption` <!-- row --> | <!-- empty --> |  `ContentKeyPolicyOptions` a részét képezik `ContentKeyPolicy` . | Igen | Nem |
| `IngestManifest`<!-- row --> | <!-- empty --> | Az entitás `IngestManifests` nem létezik a v3-ben. A v3 fájlok feltöltése az Azure Storage API-t jelenti. A rendszer először létrehozza az eszközöket, majd feltölti a fájlokat a társított tárolóba. Az Azure Storage-tárolóba számos módon lehet beolvasni az adatmennyiséget. `JobInputHttp` lehetőség van arra is, hogy egy adott URL-címről töltsön le egy feladatot, ha szükséges. | Nem | Nem |
| `IngestManifestAsset`<!-- row --> | <!-- empty --> | Az Azure Storage-tárolóba számos módon lehet beolvasni az adatmennyiséget. `JobInputHttp` lehetőség van arra is, hogy egy adott URL-címről töltsön le egy feladatot, ha szükséges. | Nem | Nem |
| `IngestManifestFile`<!-- row --> | <!-- empty --> | Az Azure Storage-tárolóba számos módon lehet beolvasni az adatmennyiséget. `JobInputHttp` lehetőség van arra is, hogy egy adott URL-címről töltsön le egy feladatot, ha szükséges. | Nem | Nem |
| `Job`<!-- row --> | `Job` | Hozzon létre egy t a `Transform` létrehozása előtt `Job` . | Nem | Nem |
| `JobTemplate`<!-- row --> | `Transform` | Használjon `Transform` helyette. Az átalakítás egy adott feladatokból származó különálló entitás, amely újra felhasználható. | Nem | Nem |
| `Locator`<!-- row --> | `StreamingLocator` | <!--empty --> | Igen | Nem |
| `MediaProcessor`<!-- row --> | <!-- empty --> | Ahelyett, hogy megkeresi a `MediaProcessor` nevet, használja a kívánt beállításkészletet az átalakítás definiálásához. A használt beállításkészlet határozza meg a rendszer által használt adathordozó-processzort. Lásd: kódolási témakörök a [forgatókönyv-alapú kódolásban](migrate-v-2-v-3-migration-scenario-based-encoding.md). <!--Probably needs a link to its own article so customers know Indexerv1 maps to AudioAnalyzerPreset in basic mode, etc.--> | Nem | NA (ReadOnly a v2-ben) |
| `NotificationEndPoint`<!-- row --> | <!--empty --> | A v3-as értesítések Azure Event Grid használatával kezelhetők. A `NotificationEndpoint` Event Grid előfizetés-regisztráció váltja fel, amely a kapott értesítések típusának konfigurációját is magában foglalja (a v2-ben a feladat `JobNotificationSubscription` , a `TaskNotificationSubscription` feladat és a telemetria kezelése `ComponentMonitoringSetting` ). A v2 telemetria Azure Event Grid és Azure Monitor között osztották fel, hogy illeszkedjenek a nagyobb Azure-ökoszisztémák fejlesztéséhez. | Nem | Nem |
| `Program`<!-- row --> | `LiveOutput` | Az élő kimenetek mostantól a V3 API-ban lévő programokat cserélik le.  | Nem | Nem |
| `StreamingEndpoint`<!-- row --> | `StreamingEndpoint` | A folyamatos átviteli végpontok elsődlegesek maradnak. Ezek a dinamikus csomagolás, a titkosítás és a HLS és a DASH tartalom továbbítására szolgálnak mind az élő, mind az igény szerinti folyamatos átvitelhez, vagy közvetlenül a forrástól, vagy a CDN-n keresztül. Az új funkciók közé tartozik a jobb Azure Monitor integráció és a diagramok támogatása. |  Igen | Igen |
| `Task`<!-- row --> | `JobOutput` | Lecserélte `JobOutput` (amely már nem különálló entitás az API-ban).  Lásd: kódolási témakörök a [forgatókönyv-alapú kódolásban](migrate-v-2-v-3-migration-scenario-based-encoding.md). | Nem | Nem |
| `TaskTemplate`<!-- row --> | `TransformOutput` | Lecserélte `TransformOutput` (amely már nem különálló entitás az API-ban). Lásd: kódolási témakörök a [forgatókönyv-alapú kódolásban](migrate-v-2-v-3-migration-scenario-based-encoding.md). | Nem | Nem |
| `Inputs`<!-- row --> | `Inputs` | A bemenetek és kimenetek jelenleg a feladatok szintjén vannak. Lásd: kódolási témakörök a [forgatókönyv-alapú kódolásban](migrate-v-2-v-3-migration-scenario-based-encoding.md) | Nem | Nem |
| `Outputs`<!-- row --> | `Outputs` | A bemenetek és kimenetek jelenleg a feladatok szintjén vannak.  A v3-as verzióban a metaadatok formátuma XML-ből JSON-ra változott.  Az élő kimenetek a létrehozás után kezdődnek, és a törléskor leállnak. Lásd: kódolási témakörök a [forgatókönyv-alapú kódolásban](migrate-v-2-v-3-migration-scenario-based-encoding.md) | Nem | Nem |


| **További változások** | **V2**  | **V3** |
|---|---|---|
| **Storage** <!--new row --> |||
| Tárolás <!--new row --> | | A v3 SDK-k mostantól le vannak választva a Storage SDK-ból, így nagyobb mértékben szabályozható a használni kívánt Storage SDK verziója, és elkerülheti a verziószámozási problémákat.                      |
| **Kódolás** <!--new row --> |||
| Kódolási átviteli sebesség <!--new row --> | átviteli sebesség (Kbit/s): 128 (Kbit/s)| bitek másodpercenként: ex: 128000 (BITS/másodperc)|
| DRM-FairPlay kódolása <!--new row --> | Media Services v2-ben megadható az inicializálási vektor (IV). | Media Services v3-as verzióban nem adható meg a FairPlay IV.|
| Prémium szintű kódoló <!--new row --> | Prémium szintű kódoló és örökölt indexelő| A [prémium szintű kódoló](../previous/media-services-encode-asset.md) és az örökölt [Media Analytics-processzorok](../previous/legacy-components.md) (Azure Media Services indexelő 2 előzetes, Face redactor stb.) nem érhetők el a v3-n keresztül. A hangcsatorna-hozzárendelés támogatását a standard kódolóhoz egészítették ki.  [A Media Services kódolással kapcsolatos dokumentációban](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/Encoding.json)lásd: hang.  | Lásd: kódolási témakörök a [forgatókönyv-alapú kódolásban](migrate-v-2-v-3-migration-scenario-based-encoding.md) |
| **Átalakítások és feladatok** <!--new row -->|||
| A feladatok alapú feldolgozás HTTPS <!--new row --> |<!-- empty -->| A fájl alapú feladatok feldolgozásához a bemenetként HTTPS URL-címet használhat. Nem kell már az Azure-ban tárolt tartalmat használnia, és nem kell eszközöket létrehoznia. |
| ARM-sablonok a feladatokhoz <!--new row --> | Az ARM-sablonok nem léteznek a v2-ben. | Az átalakító használatával újrafelhasználható konfigurációk hozhatók létre, Azure Resource Manager sablonok hozhatók létre, és a feldolgozási beállítások elkülöníthetők több ügyfél vagy bérlő között. |
| **Élő események** <!--new row --> |||
| Streamvégpont <!--new row --> | A folyamatos átviteli végpontok olyan adatfolyam-szolgáltatást jelentenek, amely közvetlenül egy ügyfél-lejátszó alkalmazásnak vagy egy Content Delivery Network (CDN) számára biztosít tartalmat a további terjesztéshez. | A folyamatos átviteli végpontok elsődlegesek maradnak. Ezek a dinamikus csomagolás, a titkosítás és a HLS és a DASH tartalom továbbítására szolgálnak mind az élő, mind az igény szerinti folyamatos átvitelhez, vagy közvetlenül a forrástól, vagy a CDN-n keresztül.  Az új funkciók közé tartozik a jobb Azure Monitor integráció és a diagramok támogatása. |
| Élő események csatornái <!--new row --> | A csatornák az élő adatfolyam tartalmának feldolgozásához felelősek. A csatorna egy bemeneti végpontot (betöltési URL-címet) biztosít, amelyet aztán egy élő transcoder számára biztosít. A csatorna élő bemeneti streameket fogad az élő átkódolóból, és egy vagy több folyamatos átviteli végponton keresztül elérhetővé teszi. A csatornák egy előzetes verziójú végpontot (előzetes verziójú URL-címet) is biztosítanak, amelyet a további feldolgozás és a továbbítás előtt a stream előzetes verziójának megtekintéséhez és érvényesítéséhez használhat.| Az élő események lecserélik a csatornákat a v2 API-ból. Többek között a legtöbb funkcióval rendelkeznek, és több új funkcióval rendelkeznek, mint például az élő átírások, a készenléti üzemmód és az RTMP-ket támogató funkciók. |
| Élő események programjai <!--new row --> | A programok lehetővé teszik az élő stream szegmenseinek közzétételét és tárolását. A programokat a csatornák kezelik. A csatorna és a program kapcsolata hasonló a hagyományos adathordozóhoz, ahol a csatornán állandó tartalom található, és a program hatóköre az adott csatornán futó eseményekre vonatkozik. Megadhatja, hogy hány óra elteltével szeretné megőrizni a program rögzített tartalmát a tulajdonság beállításával `ArchiveWindowLength` . Ez az érték 5 perc és 25 óra közötti lehet.| Az élő kimenetek mostantól a V3 API-ban lévő programokat cserélik le. |
| Élő esemény hossza <!--new row --> |<!-- empty -->| Az 24/7-as élő események továbbítására akkor van lehetőség, ha a Media Services használatával átkódolja az egyetlen sávszélesség-hozzájárulási csatornát egy olyan kimeneti adatfolyamba, amely több bitrátát tartalmaz.|
| Élő esemény késése <!--new row --> |<!-- empty -->| Új, kis késleltetésű élő adatfolyam-támogatás élő eseményeken. |
| Élő esemény előnézete <!--new row --> |<!-- empty -->| Az élő esemény előzetes verziója támogatja a dinamikus csomagolást és a dinamikus titkosítást. Ez az előzetes verzióban, valamint a DASH és a HLS csomagoláson is lehetővé teszi a tartalomvédelem használatát. |
| Élő esemény RTMP-je <!--new row --> |<!-- empty-->| A továbbfejlesztett RTMP-támogatás nagyobb stabilitást és több forrás-kódoló támogatást nyújt. |
| Élő Event RTMP biztonságos betöltés <!--new row --> | | Élő esemény létrehozásakor 4 betöltési URL-címet kap. A 4 betöltési URL-cím majdnem azonos, ugyanazzal a folyamatos átviteli jogkivonattal rendelkezik `AppId` , csak a portszám rész különbözik. A két URL-cím elsődleges és biztonsági másolat az RTMP-hez.| 
| Élő esemény átírása <!--new row --> |<!-- empty--> | Az Azure Media Service videó-, hang-és szöveges szolgáltatásokat biztosít különböző protokollokban. Ha az élő streamet az MPEG-DASH vagy a HLS/CMAF használatával teszi közzé, akkor a videó-és hanganyagokkal együtt a szolgáltatás a IMSC 1.1-kompatibilis TTML-ban továbbítja az átmásolt szöveget.|
| Élő esemény készenléti módja <!--new row --> | Nem volt készenléti mód a v2-hez. | A készenléti üzemmód egy új v3-szolgáltatás, amely megkönnyíti az élő események meleg készletének kezelését. A futó állapotba való áttérés előtt az ügyfelek mostantól alacsonyabb áron indíthatják el az élő eseményeket a készenléti módban. Ez javítja a csatorna indítási időpontját, és csökkenti a gyors üzembe helyezéshez szükséges, meleg készletek működési költségeit. |
| Élő esemény számlázása <!--new row --> | <!-- empty-->| Az élő események számlázása az élő csatorna mérőszámán alapul. |
| Élő kimenetek <!--new row --> | A programokat a létrehozás után kell elindítani. | Az élő kimenetek a létrehozás után kezdődnek, és a törléskor leállnak. |

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]