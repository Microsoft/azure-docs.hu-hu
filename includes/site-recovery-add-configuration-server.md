---
title: fájl belefoglalása
description: fájl belefoglalása
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: include
ms.date: 02/28/2019
ms.author: mayg
ms.custom: include file
ms.openlocfilehash: f699ffe6d5a91e8ce3ae90c7e12249bbad0fff3e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "96001556"
---
1. Futtassa az egyesített telepítő fájlját.
2. A **Kezdés előtt** válassza **a konfigurációs kiszolgáló és a Process Server telepítése** lehetőséget.

    ![Képernyőfelvétel: az első lépések képernyő az egyesített telepítőben.](./media/site-recovery-add-configuration-server/combined-wiz1.png)

3. A **Külső szoftver licence** területen kattintson az **Elfogadom** elemre a MySQL letöltéséhez és telepítéséhez.

    ![Képernyőfelvétel a Third Party Software licenc képernyőről az egyesített telepítőben.](./media/site-recovery-add-configuration-server/combined-wiz2.png)
4. A **Regisztráció** területen válassza ki a kulcstartóból letöltött regisztrációs kulcsot.

    ![Képernyőkép az egyesített telepítő regisztrációs képernyőjéről.](./media/site-recovery-add-configuration-server/combined-wiz3.png)
5. Az **Internetbeállítások** területen adja meg, hogy a konfigurációs kiszolgálón futó Provider hogyan csatlakozzon az Azure Site Recoveryhez az interneten keresztül. Győződjön meg arról, hogy engedélyezte a szükséges URL-címeket.

    - Ha a számítógépen aktuálisan beállított proxyval szeretne csatlakozni, válassza a **csatlakozás Azure site Recovery proxykiszolgálón keresztül** lehetőséget.
    - Ha azt szeretné, hogy a szolgáltató közvetlenül kapcsolódjon, válassza a **közvetlen csatlakozás Azure site Recovery proxykiszolgáló nélkül** lehetőséget.
    - Ha a meglévő proxy hitelesítést igényel, illetve ha egyéni proxyt szeretne használni a szolgáltatói kapcsolathoz, válassza a **Csatlakozás egyéni proxybeállítások** alapján lehetőséget, és adja meg a címeket, a portokat és a hitelesítő adatokat.
     ![Képernyőfelvétel az Internet beállítások képernyőről az egyesített telepítőben.](./media/site-recovery-add-configuration-server/combined-wiz4.png)
6. Az **Előfeltételek ellenőrzése** területen a telepítő ellenőrzi, hogy a telepítés végrehajtható-e. Ha megjelenik egy figyelmeztetés a **globális időszinkron ellenőrzéséről**, ellenőrizze, hogy a rendszeróra ideje (a **Dátum és idő** beállítások) megegyeznek-e az időzónával.

    ![Képernyőfelvétel az Előfeltételek ellenőrzése képernyőről az egyesített telepítőben.](./media/site-recovery-add-configuration-server/combined-wiz5.png)
7. A **MySQL-konfiguráció** területen hozza létre a telepített MySQL-kiszolgálópéldányra való bejelentkezéshez szükséges hitelesítő adatokat.

    ![Képernyőfelvétel a MySQL konfigurációs képernyőjéről az egyesített telepítőben.](./media/site-recovery-add-configuration-server/combined-wiz6.png)
8. A **környezet részletei** területen válassza a nem lehetőséget, ha Azure stack virtuális gépeket vagy fizikai kiszolgálókat replikál. 
9. A **Telepítés helye** területen válassza ki, hová szeretné telepíteni a bináris fájlokat, és hol kívánja tárolni a gyorsítótárat. A kiválasztott meghajtón legalább 5 GB szabad lemezterületre van szükség, de javasoljuk, hogy a gyorsítótárazáshoz használt lemezen legyen legalább 600 GB szabad hely.

    ![Képernyőfelvétel a telepítési hely képernyőről az egyesített telepítőben.](./media/site-recovery-add-configuration-server/combined-wiz8.png)
10. A **hálózat kiválasztása** területen válassza ki azt a hálózati adaptert, amelyet a beépített Process Server használ a mobilitási szolgáltatás felderítéséhez és leküldéses telepítéséhez a forrásoldali gépeken, majd válassza ki azt a hálózati adaptert, amelyet az Azure-hoz való kapcsolódáshoz használ a konfigurációs kiszolgáló. A 9443-as port a replikációs forgalom küldésére és fogadására használt alapértelmezett port, ez azonban a környezeti követelményektől függően módosítható. A 9443-as port mellett a 443-as portot is megnyitjuk, amelyen keresztül egy webkiszolgáló a replikálási műveleteket vezényli. Ne használja a 443-es portot a replikációs forgalom küldésére és fogadására.

    ![Képernyőfelvétel a hálózat kiválasztása képernyőről az egyesített telepítőben.](./media/site-recovery-add-configuration-server/combined-wiz9.png)


11. Az **Összefoglalás** területen ellenőrizze az adatokat, majd kattintson a **Telepítés** gombra. A telepítés után a rendszer létrehoz egy hozzáférési kódot. Erre szüksége lesz, amikor engedélyezi a replikálást, ezért másolja le, és tárolja biztonságos helyen.

    ![Képernyőkép az egyesített telepítő összegző képernyőjéről.](./media/site-recovery-add-configuration-server/combined-wiz10.png)

A regisztráció befejeződése után a kiszolgáló megjelenik a tároló **Beállítások**  >  **kiszolgálók** paneljén.
