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
ms.openlocfilehash: 18d0a24de6f0775fdb35799512f9796a323d353a
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105045484"
---
# <a name="migrate-to-connection-monitor-from-network-performance-monitor"></a>Migrálás a Network Performance Monitorről a kapcsolódási figyelőbe

> [!IMPORTANT]
> 2021. július 1-től nem lehet új teszteket felvenni egy meglévő munkaterületre, vagy új munkaterületet engedélyezni Network Performance Monitor. Továbbra is használhatja a 2021. július 1. előtt létrehozott teszteket. A szolgáltatás megszakadásának minimalizálásához a jelenlegi számítási feladatokhoz telepítse át a teszteket Network Performance Monitorról az Azure Network Watcher új, 2024. február 29. előtti kapcsolódási figyelője felé.

Network Performance Monitor (NPM) teszteit áttelepítheti az új, továbbfejlesztett kapcsolódási figyelőre egyetlen kattintással és nulla állásidővel. További információ az előnyökről: a [kapcsolódási figyelő](./connection-monitor-overview.md).


## <a name="key-points-to-note"></a>Jegyezze fel a legfontosabb pontokat

Az áttelepítés a következő eredményeket segíti elő:

* A helyszíni ügynökök és a tűzfalbeállítások ugyanúgy működnek, mint a. Nincs szükség módosításra. Log Analytics Azure-beli virtuális gépekre telepített ügynököket a [Network Watcher bővítménnyel](../virtual-machines/extensions/network-watcher-windows.md)kell helyettesíteni.
* A meglévő tesztek le vannak képezve a Csatlakozáskezelő > a tesztelési csoport > tesztelési formátumára. A **Szerkesztés** lehetőség kiválasztásával megtekintheti és módosíthatja az új kapcsolat figyelője tulajdonságait, letöltheti a sablonokat, és elküldheti a sablont Azure Resource Manager használatával.
* Az ügynökök az Log Analytics munkaterületre és a metrikára is küldenek adatokat.
* Adatfigyelés:
   * **Log Analyticsban tárolt adatértékek**: az áttelepítés előtt az adatterület abban a munkaterületen marad, amelyben a NPM a NetworkMonitoring táblában van konfigurálva. Az áttelepítés után az adatelérési pont a NetworkMonitoring táblára, a NWConnectionMonitorTestResult táblára és a NWConnectionMonitorPathResult táblára kerül ugyanabban a munkaterületen. Miután a tesztek le vannak tiltva a NPM-ben, az adattárolást csak a NWConnectionMonitorTestResult tábla és a NWConnectionMonitorPathResult táblában tárolja a rendszer.
   * **Napló alapú riasztások, irányítópultok és integrációk**: manuálisan kell szerkesztenie a lekérdezéseket az új NWConnectionMonitorTestResult tábla és NWConnectionMonitorPathResult tábla alapján. A riasztások a mérőszámokban való újbóli létrehozásával kapcsolatban lásd: [hálózati kapcsolat figyelése a kapcsolat figyelője szolgáltatással](./connection-monitor-overview.md#metrics-in-azure-monitor).
* ExpressRoute-figyelés esetén:
    * **Végpontok közötti veszteség és késés**: a kapcsolódási figyelő ezt a lehetőséget fogja biztosítani, és könnyebb, mint a NPM, mivel a felhasználóknak nem kell konfigurálniuk a figyelni kívánt áramköröket és társításokat. Az elérési útban lévő áramkörök automatikusan fel lesznek derítve, az adatok metrikákban lesznek elérhetők (amely a NPM tárolt eredményeknél nagyobb, mint LA). A topológia ugyanúgy fog működni.
    * **Sávszélesség-mérések**: a NPM log Analytics-alapú megközelítése nem volt hatékony a sávszélesség-figyelésben a ExpressRoute ügyfelek számára. Ez a funkció mostantól nem érhető el a Csatlakozáskezelőben.
    
## <a name="prerequisites"></a>Előfeltételek

* Győződjön meg arról, hogy a Network Watcher engedélyezve van az előfizetésben és a Log Analytics munkaterület régiójában. 
* Abban az esetben, ha az Azure-beli virtuális gép egy másik régióhoz vagy előfizetéshez csatlakozik, mint a Log Analytics munkaterület egy végpontja, győződjön meg arról, hogy a Network Watcher engedélyezve van az adott előfizetéshez és régióhoz.   
* A telepített Log Analytics-ügynökökkel rendelkező Azure-beli virtuális gépeket a Network Watcher bővítménnyel kell engedélyezni.

## <a name="migrate-the-tests"></a>A tesztek áttelepíteni

A tesztek Network Performance Monitorról a következőre történő áttelepíthetők:

1. A Network Watcher területen válassza a **Csatlakozáskezelő** lehetőséget, majd válassza a **tesztek átmigrálása a NPM** lapról lehetőséget. 

    :::image type="content" source="./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png" alt-text="Tesztek migrálása Network Performance Monitorról a kapcsolódási figyelőbe" lightbox="./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png":::
    
1. A legördülő listában válassza ki az előfizetést és a munkaterületet, majd válassza ki az áttelepíteni kívánt NPM szolgáltatást. 
1. Az **Importálás** gombra kattintva áttelepítheti a teszteket.

Az áttelepítés megkezdése után a következő módosítások lépnek érvénybe: 
* Létrejön egy új Csatlakozáskezelő-erőforrás.
   * Régiónként egy figyelőt hoznak létre. Helyszíni ügynökökkel végzett tesztek esetén az új figyelő neve a következő lesz: `<workspaceName>_"workspace_region_name"` . Az Azure-ügynökökkel végzett tesztek esetében az új figyelő neve a következő lesz: `<workspaceName>_<Azure_region_name>` .
   * A figyelési adattárolási szolgáltatás jelenleg ugyanazon a Log Analytics munkaterületen található, amelyben a NPM engedélyezve van, az új táblákban, a NWConnectionMonitorTestResult Table és a NWConnectionMonitorPathResult tábla néven. 
   * A teszt neve a test Group neve alapján kerül továbbításra. A teszt leírása nincs áttelepítve.
   * A forrás és a cél végpontok létrehozása és használata az új tesztelési csoportban történik. A helyszíni ügynökök esetében a végpontok formátuma a következő: `<workspaceName>_<FQDN of on-premises machine>` . Az ügynök leírása nincs áttelepítve.
   * A rendszer áthelyezi a célport és a szondázás intervallumát a és a nevű teszt-konfigurációba `TC_<protocol>_<port>` `TC_<protocol>_<port>_AppThresholds` . A protokoll beállítása a portok értékei alapján történik. Az ICMP esetében a teszt konfigurációk neve a következő: `TC_<protocol>` és `TC_<protocol>_AppThresholds` . A siker küszöbértékei és egyéb opcionális tulajdonságok, ha a beállítás át van telepítve, máskülönben üresen maradnak.
   * Ha az áttelepítési tesztek olyan ügynököket tartalmaznak, amelyek nem futnak, engedélyeznie kell az ügynököket, és újra kell telepíteni az áttelepítést.
* A NPM nincs letiltva, ezért az áttelepített tesztek továbbra is küldhetnek adatküldést a NetworkMonitoring táblába, a NWConnectionMonitorTestResult Table és a NWConnectionMonitorPathResult táblába. Ez a megközelítés biztosítja, hogy a meglévő napló alapú riasztások és integrációk ne legyenek hatással.
* Az újonnan létrehozott Csatlakozáskezelő látható a Csatlakozáskezelőben.

Az áttelepítés után ügyeljen a következőre:
* Manuálisan tiltsa le a teszteket a NPM. Amíg így tesz, továbbra is díjat kell fizetnie. 
* A NPM letiltása közben hozza létre újra a riasztásokat a NWConnectionMonitorTestResult és a NWConnectionMonitorPathResult táblákon, vagy használjon metrikákat. 
* Telepítse át az összes külső integrációt a NWConnectionMonitorTestResult és a NWConnectionMonitorPathResult táblába. A külső integrációk példái a Power BI-és Grafana található irányítópultok, valamint a biztonsági információkkal és az Event Management-(SIEM-) rendszerekkel való integrációk.


## <a name="next-steps"></a>Következő lépések

A kapcsolódási Figyelőről további információt a következő témakörben talál:
* [Migrálás a kapcsolódási figyelőből (klasszikus) a kapcsolódási figyelőbe](./migrate-to-connection-monitor-from-connection-monitor-classic.md)
* [Csatlakozáskezelő létrehozása a Azure Portal használatával](./connection-monitor-create-using-portal.md)