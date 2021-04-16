---
title: Ütemezett karbantartás – Azure Database for PostgreSQL – Rugalmas kiszolgáló
description: Ez a cikk a rugalmas Azure Database for PostgreSQL ütemezett karbantartási funkcióját ismerteti.
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 50ef040f1cb7d8c533ec5ee31e9bffa2e6dca2f5
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478012"
---
# <a name="scheduled-maintenance-in-azure-database-for-postgresql--flexible-server"></a>Ütemezett karbantartás rugalmas Azure Database for PostgreSQL-kiszolgálón
 
Azure Database for PostgreSQL – A rugalmas kiszolgáló rendszeres karbantartást végez a felügyelt adatbázis biztonságának, stabilának és naprakésznek fenntartása érdekében. A karbantartás során a kiszolgáló új funkciókat, frissítéseket és javításokat kap.
 
> [!IMPORTANT]
> Azure Database for PostgreSQL – A rugalmas kiszolgáló előzetes verzióban érhető el
 
## <a name="selecting-a-maintenance-window"></a>Karbantartási időszak kiválasztása
 
A karbantartást a hét egy adott napján és egy adott napon belüli időszakra ütemezheti. Azt is választhatja, hogy a rendszer automatikusan kiválaszt egy napot és egy időablakot. A rendszer mindkét esetben öt nappal a karbantartás előtt riasztja. A rendszer azt is tudatja, hogy mikor indult el a karbantartás, és mikor fejeződött be sikeresen.
 
A közelgő ütemezett karbantartásról a következő értesítéseket kaphatja:
 
* E-mailben egy adott címre
* E-mailben egy Azure Resource Manager szerepkörbe
* Sms-ben elküldve mobileszközökre
* Leküldéses értesítésként Azure-alkalmazásba
* Hangüzenetként
 
A karbantartási ütemezés beállításainak megadásakor kiválaszthatja a hét valamelyik napját és egy időszakot. Ha nem határoz meg időpontot, a rendszer a kiszolgáló régiójának időzónája szerint 23:00 és 7:00 közötti időpontot választ. Az Azure-előfizetésében különböző ütemezéseket határozhat meg az egyes rugalmas kiszolgálókhoz. 
 
> [!IMPORTANT]
> Általában legalább 30 nap telik el a kiszolgáló sikeres ütemezett karbantartási eseményei között.
>
> A kritikus fontosságú vészhelyzeti frissítések, például súlyos biztonsági rések esetén azonban előfordulhat, hogy kevesebb mint 5 nappal korábban kap értesítést a frissítésről. A kritikus fontosságú frissítés akkor is telepíthető a kiszolgálón, ha az elmúlt 30 napban sikeres ütemezett karbantartást hajtottak végre azon.

Az ütemezési beállításokat bármikor frissítheti. Ha a rugalmas kiszolgálóhoz karbantartás van ütemezve, és Ön frissíti az ütemezési beállításokat, az aktuális bevezetés az ütemezett állapot szerint folytatódik, és az ütemezési beállítások módosítása a következő ütemezett karbantartás sikeres befejezését követően hatóvá válik.

Az Azure-előfizetésben található összes rugalmas kiszolgálóhoz definiálhat rendszer által felügyelt ütemezést vagy egyéni ütemezést.  
* Egyéni ütemezéssel megadhatja a kiszolgáló karbantartási időszakát a hét napjának és egy egyórás időszakának kiválasztásával.  
* A rendszer által felügyelt ütemezéssel a rendszer a kiszolgáló régiójának időében 11:00 és 07:00 között bármilyen egyórás időt választ.  

A módosítások részeként a frissítéseket először a rendszer által felügyelt ütemezéssel konfigurált kiszolgálókra, majd az egyéni ütemezésű kiszolgálókra alkalmazjuk, miután legalább 7 nap van egy adott régióban. Ha korai frissítéseket kíván kapni a fejlesztési és tesztelési környezet kiszolgálóflottairól, javasoljuk, hogy konfigurálja a rendszer által felügyelt ütemezést a fejlesztési és tesztelési környezetben használt kiszolgálókhoz. Ez lehetővé teszi, hogy először a legújabb frissítést kapja meg a fejlesztési/tesztelési környezetben az ellenőrzés teszteléséhez és értékeléséhez. Ha bármilyen viselkedést vagy a nem megfelelő változásokat kell kezelnie, még azelőtt, hogy ugyanezt a frissítést egyénileg felügyelt ütemezéssel ű éles kiszolgálókra a rendszer kiveszi. A frissítés 7 nap után megkezdi a bevezetést a rugalmas egyéni ütemezésű kiszolgálókon, és a megadott karbantartási időszakban lesz alkalmazva a kiszolgálóra. Jelenleg nincs lehetőség a frissítés halasztásra az értesítés elküldését követően. Az egyéni ütemezés használata csak éles környezetekben ajánlott. 

Ritka esetekben a karbantartási eseményt megszakíthatja a rendszer, vagy sikertelen lehet a befejezése. Ha a frissítés sikertelen, a rendszer visszaállítja a frissítést, és visszaállítja a bináris fájlok előző verzióját. Az ilyen sikertelen frissítési forgatókönyvekben a kiszolgáló újraindítása a karbantartási időszak alatt is előfordulhat. Ha a frissítést megszakítják vagy sikertelenek, a rendszer értesítést küld Önnek a megszakított vagy sikertelen karbantartási eseményekről. A következő karbantartási kísérlet az aktuális ütemezési beállítások szerint lesz ütemezve, és erről öt nappal előre értesítést kap. 

 
## <a name="next-steps"></a>Következő lépések
 
* Ismerje meg, [hogyan módosíthatja a karbantartási ütemezést](how-to-maintenance-portal.md)
* Ismerje meg, [hogyan kaphat értesítéseket a közelgő karbantartásról](../../service-health/service-notifications.md) a Azure Service Health
* Ismerje meg, hogyan [állíthat be riasztásokat a közelgő ütemezett karbantartási eseményekkel kapcsolatban](../../service-health/resource-health-alert-monitor-guide.md)
