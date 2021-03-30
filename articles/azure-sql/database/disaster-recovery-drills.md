---
title: Vészhelyreállítási próbák
description: Útmutatás és ajánlott eljárások a Azure SQL Database használatának elvégzéséhez a vész-helyreállítási gyakorlatok végrehajtásához.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 12/18/2018
ms.openlocfilehash: f53a08a12c5afda8dbc3f25d9102f52b870ceea4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91321662"
---
# <a name="performing-disaster-recovery-drills"></a>Vész-helyreállítási gyakorlatok végrehajtása
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

A helyreállítási munkafolyamatok alkalmazás-készültségének ellenőrzése rendszeresen történik. Az alkalmazás viselkedésének, valamint az adatvesztés következményeinek és/vagy a feladatátvétel által okozott fennakadásnak az ellenőrzése jó mérnöki gyakorlat. Emellett a legtöbb iparági szabvány követelménye az üzletmenet folytonossági minősítésének része.

A vész-helyreállítási részletezés végrehajtása a következőkből áll:

* Adatcsatorna-kimaradás szimulálása
* Helyreállítása
* Az alkalmazás integritásának érvényesítése a helyreállítás után

Attól függően, hogy az [alkalmazás hogyan lett megtervezve az üzletmenet folytonossága](business-continuity-high-availability-disaster-recover-hadr-overview.md)érdekében, a részletezés végrehajtásának munkafolyamata eltérő lehet. Ez a cikk a vész-helyreállítási gyakorlat végrehajtásának ajánlott eljárásait ismerteti Azure SQL Database környezetében.

## <a name="geo-restore"></a>Georedundáns visszaállítás

Ha meg szeretné akadályozni, hogy a vész-helyreállítási gyakorlat elvégzése során milyen adatvesztés lehetséges, végezze el a részletezést a tesztkörnyezet használatával az éles környezet másolatának létrehozásával és a használatával, hogy ellenőrizze az alkalmazás feladatátvételi munkafolyamatát.

### <a name="outage-simulation"></a>Kimaradás szimulálása

A leállás szimulálása érdekében átnevezheti a forrás-adatbázist. Ez a név megváltoztatja az alkalmazás kapcsolódási hibáit.

### <a name="recovery"></a>Helyreállítási

* Az [itt](disaster-recovery-guidance.md)leírtak szerint végezze el az adatbázis geo-visszaállítását egy másik kiszolgálóra.
* Módosítsa az alkalmazás konfigurációját a helyreállított adatbázishoz való kapcsolódáshoz, és kövesse az [adatbázis konfigurálása a helyreállítási útmutató után](disaster-recovery-guidance.md) a helyreállítás befejezéséhez című témakört.

### <a name="validation"></a>Érvényesítés

Végezze el a részletezést az alkalmazás integritás utáni helyreállításának ellenőrzésével (beleértve a kapcsolati karakterláncokat, a bejelentkezéseket, az alapszintű funkciók tesztelését vagy a szabványos alkalmazások signoffs eljárásainak egyéb érvényességi részeit).

## <a name="failover-groups"></a>Feladatátvételi csoportok

A feladatátvételi csoportok használatával védett adatbázisok esetében a részletezési gyakorlat a másodlagos kiszolgálóra irányuló tervezett feladatátvételt is magában foglalja. A tervezett feladatátvétel biztosítja, hogy a feladatátvételi csoportban lévő elsődleges és másodlagos adatbázisok szinkronban maradjanak a szerepkörök kiváltásakor. A nem tervezett feladatátvételtől eltérően ez a művelet nem eredményez adatvesztést, így a részletezést az éles környezetben is elvégezheti.

### <a name="outage-simulation"></a>Kimaradás szimulálása

A leállás szimulálása érdekében letilthatja az adatbázishoz csatlakozó webalkalmazást vagy virtuális gépet. Ez a leállás-szimuláció a webes ügyfelek kapcsolódási hibáit eredményezi.

### <a name="recovery"></a>Helyreállítási

* Győződjön meg arról, hogy az alkalmazás konfigurációja a DR régióban a korábbi másodlagosra mutat, amely a teljesen elérhető új elsődleges lesz.
* A feladatátvételi csoport [tervezett feladatátvételének](scripts/setup-geodr-and-failover-database-powershell.md) kezdeményezése a másodlagos kiszolgálóról.
* A helyreállítás befejezéséhez kövesse az [adatbázis konfigurálása a helyreállítási útmutató után](disaster-recovery-guidance.md) című témakört.

### <a name="validation"></a>Érvényesítés

Végezze el a részletezést az alkalmazás integritás utáni helyreállításának ellenőrzésével (beleértve a kapcsolatot, az alapszintű funkciók tesztelését vagy a részletezési signoffs szükséges egyéb érvényesítéseket).

## <a name="next-steps"></a>Következő lépések

* Az üzletmenet-folytonossági forgatókönyvek megismeréséhez lásd: [folytonossági forgatókönyvek](business-continuity-high-availability-disaster-recover-hadr-overview.md).
* Az automatikus biztonsági mentések Azure SQL Databaseával kapcsolatos további tudnivalókért lásd: [SQL Database automatikus biztonsági mentések](automated-backups-overview.md)
* Ha többet szeretne megtudni a helyreállítás automatizált biztonsági mentéséről, olvassa el [az adatbázis visszaállítása a szolgáltatás által kezdeményezett biztonsági másolatokból](recovery-using-backups.md)című témakört.
* A gyorsabb helyreállítási lehetőségek megismeréséhez lásd: [aktív földrajzi replikálás](active-geo-replication-overview.md) és [automatikus feladatátvételi csoportok](auto-failover-group-overview.md).
