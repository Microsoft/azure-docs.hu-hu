---
title: Tudnivalók a Azure Monitor naplók hálózati figyeléséről | Microsoft Docs
description: A hálózati figyelési megoldások áttekintése, beleértve a NPM, a Felhőbeli, a helyszíni és a hibrid környezetek közötti hálózatok felügyeletét.
services: monitoring-and-diagnostics
documentationcenter: na
author: agummadi
manager: ''
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: agummadi
ms.openlocfilehash: a92c6789723dc42ac8f3a9c471e494079dc19328
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98233440"
---
# <a name="network-monitoring-solutions"></a>Hálózati figyelési megoldások 

Az Azure számos megoldást kínál a hálózati eszközök figyelésére. Az Azure megoldásokat és segédprogramokat tartalmaz a hálózati kapcsolatok, a ExpressRoute-áramkörök állapotának figyelése és a felhőben lévő hálózati forgalom elemzéséhez.

## <a name="network-performance-monitor-npm"></a>Network Performance Monitor (NPM)

A Network Performance Monitor (NPM) a képességek egyik csomagja, amely a hálózat állapotának figyelésére, az alkalmazásaihoz való hálózati kapcsolatra, valamint a hálózat teljesítményének áttekintésére irányul. A NPM felhőalapú, és hibrid hálózati figyelési megoldást kínál, amely figyeli a következők közötti kapcsolatot:
 
* Felhőbeli üzemelő példányok és helyszíni telephelyek
* Több adatközpont és fiókirodák
* Kritikus többrétegű alkalmazások/mikro-szolgáltatások
* Felhasználói helyszínek és webalapú alkalmazások (HTTP/HTTPs) 

A Teljesítményfigyelő, a ExpressRoute-figyelő és a szolgáltatás-csatlakozási figyelő a NPM-en belüli figyelési képességek, és az alábbiakban olvasható.

## <a name="performance-monitor"></a>Teljesítménymonitorozás

A Teljesítményfigyelő a NPM része, és a Felhőbeli, a hibrid és a helyszíni környezetek hálózati figyelése. A hálózati kapcsolatot a távoli fiókirodák és a mezők, a tárolóhelyek, az adatközpontok és a felhők között figyelheti. A hálózati problémák észlelése előtt a felhasználók panaszkodnak. A legfontosabb előnyök a következők:

* Különböző alhálózatokban lévő veszteségek és késések figyelése és riasztások beállítása
* A hálózaton lévő összes elérési út (beleértve a redundáns útvonalakat is) figyelése
* Az átmeneti és az időponthoz tartozó hálózati hibák elhárítása, amelyeket nehéz replikálni
* Határozza meg a hálózat adott szegmensét, amely a teljesítmény romlásának felel meg.
* A hálózat állapotának figyelése, nincs szükség SNMP-re

![NPM-topológia térképe](./media/network-monitoring-overview/npm-topology-map.png) 

További információkért tekintse meg a következő cikkeket:

* [Network Performance Monitor megoldás konfigurálása Azure Monitor naplókban](../azure-monitor/insights/network-performance-monitor.md) 
* [Használati esetek](/archive/blogs/msoms/monitor-on-premises-cloud-iaas-and-hybrid-networks-using-oms-network-performance-monitor)
* Termékfrissítések:
  * [2017. február](/archive/blogs/msoms/oms-network-performance-monitor-is-now-generally-available)
  * [2017. augusztus](/archive/blogs/msoms/improvements-to-oms-network-performance-monitor)

## <a name="expressroute-monitor"></a>ExpressRoute-figyelő

A NPM for ExpressRoute átfogó ExpressRoute-figyelést biztosít az Azure-beli privát és Microsoft-kapcsolatok számára. A E2E-kapcsolat és a teljesítmény figyelése a fiókirodák és az Azure között a ExpressRoute-en keresztül végezhető el. A legfontosabb képességek a következők:

* Az előfizetéshez társított ER-áramkörök automatikus észlelése
* Hálózati topológia észlelése a helyszíni környezetből a Felhőbeli alkalmazásokba
* Kapacitás megtervezése, sávszélesség-kihasználtság elemzése
* Figyelés és riasztás mind az elsődleges, mind a másodlagos útvonalon
* Az Azure-szolgáltatásokkal való kapcsolat figyelése, például Microsoft 365, Dynamics 365,... több mint ExpressRoute
* A virtuális hálózatok való kapcsolat romlásának észlelése

![Régiók közötti forgalmat mutató geo-Térkép](./media/network-monitoring-overview/expressroute-topology-map.png) 

További információért tekintse át a következő cikkeket:

* [Network Performance Monitor for ExpressRoute konfigurálása](../expressroute/how-to-npm.md)
* [Blogbejegyzés](https://aka.ms/NPMExRmonitorGA)

## <a name="service-connectivity-monitor"></a>Szolgáltatáskapcsolati monitor

A szolgáltatás-kapcsolat figyelésével mostantól tesztelheti az alkalmazások elérhetőségét, és felderítheti a teljesítmény szűk keresztmetszetét a helyszíni, a szállítói hálózatokon és a felhő/privát adatközpontokban.

* Az alkalmazásokhoz való végpontok közötti hálózati kapcsolat figyelése
* Az alkalmazások kézbesítésének korrelálása hálózati teljesítménnyel, a felhasználó és az alkalmazás közötti útvonalon a romlás pontos helyének észlelése
* Alkalmazások elérhetőségének tesztelése több felhasználói helyről szerte a világon
* Az üzletági és SaaS-alkalmazások hálózati késésének és a csomagok elvesztésének meghatározása
* A hálózatban található gyors elérésű pontok meghatározása, amelyek gyenge alkalmazások teljesítményét okozhatják
* A Microsoft 365, a Dynamics 365, a Skype vállalati verzió és más Microsoft-szolgáltatások számára elérhető beépített tesztek használatával megfigyelhető az alkalmazások elérhetősége Microsoft 365

További információért tekintse át a következő cikkeket:

* [Network Performance Monitor konfigurálása a figyelési szolgáltatás végpontjai számára](../azure-monitor/insights/network-performance-monitor-service-connectivity.md#configuration)
* [Blogbejegyzés](https://aka.ms/svcendptmonitor)

## <a name="traffic-analytics"></a>Forgalmi elemzések
Traffic Analytics egy felhőalapú megoldás, amely láthatóságot biztosít a felhasználói és alkalmazási tevékenységeknek a felhőalapú hálózatokon. A rendszer elemzi a NSG-naplók elemzését, hogy betekintést nyújtson a következőkbe:

* Az Azure és az Internet, a nyilvános Felhőbeli régiók, a virtuális hálózatok és az alhálózatok közötti forgalom a hálózatokon keresztül zajlik
* A hálózaton lévő alkalmazások és protokollok anélkül, hogy a szippantók vagy a dedikált flow-gyűjtő készülékekre lenne szükség.
* Top-beszélők, csevegéses alkalmazások, virtuális gépekkel folytatott beszélgetések a felhőben, forgalmi pontok
* A virtuális hálózatok közötti forgalom forrásai és célhelyei, a kritikus fontosságú üzleti szolgáltatások és alkalmazások közötti kapcsolatok
* Biztonság – rosszindulatú forgalom, internetre nyitott portok, alkalmazások vagy virtuális gépek, amelyekhez internet-hozzáférésre van kísérlet...
* Kapacitáskihasználás – segít a VPN-átjárók és egyéb szolgáltatások kihasználtsági trendjeinek figyelésével a túlzott kiépítés vagy a kihasználás során felmerülő problémák elhárításában

Traffic Analytics a szervezet hálózati tevékenységének, az alkalmazások és az adatok biztonságossá tételének, a munkaterhelés teljesítményének optimalizálása és a megfelelő működés biztosítása érdekében hasznos információkat nyújt.

![A 2. régión belüli forgalmat bemutató geo-Térkép](../network-watcher/media/traffic-analytics/geo-map-view-showcasing-traffic-distribution-to-countries-and-continents.png) 

Kapcsolódó hivatkozások:
* [Blogbejegyzés](https://aka.ms/trafficanalytics), [dokumentáció](../network-watcher/traffic-analytics.md), [GYIK](../network-watcher/traffic-analytics-faq.md)

## <a name="dns-analytics"></a>DNS-elemzés
A DNS-rendszergazdák számára készült megoldás a DNS-naplókat gyűjti, elemzi és korrelálja a biztonság, a műveletek és a teljesítménnyel kapcsolatos információk biztosításához.  A funkciók némelyike:

* A kártékony tartományokra feloldani próbáló ügyfelek azonosítása
* Elavult erőforrásrekordok azonosítása
* Gyakran lekérdezett tartománynevek és beszédes DNS-ügyfelek láthatósága
* DNS-kiszolgálókon a kérések terhelésének láthatósága
* Dinamikus DNS-regisztrációs hibák figyelése

![DNS Analytics irányítópult](./media/network-monitoring-overview/dns-analytics-overview.png) 

Kapcsolódó hivatkozások:
* [Blogbejegyzés](/archive/blogs/msoms/introducing-oms-dns-analytics), [dokumentáció](../azure-monitor/insights/dns-analytics.md)

## <a name="miscellaneous"></a>Különböző veszélyes anyagok és tárgyak

* [Új díjszabás](../azure-monitor/insights/network-performance-monitor-pricing-faq.md)