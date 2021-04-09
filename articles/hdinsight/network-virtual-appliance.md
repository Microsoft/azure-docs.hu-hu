---
title: Hálózati virtuális berendezés konfigurálása az Azure HDInsight
description: Megtudhatja, hogyan konfigurálhat számos további funkciót a hálózati virtuális berendezéshez az Azure HDInsight-ben.
ms.service: hdinsight
ms.topic: how-to
ms.date: 06/30/2020
ms.openlocfilehash: 7fe266c3c7b75762133fca4645e0675845c28972
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98943967"
---
# <a name="configure-network-virtual-appliance-in-azure-hdinsight"></a>Hálózati virtuális berendezés konfigurálása az Azure HDInsight

> [!Important]
> A következő információkra **csak** akkor van szükség, ha nem [Azure Firewall](./hdinsight-restrict-outbound-traffic.md)hálózati virtuális berendezést (NVA) szeretne konfigurálni.

Azure Firewall FQDN címkét a rendszer automatikusan úgy konfigurálja, hogy a nagy számú fontos teljes tartománynév esetében engedélyezze a forgalmat. Egy másik hálózati virtuális készülék használata több további funkció konfigurálását is megköveteli. A hálózati virtuális berendezés konfigurálása során tartsa szem előtt a következő tényezőket:

* A szolgáltatás-végponttal kompatibilis szolgáltatások olyan szolgáltatási végpontokkal konfigurálhatók, amelyek megkerülik a NVA, általában a költségeket vagy a teljesítménnyel kapcsolatos megfontolásokat.
* Ha a ResourceProviderConnection a *kimenő* értékre van állítva, használhat privát végpontokat a metaadattárak tárolóhoz és az SQL-kiszolgálókhoz, és nem kell őket hozzáadnia a NVA.
* Az IP-címek függőségei nem HTTP/S forgalomra vonatkoznak (TCP-és UDP-forgalom).
* Az FQDN HTTP/HTTPS-végpontok jóváhagyható a NVA-eszközön.
* Rendelje hozzá a HDInsight-alhálózathoz létrehozott útválasztási táblázatot.

## <a name="service-endpoint-capable-dependencies"></a>Szolgáltatási végpontok számára alkalmas függőségek

A következő szolgáltatási végpontok közül egyet vagy többet is engedélyezhet, amelyek a NVA megkerülését eredményezik. Ez a lehetőség nagy mennyiségű adatátvitelhez hasznos, ha a költségeket és a teljesítmény-optimalizálást is lehetővé teszi. 

| **Végpont** |
|---|
| Azure SQL |
| Azure Storage |
| Azure Active Directory |

### <a name="ip-address-dependencies"></a>IP-címek függőségei

| **Végpont** | **Részletek** |
|---|---|
| [Itt](hdinsight-management-ip-addresses.md) közzétett IP-címek | Ezek az IP-címek a HDInsight erőforrás-szolgáltatóhoz tartoznak, és a UDR kell szerepelniük az aszimmetrikus útválasztás elkerüléséhez. Ez a szabály csak akkor szükséges, ha a ResourceProviderConnection *bejövő* értékre van beállítva. Ha a ResourceProviderConnection *kimenő* értékre van állítva, akkor ezek az IP-címek nem szükségesek a UDR.  |
| HRE – DS magánhálózati IP-címek | Csak ESP-fürtök esetén szükséges, ha a virtuális hálózatok nincsenek összevonásban.|


### <a name="fqdn-httphttps-dependencies"></a>FQDN HTTP/HTTPS-függőségek

A NVA ebben a tárházban való konfigurálásához az FQDN [-](https://github.com/Azure-Samples/hdinsight-fqdn-lists/)függőségek (elsősorban az Azure Storage és a Azure Service Bus) listáját szerezheti be. A regionális listáról [itt](https://github.com/Azure-Samples/hdinsight-fqdn-lists/tree/master/Regional)talál további információt. Ezeket a függőségeket a HDInsight erőforrás-szolgáltató (RP) használja a fürtök sikeres létrehozásához és figyeléséhez/kezeléséhez. Ilyenek például a telemetria/diagnosztikai naplók, a metaadatok kiépítése, a fürthöz kapcsolódó konfigurációk, parancsfájlok stb. Ez a teljes tartománynév-függőségi lista változhat a későbbi HDInsight-frissítések felszabadításával.

Az alábbi lista csak néhány teljes tartománynevet ad meg, amelyekre szükség lehet az operációs rendszer és a biztonsági javítás vagy a tanúsítvány érvényessége *után* a fürt létrehozása és a fürt műveleteinek élettartama során:

| **Futásidejű függőségek teljes tartományneve**                                                          |
|---|
| azure.archive.ubuntu.com:80                                           |
| security.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |
| microsoft.com/pki/mscorp/cps/default.htm:443                                      |
| microsoft.com:80                                                      |
|login.windows.net:443                                                  |
|login.microsoftonline.com:443                                          |

## <a name="next-steps"></a>Következő lépések

* [Tűzfal használata a kimenő forgalom korlátozására](./hdinsight-restrict-outbound-traffic.md)
* [Azure HDInsight virtuális hálózati architektúra](hdinsight-virtual-network-architecture.md)
