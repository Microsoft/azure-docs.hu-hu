---
title: Az Azure Percept biztonsági áttekintése
description: További információ az Azure Percept biztonságáról
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/18/2021
ms.custom: template-concept
ms.openlocfilehash: b5a345139114842c83cb1f11792076efb1461870
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101663713"
---
# <a name="azure-percept-security-overview"></a>Az Azure Percept biztonsági áttekintése

Az Azure Percept DK-eszközök a megbízható hardver gyökerével vannak kialakítva – a beépített biztonság minden eszközön. Megvédi az adatvédelmet, például a kamerákat és a mikrofonokat, az adatokat, és lehetővé teszi az eszközök hitelesítését és engedélyezését az Azure Percept Studio-szolgáltatásokhoz.

> [!NOTE]
> Az Azure Percept DK fejlesztési és tesztelési környezetekben, valamint a megvalósíthatósági forgatókönyvekben használható.

## <a name="devices"></a>Eszközök

### <a name="azure-percept-dk"></a>Azure Percept DK

Az Azure Percept DK tartalmaz egy platformmegbízhatósági modul (TPM) 2,0-es verziót, amely az eszköz biztonságos csatlakoztatására használható az Azure Device kiépítési szolgáltatásokhoz. A TPM a Trusted Computing Group iparági szintű, ISO szabványa, és a TPM a [teljes tpm 2,0 specifikációban](https://trustedcomputinggroup.org/resource/tpm-library-specification/) , illetve az ISO/IEC 11889 specifikációban is olvasható. További információ arról, hogy a DPS hogyan tudja biztonságos módon kiépíteni az eszközöket: [Azure IoT hub Device Provisioning Service – TPM-igazolás](https://docs.microsoft.com/azure/iot-dps/concepts-tpm-attestation).

### <a name="azure-percept-system-on-module-som"></a>Azure Percept rendszer a modulon (SOM)

Az Azure Percept DK-alapú, a modulon (SOM) és az Azure Percept audio kiegészítő SOM-on keresztüli, a beágyazott AI-érzékelőkhöz való hozzáférés védelmét biztosító Micro Controller Unit (MCU) is használható. Az MCU belső vezérlőprogram minden rendszerindításkor hitelesíti és engedélyezi az AI-gyorsító használatát az Azure Percept Studio Services használatával az eszköz-azonosító összeállítási motor (DICE) architektúrával. A DICE úgy működik, hogy elvégzi a rendszerindítást a rétegek között, és az egyes rétegekhez és konfigurációkhoz egyedi titkos titkokat hoz létre, egy egyedi frissítési alapján. Ha más kód vagy konfiguráció van elindítva, a lánc bármely pontján a titkok eltérőek lesznek. A DICE szolgáltatásról a [Dice munkacsoport specifikációjában](https://trustedcomputinggroup.org/work-groups/dice-architectures/)olvashat bővebben. Az Azure Percept studióhoz és a szükséges szolgáltatásokhoz való hozzáférés konfigurálásához tekintse meg az alábbi, az **Azure PERCEPT DK tűzfalak konfigurálása** című szakaszt.

Az Azure Percept-eszközök a hardveres legfelső szintű megbízhatóságot használják a belső vezérlőprogram biztonságossá tételéhez. A rendszerindító ROM biztosítja a belső vezérlőprogram integritását a ROM-és az operációs rendszer (OS) betöltője között, ami pedig biztosítja az egyéb szoftveres összetevők integritását, amely megbízhatósági láncot hoz létre.

## <a name="services"></a>Szolgáltatások

### <a name="iot-edge"></a>IoT Edge

Az Azure Percept DK biztonságosan csatlakozik az Azure Percept studióhoz és más Azure-szolgáltatásokhoz, Transport Layer Security (TLS) protokollt használ. Az Azure Percept DK egy Azure IoT Edge engedélyezett eszköz. A IoT Edge Runtime olyan programok gyűjteménye, amelyek egy eszközt IoT Edge eszközre kapcsolnak be. Együttesen a IoT Edge futtatókörnyezet-összetevők lehetővé teszik, hogy IoT Edge eszközök megkapják a kód futtatását a peremhálózat szélén, és közlik az eredményeket. Az Azure Percept DK Docker-tárolókat használ a IoT Edge munkaterhelések elkülönítésére a gazda operációs rendszer és az Edge-kompatibilis alkalmazások számára. A Azure IoT Edge biztonsági keretrendszerével kapcsolatos további információkért olvassa el a [IoT Edge Security Manager](https://docs.microsoft.com/azure/iot-edge/iot-edge-security-manager?view=iotedge-2018-06)című témakört.

### <a name="device-update-for-iot-hub"></a>IoT Hub eszköz frissítése

A IoT Hub eszköz frissítése lehetővé teszi a biztonságos, méretezhető és megbízható, az Azure Percept-eszközökre épülő, megújítható biztonságot biztosító frissítést. Részletes felügyeleti felügyeletet biztosít, és a megfelelőséget az információkkal frissíti. Az Azure Percept DK olyan előre integrált eszköz-frissítési megoldást tartalmaz, amely rugalmas frissítést (A/B) biztosít a belső vezérlőprogram és az operációs rendszer rétegei számára.

<!---I think the below topics need to be somewhere else, (i.e. not on the main page)
--->

## <a name="configuring-firewalls-for-azure-percept-dk"></a>Tűzfalak konfigurálása az Azure Percept DK-hoz

Ha a hálózatkezelés beállítása megköveteli, hogy explicit módon engedélyezze az Azure Percept DK-eszközökről érkező kapcsolatokat, tekintse át a következő összetevőket.

Ez az ellenőrzőlista a tűzfalszabályok kiindulási pontja:

|URL-cím (* = helyettesítő karakter) |Kimenő TCP-portok|    Használat|
|-------------------|------------------|---------|
|*. auth.azureperceptdk.azure.net|   443|    Azure DK SOM-hitelesítés és-engedélyezés|
|*. auth.projectsantacruz.azure.net| 443|    Azure DK SOM-hitelesítés és-engedélyezés|

Emellett tekintse át az [Azure IOT Edge által használt kapcsolatok](https://docs.microsoft.com/azure/iot-edge/production-checklist?view=iotedge-2018-06#allow-connections-from-iot-edge-devices)listáját.

## <a name="additional-recommendations-for-deployment-to-production"></a>További javaslatok az éles környezetben történő üzembe helyezéshez

Az Azure Percept DK számos biztonsági képességgel rendelkezik a dobozból. A jelenlegi kiadásban szereplő hatékony biztonsági funkciók mellett a Microsoft a következő irányelveket is javasolja az éles környezetekben üzemelő példányok tervezésekor:

- Az eszköz erős fizikai védelme
- Az inaktív adatok titkosítása engedélyezve van
- Az eszköz testhelyzetének folyamatos figyelése és a riasztások gyors reagálásának megválaszolása
- Az eszközhöz hozzáféréssel rendelkező rendszergazdák számának korlátozása

## <a name="next-steps"></a>Következő lépések

Ismerje meg az elérhető [Azure PERCEPT AI-modelleket](./overview-ai-models.md).