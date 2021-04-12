---
title: Tudnivalók a riasztási üzenetekről
description: A részletek áttekintéséhez válasszon ki egy riasztást a riasztások ablakból.
ms.date: 3/21/2021
ms.topic: how-to
ms.openlocfilehash: 2fa2b265c7d3983ca6ae2d7507392dd37afabd27
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104781483"
---
# <a name="about-alert-messages"></a>Tudnivalók a riasztási üzenetekről

A riasztások részleteinek áttekintéséhez válasszon ki egy riasztást **a riasztások ablakból** . A részletek a következő információkat tartalmazzák:

- Riasztási metaadatok

- Információk a forgalomról, az eszközökről és az eseményről

- A csatlakoztatott eszközökre mutató hivatkozások az eszköz térképén

- A biztonsági elemzők és a rendszergazdák által meghatározott megjegyzések

- Javaslatok az esemény kivizsgálásához

## <a name="alert-metadata"></a>Riasztási metaadatok

A riasztás részletei a következő riasztási metaadatokat tartalmazzák:

  - Riasztás azonosítója

  - A riasztást kiváltó házirend-motor

  - A riasztás indításának dátuma és időpontja

:::image type="content" source="media/how-to-work-with-alerts-sensor/internet-connectivity-detection-unauthorized.png" alt-text="A rendszer jogosulatlan internetkapcsolatot észlelt.":::

## <a name="information-about-devices-traffic-and-the-event"></a>Az eszközökre, a forgalomra és az eseményre vonatkozó információk

A riasztási üzenet a következő információkat tartalmazza:

  - Az észlelt eszközök.

  - Az eszközök, például a protokollok és a függvények kódjai között észlelt forgalom.

  - Betekintést nyerhet az esemény következményeire.

Ezt az információt a riasztási esemény kezelésének eldöntése során használhatja fel.

## <a name="links-to-connected-devices-in-the-device-map"></a>A csatlakoztatott eszközökre mutató hivatkozások az eszköz térképén

Ha többet szeretne megtudni az észlelt eszközökhöz csatlakozó eszközökről, a riasztásban kiválaszthatja az eszköz rendszerképét, és megtekintheti a csatlakoztatott eszközöket a térképen.

:::image type="content" source="media/how-to-work-with-alerts-sensor/rcp-operation-failed.png" alt-text="Az RCP művelet sikertelen volt.":::

:::image type="content" source="media/how-to-work-with-alerts-sensor/devices-screen-populated.png" alt-text="Eszközök képernyőfelvétele":::

A Térkép szűrőit a kiválasztott eszközre, és más csatlakoztatott eszközökre. A Térkép a riasztásokban észlelt eszközökhöz tartozó **gyors tulajdonságok** párbeszédpanelt is megjeleníti.

## <a name="comments-defined-by-security-analysts-and-administrators"></a>A biztonsági elemzők és a rendszergazdák által meghatározott megjegyzések 

A riasztások tartalmazhatják az előre meghatározott megjegyzések listáját. A megjegyzésekben például megadható a kockázatcsökkentő műveletek elvégzéséhez szükséges utasítások, illetve az eseményre vonatkozó személyek nevei.

Riasztási esemény kezelésekor kiválaszthatja azokat a megjegyzéseket vagy megjegyzéseket, amelyek a legjobban tükrözik az esemény állapotát vagy a riasztás kivizsgálásához szükséges lépéseket.

A kiválasztott megjegyzések a riasztási üzenetben lesznek mentve. A megjegyzések használata javítja az egyének és a csapatok közötti kommunikációt egy riasztási esemény vizsgálata során. Ennek eredményeképpen a megjegyzések felgyorsítják az incidensek időpontját.

A rendszergazda vagy a biztonsági elemző előre definiálja a megjegyzéseket. A kiválasztott megjegyzések nem továbbítódnak a továbbítási szabályokban definiált partneri rendszerekhez.

Ha áttekinti a riasztásban szereplő információkat, különböző törvényszéki lépéseket hajthat végre a riasztási esemény kezelésének megkezdéséhez. Például:

- Az eszköz legutóbbi tevékenységének (adatbányászati jelentés) elemzése. 

- Elemezheti az egy időben történt egyéb eseményeket (az esemény idővonalát). 

- Átfogó esemény-adatforgalom elemzése (PCAP-fájl).

## <a name="pcap-files"></a>PCAP-fájlok

Bizonyos esetekben a riasztási üzenetből hozzáférhet egy PCAP-fájlhoz. Ez akkor lehet hasznos, ha részletesebb információra van szüksége a kapcsolódó hálózati forgalomról.

PCAP-fájl letöltéséhez válassza a :::image type="content" source="media/how-to-work-with-alerts-sensor/download-pcap.png" alt-text="Letöltés ikont."::: a **riasztás részletei** párbeszédpanel jobb felső sarkában.

## <a name="recommendations-for-investigating-an-event"></a>Az események kivizsgálására vonatkozó javaslatok 

A riasztások **javaslati** területe olyan információkat jelenít meg, amelyek segíthetnek az események jobb megismerésében. Tekintse át ezeket az információkat a riasztási esemény kezelése vagy az eszközön vagy a hálózaton való beavatkozás előtt.

## <a name="see-also"></a>Lásd még

[Riasztási munkafolyamatok felgyorsítása](how-to-accelerate-alert-incident-response.md)

[A riasztási esemény kezelése](how-to-manage-the-alert-event.md)
