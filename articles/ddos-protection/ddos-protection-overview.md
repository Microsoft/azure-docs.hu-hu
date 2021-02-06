---
title: Azure DDoS Protection standard áttekintése
description: Ismerje meg, hogy az Azure DDoS Protection Standard a DDoS-támadások elleni védelem érdekében hogyan kombinálható az alkalmazástervezés ajánlott eljárásaival.
services: virtual-network
documentationcenter: na
author: yitoh
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/9/2020
ms.author: yitoh
ms.openlocfilehash: 71eada0dcd7f7684e6c6be935bcb44405eb95322
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2021
ms.locfileid: "99626206"
---
# <a name="azure-ddos-protection-standard-overview"></a>Az Azure DDoS Protection Standard áttekintése

Az elosztott szolgáltatásmegtagadásos (DDoS-) támadások az egyik legnagyobb rendelkezésreállási és biztonsági kockázatot jelentik az olyan felhasználók számára, akik alkalmazásaikat a felhőbe helyezik át. A DDoS-támadás megpróbál kimeríteni egy alkalmazás erőforrásait, így az alkalmazás nem érhető el a legitim felhasználók számára. A DDoS-támadások bármilyen, az interneten keresztül nyilvánosan elérhető végpontot megcélozhatnak.

Az Azure minden tulajdonságát az Azure infrastruktúrájának DDoS (alapszintű) védelme védi, többletköltség nélkül. A globálisan üzembe helyezett Azure-hálózat skálázása és kapacitása védelmet nyújt a közös hálózati rétegbeli támadásokkal szemben a folyamatos forgalom monitorozása és a valós idejű mérséklés révén. DDoS Protection alapszintű beállításhoz nincs szükség felhasználói konfigurációra vagy alkalmazás módosítására. DDoS Protection alapszintű, így biztosítható az összes Azure-szolgáltatás, beleértve a Pásti-szolgáltatásokat, mint a Azure DNS.

A standard szintű, az alkalmazások kialakításával kapcsolatos ajánlott eljárásokkal kombinálva a DDoS-támadások elleni védelem érdekében fejlett DDoS-elhárítási funkciókat biztosít. Azure DDoS Protection A rendszer automatikusan hangolja az adott Azure-erőforrások védelmére egy virtuális hálózaton. A védelem egyszerűen engedélyezhető bármely új vagy meglévő virtuális hálózaton, és nem igényel alkalmazás-vagy erőforrás-módosítást. Az alapszintű szolgáltatás számos előnnyel jár, beleértve a naplózást, a riasztásokat és a telemetria is. 

![Azure DDoS Protection szolgáltatás összehasonlítása](./media/ddos-protection-overview/ddos-comparison.png)

Az Azure DDoS Protection nem tárolja az ügyféladatokat.

## <a name="features"></a>Funkciók

- **Natív platform-integráció:** Natív módon integrálva az Azure-ba. A Azure Portal konfigurációját tartalmazza. A DDoS Protection standard értelmezése az erőforrásokat és az erőforrás-konfigurációt.
- **Kulcsrakész védelem:** Az egyszerűsített konfiguráció azonnal megvédi a virtuális hálózat összes erőforrását, amint DDoS Protection standard engedélyezve van. Nincs szükség beavatkozásra vagy felhasználói definícióra. 
- **Folyamatos forgalom figyelése:** Az alkalmazás forgalmi mintáit napi 24 órában, hetente 7 nappal figyelik, a DDoS-támadások mutatóit keresve. A rendszer azonnal DDoS Protection a standardot, és automatikusan csökkenti a támadásokat, amint azt észlelte.
- **Adaptív hangolás:** Az intelligens forgalmi profilkészítés az idő múlásával megtanulja az alkalmazás forgalmát, és kiválasztja és frissíti a szolgáltatás számára legmegfelelőbb profilt. A profil a forgalmi változások időbeli változásával változik.
- **Többrétegű védelem:** A webalkalmazási tűzfallal (WAF) való üzembe helyezéskor a DDoS Protection standard védelmet biztosít mind a hálózati rétegben (3. és 4. réteg, Azure DDoS Protection standard által kínált), valamint az alkalmazás rétegében (7. réteg, amelyet egy WAF kínál). A WAF-ajánlatok közé tartoznak az Azure [Application Gateway WAF SKU](../web-application-firewall/ag/ag-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , valamint az [Azure piactéren](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall)elérhető, harmadik féltől származó webalkalmazási tűzfalak is.
- **Széleskörű kockázatcsökkentő skála:** Több mint 60 különböző támadási típust lehet mérsékelni, globális kapacitással, hogy védelmet nyújtson a legnagyobb ismert DDoS-támadásokkal szemben.
- **Támadási elemzés:** A támadás során öt percenkénti lépésekben részletes jelentéseket kaphat, és a támadás befejezése után teljes összefoglalás is elérhető. A stream-elhárítási folyamat naplóit az [Azure Sentinel](../sentinel/connect-azure-ddos-protection.md) vagy egy offline biztonsági információ-és ESEMÉNYKEZELŐ (SIEM) rendszer ismerteti a támadás során a közel valós idejű figyeléshez.
- **Támadási metrikák:** Az egyes támadások összesített metrikái a Azure Monitoron keresztül érhetők el.
- **Támadási riasztások:** A riasztások a támadás indításakor és leállításakor, valamint a támadás időtartamán keresztül konfigurálhatók a beépített támadási metrikák használatával. A riasztások integrálva vannak az operatív szoftverbe, például Microsoft Azure a monitorozási naplók, a splunk, az Azure Storage, az E-mail és a Azure Portal.
- **DDoS gyors válasz**: a DDoS Protection Rapid Response (DRR) csapatának segítségére van a támadás kivizsgálásához és elemzéséhez. További információ: [DDoS gyors válasz](ddos-rapid-response.md).
- **Cost garancia:** Az adatátviteli és az alkalmazás-kibővített szolgáltatási kreditek fogadása a DDoS-támadások miatt felmerülő erőforrás-költségekért.

## <a name="pricing"></a>Díjszabás

A DDoS Protection-csomagok havi $2 944-os fix havi díja, amely akár 100 nyilvános IP-címet is magában foglalhat. A további erőforrások védelme havonta további $30 lesz.

A bérlők egy DDoS Protection-tervet is használhatnak több előfizetésben, így nincs szükség több DDoS Protection-csomag létrehozására.

A standard díjszabással kapcsolatos további információkért tekintse meg a [Azure DDoS Protection standard díjszabást](https://azure.microsoft.com/pricing/details/ddos-protection/)Azure DDoS Protection.

## <a name="reference-architectures"></a>Referenciaarchitektúrák

DDoS Protection standard a [virtuális hálózatban üzembe helyezett szolgáltatásokhoz](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)lett tervezve. Más szolgáltatások esetében az alapértelmezett DDoS Protection alapszintű szolgáltatás érvényes. A támogatott architektúrákkal kapcsolatos további tudnivalókért tekintse meg a [DDoS Protection hivatkozási architektúrák](https://docs.microsoft.com/azure/ddos-protection/ddos-protection-reference-architectures)című témakört. 

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [DDoS Protection terv létrehozása](manage-ddos-protection.md)
