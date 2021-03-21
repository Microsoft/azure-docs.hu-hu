---
title: Figyelési lehetőségek – Azure dedikált HSM | Microsoft Docs
description: Az Azure dedikált HSM figyelési lehetőségeinek és figyelési feladatainak áttekintése
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/18/2020
ms.author: mbaldwin
ms.openlocfilehash: 4c3d062d615208177fcb9c7df511f598613bf6a6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97092703"
---
# <a name="azure-dedicated-hsm-monitoring"></a>Azure dedikált HSM-figyelés

Az Azure dedikált HSM szolgáltatás egy fizikai eszközt biztosít az ügyfelek kizárólagos használatára a teljes körű rendszergazdai ellenőrzés és felügyelet felelőssége mellett. Az elérhetővé tett eszköz egy [Thales Luna 7 HSM-modell A790](https://cpl.thalesgroup.com/encryption/hardware-security-modules/network-hsms).  A Microsoft nem rendelkezik rendszergazdai hozzáféréssel, ha az ügyfél nem veszi igénybe a fizikai soros portok mellékletét figyelési szerepkörként. Ennek eredményeképpen az ügyfelek felelősek az általános működési tevékenységekért, beleértve az átfogó monitorozási és naplózási elemzéseket.
Az ügyfelek teljes mértékben felelősek a HSM használó alkalmazásokért, és a Thales támogatással vagy tanácsadással kapcsolatos segítségért kell működniük. Az üzemeltetési higiéniai ügyfelek tulajdonlása miatt nem lehetséges, hogy a Microsoft bármilyen magas rendelkezésre állású garanciát nyújtson ehhez a szolgáltatáshoz. Az ügyfél feladata annak biztosítása, hogy alkalmazásaikat megfelelően konfigurálja a magas rendelkezésre állás érdekében. A Microsoft figyeli és karbantartja az eszköz állapotát és a hálózati kapcsolatot.

## <a name="microsoft-monitoring"></a>Microsoft monitoring

A használatban lévő Thales Luna 7 HSM-eszköz alapértelmezett SNMP-és soros portja az eszköz figyelésének lehetőségei. A Microsoft a soros port kapcsolatát fizikai eszközként használta az eszközhöz való csatlakozáshoz az eszköz állapotának alapszintű telemetria lekéréséhez. Ide tartoznak az olyan elemek, mint például a hőmérséklet és az összetevők állapota, például az energiaellátási készletek és a ventilátorok.
Ennek eléréséhez a Microsoft a Thales-eszközön beállított, nem rendszergazdai "figyelő" szerepkört használ. Ez a szerepkör lehetővé teszi a telemetria beolvasását, de nem biztosít hozzáférést az eszközhöz rendszergazdai feladat vagy semmilyen módon a titkosítási adatok megtekintése során. Ügyfeleink biztosak lehetnek abban, hogy az eszközük valóban a bizalmas titkosítási kulcs tárolására, kezelésére és használatára szolgál. Abban az esetben, ha az ügyfelek nem elégedettek az alapszintű állapot-figyeléshez szükséges minimális hozzáféréssel, letilthatja a figyelési fiókot. Ennek nyilvánvaló következménye, hogy a Microsoft nem rendelkezik információval, így nem nyújt semmilyen proaktív értesítést az eszköz állapotával kapcsolatban. Ebben az esetben az ügyfél felelős az eszköz állapotáról.
A figyelő függvény úgy van beállítva, hogy 10 percenként lekérdezze az eszközt az állapotadatok beszerzéséhez. A soros kommunikáció jellegéből adódó hiba miatt a riasztást csak az egy órás időszakra vonatkozó több negatív állapotú állapotjelző után lehet kiemelni. Ez a riasztás végül egy proaktív ügyfél-kommunikációt eredményez, amely értesíti a problémát.
A probléma jellegétől függően a megfelelő lépéseket kell végrehajtani a hatás csökkentése és az alacsony kockázatú szervizelés biztosítása érdekében. A tápegység meghibásodása például egy olyan gyors swap-eljárás, amely nem eredményez eredő módosítási eseményt, ezért az alacsony hatású és a minimális kockázatot jelentő művelettel lehet elvégezni. Más eljárásoknál előfordulhat, hogy az eszköz nullázása és megszüntetése szükséges az ügyfélre vonatkozó biztonsági kockázat minimálisra csökkentése érdekében. Ebben az esetben az ügyfél kiépít egy másik eszközt, és újracsatlakoztatja a magas rendelkezésre állású párosítást, így az eszköz szinkronizálása is aktiválódik. A normál művelet minimális idő alatt folytatódni fog, minimális megszakítással és legalacsonyabb biztonsági kockázattal.  

## <a name="customer-monitoring"></a>Ügyfelek figyelése

A dedikált HSM szolgáltatás értékének kiosztása az eszköz ügyfél általi lekérésének vezérlése, különös tekintettel arra, hogy a felhőben szállított eszköz. Ennek a vezérlőnek a következménye az eszköz állapotának monitorozása és felügyelete. A Thales Luna 7 HSM-eszköz útmutatást nyújt az SNMP és a syslog megvalósításához. A dedikált HSM szolgáltatás ügyfelei akkor is ajánlott ezt a szolgáltatást használni, ha a Microsoft monitor-fiók aktív marad, és kötelezőnek tartja, ha letiltja a Microsoft monitor-fiókot.
Az elérhető technikák lehetővé teszik az ügyfél számára a problémák azonosítását és a Microsoft támogatási szolgálatának meghívását a megfelelő szervizelési munka elindításához.

## <a name="next-steps"></a>Következő lépések

Javasoljuk, hogy a szolgáltatás alapvető fogalmait, például a magas rendelkezésre állást és a biztonságot, az eszközök üzembe helyezése és az alkalmazások tervezése és telepítése előtt jól megértse. További fogalmi szintű témakörök:

* [Magas rendelkezésre állás](high-availability.md)
* [Fizikai biztonság](physical-security.md)
* [Hálózat](networking.md)
* [Támogatási lehetőségek](supportability.md)
