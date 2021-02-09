---
title: Migrálás a Network Performance Monitorről a kapcsolódási figyelőbe
titleSuffix: Azure Network Watcher
description: Megtudhatja, hogyan telepítheti át a Network Performance Monitorról a kapcsolódási figyelőre.
services: network-watcher
documentationcenter: na
author: vinynigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: vinigam
ms.openlocfilehash: 0bb46c17ece9a38d9f1e10c79a4b026efa0ece4c
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2021
ms.locfileid: "99833796"
---
# <a name="migrate-to-connection-monitor-from-network-performance-monitor"></a>Migrálás a Network Performance Monitorről a kapcsolódási figyelőbe

> [!IMPORTANT]
> 2021. július 1-től nem lehet új teszteket felvenni egy meglévő munkaterületre, vagy új munkaterületet engedélyezni Network Performance Monitor. Továbbra is használhatja a 2021. július 1. előtt létrehozott teszteket. A szolgáltatás megszakadásának minimalizálásához a jelenlegi számítási feladatokhoz telepítse át a teszteket Network Performance Monitorról az Azure Network Watcher új, 2024. február 29. előtti kapcsolódási figyelője felé.

Network Performance Monitor (NPM) teszteit áttelepítheti az új, továbbfejlesztett kapcsolódási figyelőre egyetlen kattintással és nulla állásidővel. További információ az előnyökről: a [kapcsolódási figyelő](./connection-monitor-overview.md).


## <a name="key-points-to-note"></a>Jegyezze fel a legfontosabb pontokat

Az áttelepítés a következő eredményeket segíti elő:

* A helyszíni ügynökök és a tűzfalbeállítások ugyanúgy működnek, mint a. Nincs szükség módosításra. Log Analytics Azure-beli virtuális gépekre telepített ügynököket a Network Watcher bővítménnyel kell helyettesíteni.
* A meglévő tesztek le vannak képezve a Csatlakozáskezelő > a tesztelési csoport > tesztelési formátumára. A **Szerkesztés** lehetőség kiválasztásával megtekintheti és módosíthatja az új kapcsolat figyelője tulajdonságait, letöltheti a sablonokat, és elküldheti a sablont Azure Resource Manager használatával.
* Az ügynökök az Log Analytics munkaterületre és a metrikára is küldenek adatokat.
* Adatfigyelés:
   * **Log Analyticsban tárolt adatértékek**: az áttelepítés előtt az adatterület abban a munkaterületen marad, amelyben a NPM a NetworkMonitoring táblában van konfigurálva. Az áttelepítés után az adatelérési pont a NetworkMonitoring táblába kerül, és a ConnectionMonitor_CL tábla ugyanabban a munkaterületen található. Miután a tesztek le vannak tiltva a NPM-ben, az adattárolást csak a ConnectionMonitor_CL táblában tárolja a rendszer.
   * **Napló alapú riasztások, irányítópultok és integrációk**: az új ConnectionMonitor_CL táblázat alapján manuálisan kell szerkesztenie a lekérdezéseket. A riasztások a mérőszámokban való újbóli létrehozásával kapcsolatban lásd: [hálózati kapcsolat figyelése a kapcsolat figyelője szolgáltatással](./connection-monitor-overview.md#metrics-in-azure-monitor).
    
## <a name="prerequisites"></a>Előfeltételek

* Győződjön meg arról, hogy a Network Watcher engedélyezve van az előfizetésben és a Log Analytics munkaterület régiójában.
* A telepített Log Analytics-ügynökökkel rendelkező Azure-beli virtuális gépeket a Network Watcher bővítménnyel kell engedélyezni.

## <a name="migrate-the-tests"></a>A tesztek áttelepíteni

A tesztek Network Performance Monitorról a következőre történő áttelepíthetők:

1. A Network Watcher területen válassza a **Csatlakozáskezelő** lehetőséget, majd válassza a **tesztek átmigrálása a NPM** lapról lehetőséget. 

    :::image type="content" source="./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png" alt-text="Tesztek migrálása Network Performance Monitorról a kapcsolódási figyelőbe" lightbox="./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png":::
    
1. A legördülő listában válassza ki az előfizetést és a munkaterületet, majd válassza ki az áttelepíteni kívánt NPM szolgáltatást. 
1. Az **Importálás** gombra kattintva áttelepítheti a teszteket.

Az áttelepítés megkezdése után a következő módosítások lépnek érvénybe: 
* Létrejön egy új Csatlakozáskezelő-erőforrás.
   * Régiónként egy figyelőt hoznak létre. Helyszíni ügynökökkel végzett tesztek esetén az új figyelő neve a következő lesz: `<workspaceName>_"on-premises"` . Az Azure-ügynökökkel végzett tesztek esetében az új figyelő neve a következő lesz: `<workspaceName>_<Azure_region_name>` .
   * A figyelési adattárolási szolgáltatás jelenleg ugyanazon a Log Analytics munkaterületen található, amelyben a NPM engedélyezve van, egy új táblázatban Connectionmonitor_CL néven. 
   * A teszt neve a test Group neve alapján kerül továbbításra. A teszt leírása nincs áttelepítve.
   * A forrás és a cél végpontok létrehozása és használata az új tesztelési csoportban történik. A helyszíni ügynökök esetében a végpontok formátuma a következő: `<workspaceName>_"endpoint"_<FQDN of on-premises machine>` . Az Azure esetében, ha az áttelepítési tesztek olyan ügynököket tartalmaznak, amelyek nem futnak, engedélyeznie kell az ügynököket, és újra kell telepíteni az áttelepítést.
   * A célport és a Szondázási intervallum átkerül egy *\<testname> TC_* nevű és *TC_ \<testname> _AppThresholdsi* teszt-konfigurációba. A protokoll beállítása a portok értékei alapján történik. A sikeres küszöbértékek és egyéb opcionális tulajdonságok üresen maradnak.
* A NPM nincs letiltva, ezért az áttelepített tesztek továbbra is küldhetnek adatátvitelt a NetworkMonitoring és ConnectionMonitor_CL táblákba. Ez a megközelítés biztosítja, hogy a meglévő napló alapú riasztások és integrációk ne legyenek hatással.
* Az újonnan létrehozott Csatlakozáskezelő látható a Csatlakozáskezelőben.

Az áttelepítés után ügyeljen a következőre:
* Manuálisan tiltsa le a teszteket a NPM. Amíg így tesz, továbbra is díjat kell fizetnie. 
* A NPM letiltása közben hozza létre újra a riasztásokat a ConnectionMonitor_CL táblában, vagy használjon metrikákat. 
* Telepítse át a külső integrációkat a ConnectionMonitor_CL táblába. A külső integrációk példái a Power BI-és Grafana található irányítópultok, valamint a biztonsági információkkal és az Event Management-(SIEM-) rendszerekkel való integrációk.


## <a name="next-steps"></a>Következő lépések

A kapcsolódási Figyelőről további információt a következő témakörben talál:
* [Migrálás a kapcsolódási figyelőből (klasszikus) a kapcsolódási figyelőbe](./migrate-to-connection-monitor-from-connection-monitor-classic.md)
* [Csatlakozáskezelő létrehozása a Azure Portal használatával](./connection-monitor-create-using-portal.md)
