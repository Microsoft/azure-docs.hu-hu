---
title: Az Azure Percept biztonsági áttekintése
description: További információ az Azure Percept biztonságáról
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: conceptual
ms.date: 03/24/2021
ms.custom: template-concept
ms.openlocfilehash: 93884fb87f87651054ffff0a04c4910de634a5eb
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105567644"
---
# <a name="azure-percept-security-overview"></a>Az Azure Percept biztonsági áttekintése

Az Azure Percept-eszközöket a megbízható hardver gyökerével tervezték. Ez a beépített biztonság segít az adatok és az adatvédelemre érzékeny érzékelők, például a kamerák és a mikrofonok védelmében, és lehetővé teszi az eszközök hitelesítését és engedélyezését az Azure Percept Studio-szolgáltatásokhoz.

> [!NOTE]
> Az Azure Percept DK kizárólag fejlesztési és tesztelési környezetekben használható.

## <a name="devices"></a>Eszközök

### <a name="azure-percept-dk"></a>Azure Percept DK

Az Azure Percept DK tartalmaz egy platformmegbízhatósági modul (TPM) 2,0-es verziót, amely az eszközt az Azure-eszközök kiépítési szolgáltatásaihoz (DPS) való csatlakoztatásra is felhasználhatja további biztonsággal. A TPM a Trusted Computing Group iparági szintű, ISO szabványa. Tekintse meg a [Trusted Computing Group webhelyét](https://trustedcomputinggroup.org/resource/tpm-library-specification/) , ahol további információkat talál a teljes TPM 2,0 specifikációval vagy az ISO/IEC 11889 specifikációval kapcsolatban. További információ arról, hogy a DPS hogyan tudja biztonságos módon kiépíteni az eszközöket, lásd: [Azure IoT hub Device Provisioning Service – TPM-igazolás](../iot-dps/concepts-tpm-attestation.md).

### <a name="azure-percept-system-on-modules-soms"></a>Azure Percept System-on-modules (SoMs)

Az Azure Percept látási rendszer-modul (SoM) és az Azure Percept audio SoM egyaránt tartalmaz egy mikrovezérlő-egységet (MCU) a beágyazott AI-érzékelők elérésének védelméhez. Az MCU belső vezérlőprogram minden rendszerindításkor hitelesíti és engedélyezi az AI-gyorsító használatát az Azure Percept Studio Services használatával az eszköz-azonosító összeállítási motor (DICE) architektúrával. A DICE úgy működik, hogy elvégzi a rendszerindítást a rétegekre, és létrehozza az egyedi frissítési-titkokat az egyes rétegekhez és konfigurációkhoz. Ha a lánc bármely pontján a kód vagy a konfiguráció eltérő, a titkos kulcsok eltérőek lesznek. A DICE szolgáltatásról a [Dice munkacsoport specifikációjában](https://trustedcomputinggroup.org/work-groups/dice-architectures/)olvashat bővebben. Az Azure Percept studióhoz és a szükséges szolgáltatásokhoz való hozzáférés konfigurálásához tekintse meg az [Azure PERCEPT DK tűzfalak konfigurálását](concept-security-configuration.md)ismertető cikket.

Az Azure Percept-eszközök a megbízhatóság gyökerét használják a belső vezérlőprogram biztonságossá tételéhez. A rendszerindító ROM biztosítja a belső vezérlőprogram integritását a ROM és az operációs rendszer (OS) betöltője között, amely a többi szoftver-összetevő integritását biztosítja, és létrehoz egy megbízhatósági láncot.

## <a name="services"></a>Szolgáltatások

### <a name="iot-edge"></a>IoT Edge

Az Azure Percept DK a Transport Layer Security (TLS) protokollt használó további biztonsági és egyéb Azure-szolgáltatásokkal csatlakozik az Azure Percept studióhoz. Az Azure Percept DK egy Azure IoT Edge-kompatibilis eszköz. A IoT Edge Runtime olyan programok gyűjteménye, amelyek egy eszközt IoT Edge eszközre kapcsolnak be. Együttesen a IoT Edge futtatókörnyezet-összetevők lehetővé teszik, hogy IoT Edge eszközök megkapják a kód futtatását a peremhálózat szélén, és közlik az eredményeket. Az Azure Percept DK Docker-tárolókat használ a IoT Edge munkaterhelések elkülönítéséhez a gazda operációs rendszertől és az Edge-kompatibilis alkalmazásokból. A Azure IoT Edge biztonsági keretrendszerével kapcsolatos további információkért olvassa el a [IoT Edge Security Manager](../iot-edge/iot-edge-security-manager.md)című témakört.

### <a name="device-update-for-iot-hub"></a>IoT Hub eszköz frissítése

A IoT Hub eszköz frissítése biztonságosabb, méretezhető és megbízható, az Azure Percept-eszközökre épülő, megújítható biztonságot biztosító frissítést tesz lehetővé. Részletes felügyeleti felügyeletet biztosít, és a megfelelőséget az információkkal frissíti. Az Azure Percept DK olyan előre integrált eszköz-frissítési megoldást tartalmaz, amely rugalmas frissítést (A/B) biztosít a belső vezérlőprogram és az operációs rendszer rétegei számára.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [További információ a tűzfal-konfigurációkról és a biztonsági javaslatokról](concept-security-configuration.md)

> [!div class="nextstepaction"]
> [Azure Percept DK vásárlása a Microsoft online áruházból](https://go.microsoft.com/fwlink/p/?LinkId=2155270)
