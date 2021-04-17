---
title: Mi az Az Oracle BareMetal-infrastruktúrája?
description: Ismerje meg az Oracle számítási feladatokhoz elérhető BareMetal Infrastructure-szolgáltatásokat.
ms.topic: conceptual
ms.subservice: workloads
ms.date: 04/14/2021
ms.openlocfilehash: efb7506a0fa98ca93390bfe7d3cb1401cdbb77e2
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559159"
---
# <a name="what-is-baremetal-infrastructure-for-oracle"></a>Mi az Az Oracle BareMetal-infrastruktúrája?

Ez a cikk áttekintést nyújt az BareMetal Infrastructure Oracle számítási feladatokhoz elérhető szolgáltatásairól.

Az BareMetal Infrastructure for Oracle az Oracle által tanúsított unified Computing System (UCS) és az FLexPod rendszeren alapul. A FlexPod platform előre ellenőrzött tárolási, hálózati és kiszolgálói technológiákat biztosít. NfS-tárolót kínál, amely DirectNFS protokollal biztosítja az integrációt. Az BareMetal-kiszolgálókat a rendszer dedikálta Önnek, és nem rendelkezik hipervizorral az BareMetal-példányon. 

Ezek a példányok oracle számítási feladatot igénylő kritikus fontosságú alkalmazások futtatására használhatók. Az BareMetal-példányok alacsony késést (0,35 ms) biztosítanak az Azure-beli virtuális gépeken futó alkalmazások számára. Az BareMetal megosztott tárolólemezt biztosít, és támogatja a csomópontok közötti kommunikációhoz szükséges többválogatásos küldést egy dedikált privát összekapcsolási hálózattal. 

Az Oracle-hez elérhető BareMetal-infrastruktúra egyéb funkciói a következők:

- Oracle-tanúsítvánnyal rendelkező UCS-panelek – UCSB200-M5, UCSB460-M4, UCSB480-M5
- Oracle Real Application Clusters ( RAC) node-to-node (multi-cast) communication using private virtual LAN (VLAN) -40 Gb.
- A Microsoft által felügyelt hardverek
  - Redundáns tárolás, hálózat, energiaellátás, felügyelet
  - Infrastruktúra monitorozása, javítás és csere
  - Tartalmazza Azure ExpressRoute ügyfél tartományvezérlője számára
  - Biztonságos fizikai és hálózati biztonság, az összes Azure-felhőszolgáltatáshoz hozzáférhet

### <a name="supported-protocols"></a>Támogatott protokollok

Az Alábbi protokollok különböző csatlakoztatási pontokhoz használhatók az BareMetal-kiszolgálókon belül az Oracle számítási feladatokhoz.

- Operációs rendszer csatlakoztatása – iSCSI
- Adatok/napló – NFSv3
- biztonsági mentés/archiválás – NFSv4

### <a name="licensing"></a>Licencek

- Saját helyszíni operációs rendszert és Oracle-licenceket is hozhat.

### <a name="operating-system"></a>Operációs rendszer

A kiszolgálók előre be vannak töltve az RHEL 7.6 operációs rendszerrel.

## <a name="next-steps"></a>Következő lépések

Ismerje meg az Oracle BareMetal számítási feladatok SKUs-ját.

> [!div class="nextstepaction"]
> [BareMetal SKUs Oracle számítási feladatokhoz](oracle-baremetal-skus.md)
