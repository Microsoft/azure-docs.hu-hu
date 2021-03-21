---
title: Biztonsági javaslatok az Azure IoT | Microsoft Docs
description: Ez a cikk az Azure IoT Hub-megoldás biztonságának biztosításához szükséges további lépéseket foglalja össze.
author: dsk-2015
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: article
ms.date: 11/13/2019
ms.author: dkshir
ms.custom:
- security-recommendations
- amqp
- mqtt
ms.openlocfilehash: a1de3a71253b1a82b4423bff279fbf3f7e378da4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96457605"
---
# <a name="security-recommendations-for-azure-internet-of-things-iot-deployment"></a>Biztonsági javaslatok az Azure eszközök internetes hálózata (IoT) üzembe helyezéséhez

Ez a cikk a IoT vonatkozó biztonsági javaslatokat tartalmazza. A javaslatok megvalósítása a közös felelősségi modellben leírtaknak megfelelően segíti a biztonsági kötelezettségek teljesítését. Ha többet szeretne megtudni arról, hogy a Microsoft hogyan teljesíti a szolgáltatói feladatokat, olvassa el a [megosztott felelősségek a felhőalapú számítástechnika](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91)terén című témakört.

A cikkben szereplő ajánlások némelyikét a Azure Security Center automatikusan nyomon követheti. A Azure Security Center az Azure-beli erőforrások védelmének első védelmi vonala. Rendszeresen elemzi az Azure-erőforrások biztonsági állapotát az esetleges biztonsági rések azonosítása érdekében. Ezután javaslatokat tesz a megoldására.

- Azure Security Center javaslatokkal kapcsolatos további információkért lásd: [biztonsági javaslatok a Azure Security Centerban](../security-center/security-center-recommendations.md).
- További információ az Azure Security Centerről: [What is Azure Security Center?](../security-center/security-center-introduction.md)

## <a name="general"></a>Általános kérdések

| Ajánlás | Megjegyzések | Az ASC támogatja |
|-|----|--|
| Naprakész marad | A támogatott platformok, programozási nyelvek, protokollok és keretrendszerek legújabb verzióit használhatja. | - |
| Hitelesítő kulcsok biztonságos megőrzése | Az eszköz azonosítóit és a hitelesítő kulcsokat a telepítés után fizikailag biztonságban tartsa. Ezzel a megoldással elkerülhető, hogy egy rosszindulatú eszköz regisztrálva legyen regisztrált eszközként. | - |
| Az eszköz SDK-k használata, ha lehetséges | Az eszköz SDK-k számos különböző biztonsági funkciót implementálnak, például a titkosítást, a hitelesítést és így tovább, hogy segítséget nyújtson a robusztus és biztonságos eszközök alkalmazásának fejlesztéséhez. További információt az [Azure IoT hub SDK-k megismerése és használata](../iot-hub/iot-hub-devguide-sdks.md) című témakörben talál. | - |

## <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés 

| Ajánlás | Megjegyzések | Az ASC támogatja |
|-|----|--|
| A hub hozzáférés-vezérlésének meghatározása | [Ismerje meg és határozza meg az egyes összetevők hozzáférésének típusát](iot-security-deployment.md#securing-the-cloud) a IoT hub-megoldásban a funkció alapján. Az engedélyezett engedélyek a *Registry Read*, a *RegistryReadWrite*, a *ServiceConnect* és a *DeviceConnect*. Az [IoT hub alapértelmezett megosztott elérési szabályzatai](../iot-hub/iot-hub-devguide-security.md#access-control-and-permissions) az egyes összetevők engedélyeinek definiálásához is segítséget nyújtanak a szerepkörük alapján. | - |
| A háttér-szolgáltatások hozzáférés-vezérlésének meghatározása | A IoT Hub-megoldás által betöltött adatmennyiség más Azure-szolgáltatások, például [Cosmos db](../cosmos-db/index.yml), [stream Analytics](../stream-analytics/index.yml), [app Service](../app-service/index.yml), [Logic apps](../logic-apps/index.yml)és [blob Storage](../storage/blobs/storage-blobs-introduction.md)használatával is felhasználható. Ügyeljen rá, hogy megértse és engedélyezze a megfelelő hozzáférési engedélyeket a szolgáltatások dokumentációjában. | - |

## <a name="data-protection"></a>Adatvédelem

| Ajánlás | Megjegyzések | Az ASC támogatja |
|-|----|--|
| Biztonságos eszköz hitelesítése | Gondoskodjon arról, hogy az eszközök és az IoT hub biztonságos kommunikációt használjon [egyedi azonosító kulccsal vagy biztonsági jogkivonattal](iot-security-deployment.md#iot-hub-security-tokens), vagy [egy eszköz X. 509 tanúsítvánnyal](iot-security-deployment.md#x509-certificate-based-device-authentication) az egyes eszközökhöz. Használja a megfelelő módszert a [biztonsági jogkivonatok használatára a választott protokoll alapján (MQTT, AMQP vagy https)](../iot-hub/iot-hub-devguide-security.md). | - |
| Biztonságos eszközök kommunikációja | A IoT Hub a Transport Layer Security (TLS) szabvány, a 1,2-es és a 1,0-es verziókat támogató eszközök közötti kapcsolatokat biztosítja. A maximális biztonság érdekében a [TLS 1,2](https://tools.ietf.org/html/rfc5246) -et használja. | - |
| Biztonságos szolgáltatás kommunikációja | A IoT Hub végpontokat biztosít a háttér-szolgáltatásokhoz, például az [Azure Storage](../storage/index.yml) -hoz vagy a [Event HUBS](../event-hubs/index.yml) csak a TLS protokollal való kapcsolódáshoz, és a végpontok nem titkosított csatornán vannak kitéve. Ha ezek az adatok elérik ezeket a háttér-szolgáltatásokat a tároláshoz vagy az elemzéshez, ügyeljen arra, hogy a szolgáltatáshoz megfelelő biztonsági és titkosítási módszereket alkalmazzon, és megvédje a háttérbeli bizalmas adatokat. | - |

## <a name="networking"></a>Hálózatkezelés

| Ajánlás | Megjegyzések | Az ASC támogatja |
|-|----|--|
| Az eszközökhöz való hozzáférés biztosítása | A nemkívánatos hozzáférés elkerülése érdekében tartsa meg a hardveres portokat az eszközökön. Emellett az eszköz fizikai illetéktelen módosításának megakadályozására vagy észlelésére szolgáló mechanizmusokat is létrehozhat. A részletekért olvassa el a [IoT biztonsági ajánlott eljárásait](iot-security-best-practices.md) . | - |
| Biztonságos hardver létrehozása | Az eszközök és az infrastruktúra biztonságosabbá tételéhez olyan biztonsági funkciókat kell beépíteni, mint például a titkosított tárolás vagy a platformmegbízhatósági modul (TPM). Tartsa meg az eszköz operációs rendszerét és illesztőprogramját a legújabb verzióra, és ha a tárhely lehetővé teszi, telepítse a víruskereső és az antimalware funkciókat. Olvassa el a [IoT biztonsági architektúráját](iot-security-architecture.md) , hogy megtudja, hogyan segíthet enyhíteni a biztonsági fenyegetéseket. | - |

## <a name="monitoring"></a>Figyelés

| Ajánlás | Megjegyzések | Az ASC támogatja |
|-|----|--|
| Jogosulatlan hozzáférés figyelése az eszközökhöz |  Az eszköz operációs rendszerének naplózási funkciója segítségével figyelheti az eszköz vagy a portok biztonsági megsértését vagy fizikai illetéktelen módosítását. | - |
| A IoT-megoldás figyelése a felhőből | A IoT Hub-megoldás általános állapotát a [Azure monitor metrikáinak](../iot-hub/monitor-iot-hub.md)használatával figyelheti. | - |
| Diagnosztika beállítása | Részletesen tekintse meg a műveleteit a megoldásban lévő események naplózásával, majd küldje el a diagnosztikai naplókat a Azure Monitor a teljesítmény láthatóságának megismeréséhez. További információért olvassa el a [figyelés és a problémák diagnosztizálása az IoT hub-ban](../iot-hub/monitor-iot-hub.md) című témakört. | - |

## <a name="next-steps"></a>Következő lépések

Az Azure IoT-t érintő speciális forgatókönyvek esetében előfordulhat, hogy további biztonsági követelményeket kell figyelembe vennie. További útmutatásért lásd: [IoT biztonsági architektúra](iot-security-architecture.md) .