---
title: A StorSimple Eszközkezelő Service üzembe helyezése | Microsoft Docs
description: A cikk azt ismerteti, hogyan lehet létrehozni és törölni a StorSimple Eszközkezelő szolgáltatást a Azure Portalban, és ismerteti a szolgáltatás regisztrációs kulcsának kezelését.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 28499494-8c4d-4a7f-9d44-94b2b8a93c93
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: 92041be4d3d5ef4d980c17fa0a5870b740c1784a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95995009"
---
# <a name="deploy-the-storsimple-device-manager-service-for-storsimple-virtual-array"></a>A StorSimple Eszközkezelő szolgáltatás üzembe helyezése a StorSimple virtuális tömbben

## <a name="overview"></a>Áttekintés

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

A StorSimple Eszközkezelő szolgáltatás Microsoft Azure fut, és több StorSimple-eszközhöz csatlakozik. A szolgáltatás létrehozása után a segítségével felügyelheti az eszközöket a böngészőben futó Microsoft Azure Portal. Ez lehetővé teszi a StorSimple Eszközkezelő szolgáltatáshoz kapcsolódó összes eszköz figyelését egyetlen központi helyről, így minimalizálva az adminisztrációs terheket.

A StorSimple Eszközkezelő szolgáltatással kapcsolatos gyakori feladatok a következők:

* Szolgáltatás létrehozása
* Szolgáltatás törlése
* Szolgáltatásregisztrációs kulcs lekérése
* A szolgáltatás regisztrációs kulcsának újbóli előállítása

Ez az oktatóanyag az előző feladatok végrehajtását ismerteti. A cikkben szereplő információk csak StorSimple virtuális tömbökre alkalmazhatók. A StorSimple 8000 sorozattal kapcsolatos további információkért látogasson el a [StorSimple Manager szolgáltatás üzembe helyezése](./storsimple-8000-manage-service.md)című témakörre.

## <a name="create-a-service"></a>Szolgáltatás létrehozása

Szolgáltatás létrehozásához a következőkre van szükség:

* Előfizetés Nagyvállalati Szerződés
* Aktív Microsoft Azure Storage-fiók
* A hozzáférés-kezeléshez használt számlázási adatok

Azt is megteheti, hogy létrehoz egy Storage-fiókot a szolgáltatás létrehozásakor.

Egyetlen szolgáltatás több eszközt is kezelhet. Az eszközök azonban nem terjedhetnek ki több szolgáltatásra. A nagyméretű vállalatok több szolgáltatási példánnyal is dolgozhatnak különböző előfizetésekkel, szervezetekkel vagy akár üzembe helyezési helyekkel.

> [!NOTE]
> A StorSimple 8000 sorozatú eszközök és a StorSimple virtuális tömbök felügyeletéhez külön StorSimple Eszközkezelő szolgáltatásra van szükség.


Egy szolgáltatás létrehozásához hajtsa végre az alábbi lépéseket.

[!INCLUDE [storsimple-virtual-array-create-new-service](../../includes/storsimple-virtual-array-create-new-service.md)]

## <a name="delete-a-service"></a>Szolgáltatás törlése

A szolgáltatás törlése előtt győződjön meg arról, hogy nem használja a csatlakoztatott eszközöket. Ha a szolgáltatás használatban van, inaktiválja a csatlakoztatott eszközöket. Az inaktiválási művelet megszakítja a kapcsolatot az eszköz és a szolgáltatás között, de megőrzi az eszközön tárolt összes eszközt a felhőben.

> [!IMPORTANT]
> A szolgáltatás törlése után a művelet nem vonható vissza. A szolgáltatást használó összes eszköznek gyári visszaállítást kell végeznie, mielőtt más szolgáltatással is használható lenne. Ebben a forgatókönyvben az eszközön található helyi adategységek és a konfiguráció is elvész.
 

A szolgáltatás törléséhez hajtsa végre az alábbi lépéseket.

#### <a name="to-delete-a-service"></a>Szolgáltatás törlése

1. Lépjen a **Minden erőforrás** menüpontra. Keressen rá a StorSimple Eszközkezelő szolgáltatásra. Válassza ki a törölni kívánt szolgáltatást.
   
    ![Válassza ki a törlendő szolgáltatást](./media/storsimple-virtual-array-manage-service/deleteservice2.png)
2. Lépjen a szolgáltatás irányítópultra, és győződjön meg arról, hogy nincsenek csatlakoztatott eszközök a szolgáltatáshoz. Ha nincsenek regisztrálva eszközök a szolgáltatásban, a rendszer egy szalagcím-üzenetet is megjelenít a hatáshoz. Kattintson a **Törlés** gombra.
   
    ![Szolgáltatás törlése](./media/storsimple-virtual-array-manage-service/deleteservice3.png)

3. Ha a rendszer megerősítést kér, kattintson az **Igen** gombra a megerősítő értesítésben. 
   
    ![Szolgáltatás törlésének megerősítése](./media/storsimple-virtual-array-manage-service/deleteservice4.png)
4. A szolgáltatás törléséhez néhány percet is igénybe vehet. A szolgáltatás sikeres törlése után értesítést kap.
   
    ![Szolgáltatás sikeres törlése](./media/storsimple-virtual-array-manage-service/deleteservice6.png)

A szolgáltatások listája frissülni fog.

 ![A szolgáltatások frissített listája](./media/storsimple-virtual-array-manage-service/deleteservice7.png)

## <a name="get-the-service-registration-key"></a>Szolgáltatásregisztrációs kulcs lekérése
Miután sikeresen létrehozott egy szolgáltatást, regisztrálnia kell a StorSimple-eszközt a szolgáltatásban. Az első StorSimple-eszköz regisztrálásához szüksége lesz a szolgáltatás regisztrációs kulcsára. Ha további eszközöket szeretne regisztrálni egy meglévő StorSimple-szolgáltatással, akkor a regisztrációs kulcsra és a szolgáltatás adattitkosítási kulcsára is szüksége lesz (amely az első eszközön jön létre a regisztráció során). A szolgáltatási adatok titkosítási kulcsával kapcsolatos további információkért lásd: [StorSimple biztonság](./storsimple-8000-security.md). A regisztrációs kulcsot a szolgáltatás **kulcsok** paneljén érheti el.

A szolgáltatás regisztrációs kulcsának beszerzéséhez hajtsa végre a következő lépéseket.

#### <a name="to-get-the-service-registration-key"></a>A szolgáltatás regisztrációs kulcsának beszerzése
1. A **StorSimple Eszközkezelő** panelen lépjen a **felügyeleti &gt;** **kulcsok** elemre.
   
   ![Kulcsok panel](./media/storsimple-virtual-array-manage-service/getregkey2.png)
2. A **kulcsok** panelen megjelenik a szolgáltatás regisztrációs kulcsa. Másolja a regisztrációs kulcsot a másolás ikon használatával. 

A szolgáltatás regisztrációs kulcsának biztonságos helyen kell maradnia. Erre a kulcsra, valamint a szolgáltatás adattitkosítási kulcsára lesz szüksége a szolgáltatáshoz tartozó további eszközök regisztrálásához. A szolgáltatás regisztrációs kulcsának beszerzése után a Windows PowerShell StorSimple-bővítménye felületen keresztül kell konfigurálnia az eszközt.

## <a name="regenerate-the-service-registration-key"></a>A szolgáltatás regisztrációs kulcsának újbóli előállítása
Ha el kell végeznie a kulcs elforgatását, vagy ha a szolgáltatás-rendszergazdák listája módosult, újra kell létrehoznia a szolgáltatás regisztrációs kulcsát. A kulcs újralétrehozásakor az új kulcs csak a további eszközök regisztrálására szolgál. A folyamat nem érinti a már regisztrált eszközöket.

A szolgáltatás regisztrációs kulcsának újralétrehozásához hajtsa végre az alábbi lépéseket.

#### <a name="to-regenerate-the-service-registration-key"></a>A szolgáltatás regisztrációs kulcsának előállítása
1. A **StorSimple Eszközkezelő** panelen lépjen a **felügyeleti &gt;** **kulcsok** elemre.
   
   ![Kulcsok panel újra](./media/storsimple-virtual-array-manage-service/getregkey2.png)
2. A **kulcsok** panelen kattintson az **újragenerálás** elemre.
   
   ![Kattintson az újragenerált elemre.](./media/storsimple-virtual-array-manage-service/getregkey5.png)
3. A **szolgáltatás regisztrációs kulcsának újragenerálása** panelen tekintse át a kulcsok újragenerálása esetén szükséges műveletet. A szolgáltatásban regisztrált összes további eszköz az új regisztrációs kulcsot fogja használni. A megerősítéshez kattintson az **újbóli létrehozás** gombra. A regisztráció befejezését követően értesítést kap.
   
   ![Kulcs újralétrehozásának megerősítése](./media/storsimple-virtual-array-manage-service/getregkey3.png)
4. Ekkor megjelenik egy új szolgáltatás regisztrációs kulcsa.
   
    ![További újragenerált kulcs megerősítése](./media/storsimple-virtual-array-manage-service/getregkey4.png)
   
   Másolja ezt a kulcsot, és mentse a szolgáltatáshoz tartozó új eszközök regisztrálásához.

## <a name="next-steps"></a>Következő lépések
* Megtudhatja, hogyan [kezdheti](storsimple-virtual-array-deploy1-portal-prep.md) meg a StorSimple virtuális tömböket.
* Ismerje meg [a StorSimple-eszköz felügyeletét](storsimple-ova-web-ui-admin.md).