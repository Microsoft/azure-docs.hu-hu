---
title: Frissítés telepítése Azure Data Box Gateway Series-eszközön | Microsoft Docs
description: Ismerteti, hogyan alkalmazhatók a frissítések a Azure Portal és a helyi webes KEZELŐFELÜLET használatával Azure Data Box Gateway Series-eszközhöz
services: databox
author: alkohli
ms.service: databox
ms.topic: article
ms.date: 10/14/2020
ms.author: alkohli
ms.openlocfilehash: 7fda10310a4bac085b248248d80d708dfa7f3ff7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96582187"
---
# <a name="update-your-azure-data-box-gateway"></a>A Azure Data Box Gateway frissítése

Ez a cikk azokat a lépéseket ismerteti, amelyek szükségesek a frissítés telepítéséhez a Azure Data Box Gateway a helyi webes felületen és a Azure Portalon keresztül. A szoftverfrissítések vagy gyorsjavítások alkalmazásával a Data Box Gateway-eszköz naprakészen tartható.

> [!IMPORTANT]
>
> - Az **1911** -es frissítés megfelel az eszközön **1.6.1049.786** szoftver verziójának. A frissítéssel kapcsolatos további információkért nyissa meg a [kibocsátási megjegyzéseket](data-box-gateway-1911-release-notes.md).
>
> - Ne feledje, hogy egy frissítés vagy gyorsjavítás telepítése újraindítja az eszközt. Mivel a Data Box Gateway egy egyetlen csomópontos eszköz, a folyamatban lévő I/O-műveletek megszakadnak, és az eszköz akár 30 percig is elérhetetlen lesz, amíg a szoftverfrissítés tart.

Ezeket a lépéseket az alábbi szakaszokban ismertetjük.

## <a name="use-the-azure-portal"></a>Az Azure Portal használata

Javasoljuk, hogy telepítse a frissítéseket a Azure Portalon keresztül. Az eszköz naponta egyszer automatikusan frissítéseket keres. Ha a frissítések elérhetők, megjelenik egy értesítés a portálon. Ezt követően letöltheti és telepítheti a frissítéseket.

> [!NOTE]
> Győződjön meg arról, hogy az eszköz kifogástalan állapotban van, és az állapota **online** állapotba kerül, mielőtt folytatná a frissítések telepítését.

1. Ha a frissítések elérhetők az eszközhöz, egy értesítés jelenik meg. Válassza ki az értesítést, vagy a felső menüsorban **frissítse az eszközt**. Ez lehetővé teszi az eszköz szoftverfrissítések alkalmazását.

    ![Képernyőkép a Azure Data Box Gateway kezdőlapról az eszköz áttekintésével és frissítésével kapcsolatban.](./media/data-box-gateway-apply-updates/portal-apply-update-01a.png)

2. A **Device Updates (eszközök frissítése** ) panelen győződjön meg arról, hogy áttekintette a kibocsátási megjegyzések új szolgáltatásaihoz társított licencfeltételeket.

    Választhat, hogy **letölti és telepíti** a frissítéseket, vagy csak **letölti** a frissítéseket. A letöltött frissítések telepítését későbbre halaszthatja.

    ![Képernyőkép – az eszköz frissítései párbeszédpanel, az elfogadás és a megismerés lehetőséggel, valamint a letöltés és telepítés lehetőséggel.](./media/data-box-gateway-apply-updates/portal-apply-update-02.png)

    Ha le szeretné tölteni és telepíteni kívánja a frissítéseket, ellenőrizze, hogy a frissítések telepítése automatikusan megtörtént-e a letöltés befejeződése után.

    ![Képernyőfelvétel: az eszköz frissítései párbeszédpanel, az elfogadás lehetőséggel és a letöltési lehetőséggel.](./media/data-box-gateway-apply-updates/portal-apply-update-03.png)

3. A frissítések letöltése megkezdődik. Ekkor megjelenik egy értesítés arról, hogy a letöltés folyamatban van.

    ![Képernyőkép a következőről: "frissítések letöltése és telepítése folyamatban."](./media/data-box-gateway-apply-updates/portal-apply-update-05.png)

    Egy értesítési szalagcím is megjelenik a Azure Portalban. Ez a letöltési folyamatra utal.

    ![Képernyőkép a Azure Data Box Gateway kezdőlapról az Áttekintés lehetőséggel és a frissítések értesítési szalagcímének letöltésével foglalkozó oldalon.](./media/data-box-gateway-apply-updates/portal-apply-update-08a.png)

    Kiválaszthatja ezt az értesítést, vagy az **eszköz frissítése** lehetőség kiválasztásával megtekintheti a frissítés részletes állapotát.

    ![Képernyőkép a frissítések letöltése és telepítése párbeszédpanel állapotáról: folyamatban lévő üzenet és a letöltési frissítések üzenet.](./media/data-box-gateway-apply-updates/portal-apply-update-09.png)

4. A letöltés befejezését követően az értesítési szalagcím frissíti a befejezést. Ha úgy dönt, hogy letölti és telepíti a frissítéseket, a telepítés automatikusan elindul.

    ![Képernyőkép a Azure Data Box Gateway kezdőlapról az Áttekintés lehetőséggel és a letöltött frissítések értesítési szalagcímével.](./media/data-box-gateway-apply-updates/portal-apply-update-10a.png)

    Ha úgy döntött, hogy csak a frissítések letöltését választotta, válassza ki az értesítést az **eszköz frissítései** panel megnyitásához. Válassza a **Telepítés** gombot.
  
    ![Képernyőkép – az eszköz frissítései párbeszédpanel, ahol a telepítési lehetőség látható.](./media/data-box-gateway-apply-updates/portal-apply-update-11a.png)

5. Ekkor megjelenik egy értesítés arról, hogy a telepítés folyamatban van.

    ![Képernyőkép a Azure Data Box Gateway kezdőlapról az Áttekintés lehetőséggel, valamint a letöltési és telepítési folyamatjelző üzenetből.](./media/data-box-gateway-apply-updates/portal-apply-update-12a.png)

    A portál egy tájékoztató riasztást is megjelenít, amely jelzi, hogy a telepítés folyamatban van. <!-- The device goes offline and is in maintenance mode.-->

    <!-- ![Software version after update](./media/data-box-gateway-apply-updates/update-13.png)-->

6. Mivel ez egy 1 csomópontos eszköz, az eszköz a frissítések telepítése után újra fog indulni. Az újraindítás során a kritikus riasztás azt jelzi, hogy az eszköz szívverése megszakadt.

    ![Képernyőkép a Azure Data Box Gateway kezdőlapról az Áttekintés lehetőséggel és a kritikus riasztási értesítési szalagcímtel.](./media/data-box-gateway-apply-updates/portal-apply-update-19a.png)

    Válassza ki a riasztást a megfelelő eszköz eseményének megtekintéséhez.

    ![Képernyőfelvétel: az eszköz eseményei szakasz, amely az eszköz kinevezett eseményének elveszett szívverését tartalmazza.](./media/data-box-gateway-apply-updates/portal-apply-update-20a.png)

7. A frissítések telepítése után az eszköz állapota **online** állapotra frissül.

    ![Képernyőkép a Azure Data Box Gateway kezdőlapról az Áttekintés lehetőséggel és az online állapottal.](./media/data-box-gateway-apply-updates/portal-apply-update-23a.png)

    A felső menüsávban válassza az **eszközök frissítései** lehetőséget. Ellenőrizze, hogy a frissítés telepítése sikeres volt-e, és hogy az eszköz szoftverének verziója megfelel-e.

    ![Képernyőkép – az eszköz frissítései párbeszédpanel, ahol a telepített szoftververzió szakasza látható.](./media/data-box-gateway-apply-updates/portal-apply-update-24.png)

## <a name="use-the-local-web-ui"></a>A helyi webes felhasználói felület használata

A helyi webes felhasználói felület használata két lépésből áll:

- A frissítés vagy a gyorsjavítás letöltése
- A frissítés vagy a gyorsjavítás telepítése

Ezeket a lépéseket a következő szakaszokban részletesen ismertetjük.

### <a name="download-the-update-or-the-hotfix"></a>A frissítés vagy a gyorsjavítás letöltése

A frissítés letöltéséhez hajtsa végre a következő lépéseket. A frissítést a Microsoft által biztosított helyről vagy a Microsoft Update katalógusból töltheti le.

Az alábbi lépések végrehajtásával töltse le a frissítést a Microsoft Update-katalógusból.

1. Indítsa el a böngészőt, és navigáljon a gombra [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com) .

   ![Képernyőkép a Microsoft Update-katalógusról egy böngészőablakban, Data Box Gateway Begépelte a keresés szövegmezőbe. A böngésző címsorát és a keresési szövegmezőt is meghívjuk.](./media/data-box-gateway-apply-updates/download-update-1.png)

2. A Microsoft Update-katalógus keresőmező mezőjébe írja be a letölteni kívánt frissítés gyorsjavításának vagy használati feltételeinek a tudásbázisát (KB). Írja be például a következőt: **Azure Data Box Gateway**, majd kattintson a **Keresés** gombra.

   A frissítési lista **Azure Data Box Gateway 1911**-as értékkel jelenik meg.

   ![Képernyőkép a Microsoft Update-katalógusról egy böngészőablakban, amelyen Data Box Gateway keresési eredmények jelennek meg és felhívhatók.](./media/data-box-gateway-apply-updates/download-update-2.png)

3. Válassza a **Letöltés** lehetőséget. Egyetlen fájl tölthető le, amely megfelel az eszköz szoftverfrissítés-frissítésének *SoftwareUpdatePackage.exe* . Töltse le a fájlt a helyi rendszer egyik mappájába. A mappát átmásolhatja egy olyan hálózati megosztásra is, amely elérhető az eszközről.

   ![A Download (letöltés) párbeszédpanel képernyőképe, amelyen a szoftverfrissítési csomag pont E X E fájljának hivatkozása látható.](./media/data-box-gateway-apply-updates/download-update-3.png)

### <a name="install-the-update-or-the-hotfix"></a>A frissítés vagy a gyorsjavítás telepítése

A frissítés vagy a gyorsjavítás telepítése előtt győződjön meg az alábbiakról:

- A frissítés vagy a gyorsjavítás helyileg, a gazdagépen vagy egy hálózati megosztáson keresztül érhető el.
- Az eszköz állapota Kifogástalan, ahogy a helyi webes felhasználói felület **Áttekintés** lapján látható.

   ![Képernyőfelvétel a Data Box Gateway helyi web U I és az irányítópult lehetőség és a szoftver állapota: kifogástalan állapotú üzenet.](./media/data-box-gateway-apply-updates/local-ui-update-1.png)

Az eljárás végrehajtása körülbelül 20 percet vesz igénybe. A frissítés vagy a gyorsjavítás telepítéséhez hajtsa végre a következő lépéseket.

1. A helyi webes kezelőfelületen lépjen a **karbantartási**  >  **szoftverfrissítés** elemre. Jegyezze fel a futtatott szoftver verzióját.

   ![Képernyőkép a Data Box Gateway helyi web U I és a szoftverfrissítés lehetőségről, valamint az aktuális szoftververzió-üzenetről.](./media/data-box-gateway-apply-updates/local-ui-update-2.png)

2. Adja meg a frissítési fájl elérési útját. Ha hálózati megosztásra helyezi, tallózással is megkeresheti a frissítési telepítési fájlt. Válassza ki *SoftwareUpdatePackage.exe* utótaggal rendelkező szoftverfrissítési fájlt.

   ![A fájlkezelő képernyőképe a szoftverfrissítési csomag pont E X E fájljának kihívásával.](./media/data-box-gateway-apply-updates/local-ui-update-3.png)

3. Kattintson az **Alkalmaz** gombra.

   ![Képernyőfelvétel a Data Box Gateway helyi web U I és a szoftverfrissítési lehetőség, a fájl frissítése elérési útja szövegmezőben, valamint az elnevezett frissítés alkalmazása lehetőség.](./media/data-box-gateway-apply-updates/local-ui-update-4.png)

4. Ha a rendszer megerősítést kér, kattintson az **Igen** gombra a folytatáshoz. Mivel az eszköz egyetlen csomópontos eszköz, a frissítés alkalmazása után az eszköz újraindul, és leállási idő van.

   ![Képernyőkép a szoftverfrissítés párbeszédpanelről, és az igen lehetőség van kiemelve.](./media/data-box-gateway-apply-updates/local-ui-update-5.png)

5. A frissítés elindul. Az eszköz sikeres frissítése után újraindul. A helyi felhasználói felület ebben az időtartamban nem érhető el.

6. Az újraindítás után a rendszer a **bejelentkezési** oldalra kerül. Annak ellenőrzéséhez, hogy az eszköz szoftvere frissítve lett-e, a helyi webes felületen lépjen a **karbantartási**  >  **szoftverfrissítés** lapra. Ebben a példában a **1.6.1049.786** jelenik meg.

   ![Képernyőfelvétel a Data Box Gateway helyi web U I és a szoftverfrissítési lehetőség, valamint a frissített aktuális szoftververzió üzenet.](./media/data-box-gateway-apply-updates/local-ui-update-6.png)

## <a name="next-steps"></a>Következő lépések

További információ [a Azure Data Box Gateway felügyeletéről](data-box-gateway-manage-users.md).
