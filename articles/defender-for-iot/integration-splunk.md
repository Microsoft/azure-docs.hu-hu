---
title: Tudnivalók a splunk-integrációról
description: Annak érdekében, hogy az OT-hálózatok biztonságának és rugalmasságának hiánya is megmaradjon, a IoT Defender a IoT, a IIoT és az ICS veszélyforrások figyelésére szolgáló alkalmazását fejlesztette ki a splunk, a Defender for IoT és a splunk közötti natív integrációt, amely lehetővé teszi az informatikai és biztonsági rendszerek egységes megközelítését.
ms.date: 1/4/2021
ms.topic: article
ms.openlocfilehash: 28bdc6deaac09d795c45460bb211126a105b80c8
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104785920"
---
# <a name="defender-for-iot-and-ics-threat-monitoring-application-for-splunk"></a>Defender a IoT és az ICS veszélyforrások figyeléséhez alkalmazás a splunk

A IoT Defender csökkenti a IIoT, az ICS és a SCADA kockázatokat a szabadalmaztatott, ICS-t támogató önképzési motorokkal, amelyek azonnali elemzéseket nyújtanak az INTERNETKAPCSOLATtal rendelkező eszközökről, a biztonsági rések és a fenyegetésekről a képóráknál kevesebb idő alatt, és nem támaszkodnak az ügynökökre, a szabályokra vagy aláírásokra, a speciális ismeretekre vagy a környezet

Annak érdekében, hogy az OT-hálózatok biztonságának és rugalmasságának hiánya is megmaradjon, a IoT Defender a IoT, a IIoT és az ICS veszélyforrások figyelésére szolgáló alkalmazását fejlesztette ki a splunk, a Defender for IoT és a splunk közötti natív integrációt, amely lehetővé teszi az informatikai és biztonsági rendszerek egységes megközelítését.

> [!Note]
> A CyberX mutató hivatkozások az Azure Defender for IoT szolgáltatásra vonatkoznak.

## <a name="about-the-splunk-application"></a>Tudnivalók a splunk alkalmazásról

Az alkalmazás többdimenziós láthatóságot biztosít a SOC-elemzőknek az ipari környezetekben üzembe helyezett, specializált és IIoT eszközökhöz, valamint az ICS-kompatibilis viselkedési elemzésekkel a gyanús vagy rendellenes viselkedés gyors észlelése érdekében. Az alkalmazás a vállalati SOC-n belül is lehetővé teszi az IT-és az incidens-válaszokat. Ez egy fontos fejlemény, amely a folyamatos konvergenciát, valamint az új IIoT-kezdeményezéseket, például az intelligens gépeket és a valós idejű intelligenciát támogatja.

A splunk-alkalmazás helyileg vagy felhőben is telepíthető. A Defender for IoT integrációja mindkét üzemelő példányt támogatja.

## <a name="about-the-integration"></a>Tudnivalók az integrációról

A Defender a natív alkalmazáson keresztüli IoT és splunk való integrációja lehetővé teszi a felhasználók számára a következőket:

- Csökkentse az iparági és a kritikus fontosságú infrastruktúra-szervezetek számára a számítógépes fenyegetések észlelését, kivizsgálását és működésének időtartamát.

- Valós idejű intelligenciát kaphat az OT érintő kockázatokról.

- Társítsa a Defendert a IoT-riasztásokhoz a splunk Enterprise biztonsági fenyegetések felderítési adattárakkal.

- Egy egyablakos üvegből figyelheti és reagálhat.

[:::image type="content" source="media/integration-splunk/splunk-mainpage-v2.png" alt-text="A splunk eszköz főoldala.":::](media/integration-splunk/splunk-mainpage-v2.png#lightbox)

:::image type="content" source="media/integration-splunk/alerts.png" alt-text="A riasztások lapja a splunk.":::

Az alkalmazás lehetővé teszi, hogy a splunk-rendszergazdák elemezzék a IoT Defender által küldött riasztásokat, és figyelje a teljes biztonsági telepítést, beleértve a következő adatokat is:

- Az öt analitikai motor közül melyik észlelte a riasztást.

- A riasztást létrehozó protokoll.

- A IoT-érzékelőhöz tartozó Defender generálta a riasztást.

- A riasztás súlyossági szintje.

- A kommunikáció forrása és célja.

## <a name="requirements"></a>Követelmények

### <a name="version-requirements"></a>A verzióra vonatkozó követelmények

A következő verziók a követelmények.

- Defender a IoT 2,4-es vagy újabb verziójára.

- A Splunkbase 11-ös vagy újabb verziója.

- A splunk Enterprise 7,2-es vagy újabb verziója.
  
## <a name="download-the-application"></a>Az alkalmazás letöltése

Töltse le a *splunk-alkalmazás CyberX-megosztási veszélyforrásának figyelését* a [Splunkbase](https://splunkbase.splunk.com/app/4313/).

## <a name="splunk-permission-requirements"></a>A splunk engedélyeire vonatkozó követelmények

A következő splunk engedély szükséges:

- Bármely *rendszergazda* felhasználói szerepkörrel rendelkező felhasználó.

## <a name="send-defender-for-iot-alerts-to-splunk"></a>IoT-riasztások küldése a splunk

A Defender for IoT-riasztások a biztonsági események széles köréről nyújtanak információt, többek között:

- Az elsajátított alaphálózati tevékenységtől való eltérések.

- Kártevő-észlelések.

- A gyanús működési változások alapján észlelt észlelések.

- Hálózati rendellenességek.

- Protokolloktól való eltérések a protokollok előírásai alapján.

:::image type="content" source="media/integration-splunk/address-scan.png" alt-text="Az észlelések képernyő.":::

A Defender for IoT beállítható úgy, hogy riasztásokat küldjön a splunk-kiszolgálónak, ahol a riasztási információk a splunk vállalati irányítópulton jelennek meg.

:::image type="content" source="media/integration-splunk/alerts-and-details.png" alt-text="Megtekintheti az összes riasztást és azok részleteit.":::

A rendszer a következő riasztási adatokat küldi el a splunk-kiszolgálónak.

- A riasztás dátuma és időpontja.

- A IoT motor Defender, amely a következő eseményt észlelte: protokoll megsértése, házirend megsértése, kártevő, rendellenesség vagy működési motor.

- A riasztás címe.

- A riasztási üzenet.

- A riasztás súlyossága: figyelmeztetés, kisebb, fő vagy kritikus.

- A forrás eszköz neve.

- A forrásoldali eszköz IP-címe.

- A cél eszköz neve.

- A cél eszköz IP-címe.

- A IoT platform IP-címének (gazdagépének) védelmezője.

- A IoT platform készülékhez tartozó Defender neve (forrás típusa).

A minta kimenete alább látható:

| Idő | Esemény |
|--|--|
| 7/23/15<br />9:28:31.000 PM | **Defender for IoT platform riasztás**: az eszközt egy PLC-parancs leállította<br /><br />**Típus**: működési szabálysértés <br /><br />**Súlyosság**: jelentős <br /><br />**Forrás neve**: my_device1 <br /><br />**Forrás IP-cím**: 192.168.110.131 <br /><br />**Cél neve**: my_device2<br /><br /> **Cél IP-cím**: 10.140.33.238 <br /><br />**Üzenet**: egy hálózati eszköz le lett állítva a stop PLC parancs használatával. Ez az eszköz csak a Start parancs elküldése után fog működni. a 192.168.110.131 a 10.140.33.238 (a Siemens S7-eszköz) leállította a PLC leállítási parancs használatával.<br /><br />**Gazdagép**: 192.168.90.43 <br /><br />**Forrás típusa**: Sensor_Agent |

## <a name="define-alert-forwarding-rules"></a>Riasztás-továbbítási szabályok definiálása

A Defender for IoT *forwarding Rules* használatával riasztási információkat küldhet a splunk-kiszolgálókra.

A riasztási szabályok testreszabására szolgáló beállítások a következők alapján érhetők el:

- A rendszer meghatározott protokollokat észlelt.

- Az esemény súlyossága.

- Az eseményeket észlelő IoT motor Defender.

Továbbítási szabály létrehozása:

1. Az érzékelő vagy a helyszíni felügyeleti konzol bal oldali ablaktábláján válassza a **továbbítás lehetőséget.**

    :::image type="content" source="media/integration-splunk/forwarding.png" alt-text="Válassza a kék gomb továbbítási riasztás létrehozása lehetőséget.":::

1. Válassza a **továbbítási szabályok létrehozása** lehetőséget. A **továbbítási szabály létrehozása** ablakban adja meg a szabály paramétereit.

    :::image type="content" source="media/integration-splunk/forwarding-rule.png" alt-text="Hozza létre a továbbítási szabály szabályait.":::

    | Paraméter | Leírás |
    |--|--|
    | **Név** | A továbbítási szabály neve. |
    | **Súlyosság kiválasztása** | Az előre megadott minimális biztonsági szint. Ha például a másodlagos elem van kiválasztva, a rendszer továbbítja a másodlagos riasztásokat és az ezen súlyossági szint feletti riasztásokat. |
    | **Protokollok** | Alapértelmezés szerint az összes protokoll ki van választva. Egy adott protokoll kiválasztásához válassza az **adott** lehetőséget, majd válassza ki azt a protokollt, amelyre ez a szabály vonatkozik. |
    | **Végrehajtók** | Alapértelmezés szerint a rendszer minden biztonsági motort érint. Egy adott biztonsági motor kiválasztásához, amelyhez ez a szabály vonatkozik, válassza az **adott** lehetőséget, majd válassza ki a motort. |
    | **Rendszerértesítések** | Az érzékelő online/offline állapotának továbbítása. Ez a beállítás csak akkor érhető el, ha bejelentkezett a központi kezelőbe. |

1. Ahhoz, hogy a Defender IoT küldjön az splunk, válassza a **művelet**, majd **a Küldés a splunk-kiszolgálóra** lehetőséget.

1. Adja meg a következő splunk paramétereket.

    :::image type="content" source="media/integration-splunk/parameters.png" alt-text="A képernyőn meg kell adni a splunk paramétereit.":::

    | Paraméter | Leírás |
    |--|--|
    | **Állomás** | Splunk-kiszolgáló címe |
    | **Port** | 8089 |
    | **Felhasználónév** | Splunk-kiszolgáló felhasználóneve |
    | **Jelszó** | Splunk-kiszolgáló jelszava |

1. **Küldés** kiválasztása

## <a name="next-steps"></a>Következő lépések

Tudnivalók a [riasztási információk továbbításáról](how-to-forward-alert-information-to-partners.md).
