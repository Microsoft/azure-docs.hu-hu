---
title: A Azure AD Connect telepítési varázsló ismételt futtatása | Microsoft Docs
description: Azt ismerteti, hogyan működik a telepítővarázsló a második futtatásakor.
keywords: A Azure AD Connect telepítővarázsló lehetővé teszi a karbantartási beállítások konfigurálását a második alkalommal, amikor futtatja
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: d800214e-e591-4297-b9b5-d0b1581cc36a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/17/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d81836b47acb19f624075480aafef74c9c0934c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91306123"
---
# <a name="azure-ad-connect-sync-running-the-installation-wizard-a-second-time"></a>Azure AD Connect szinkronizálás: a telepítővarázsló második alkalommal történő futtatása
A Azure AD Connect telepítővarázsló első futtatásakor végigvezeti a telepítés konfigurálásának lépésein. Ha újra futtatja a telepítővarázslót, a karbantartási lehetőségeket kínál.

>[!IMPORTANT]
>Vegye figyelembe, hogy a telepítővarázsló nem futtatható, amíg a szinkronizálás folyamatban van.  A varázsló elindítása előtt ellenőrizze, hogy a szinkronizálás nem fut-e.

A telepítővarázsló a **Azure ad Connect** nevű Start menüben található.

![Start menü](./media/how-to-connect-installation-wizard/startmenu.png)

A telepítővarázsló indításakor a következő beállításokat tartalmazó oldal jelenik meg:

![További feladatok listáját tartalmazó oldal](./media/how-to-connect-installation-wizard/additionaltasks.png)

Ha telepítette az ADFS-t Azure AD Connect, még több lehetőség is rendelkezésre áll. Az ADFS-hez szükséges további beállítások dokumentálva vannak az [ADFS felügyeletében](how-to-connect-fed-management.md#manage-ad-fs).

Válassza ki az egyik feladatot, majd a folytatáshoz kattintson a **tovább** gombra.

> [!IMPORTANT]
> Amíg a telepítővarázsló meg van nyitva, a Szinkronizáló motor összes művelete fel lesz függesztve. Győződjön meg arról, hogy a konfigurációs módosítások elvégzése után a varázsló bezárását végzi.
>
>

## <a name="view-current-configuration"></a>Aktuális konfiguráció megtekintése
Ez a beállítás gyors áttekintést nyújt az aktuálisan konfigurált lehetőségekről.

![Az összes beállítást és azok állapotát tartalmazó oldal](./media/how-to-connect-installation-wizard/viewconfig.png)

Az **előző** gombra kattintva lépjen vissza. Ha a **Kilépés** lehetőséget választja, bezárja a telepítővarázslóját.

## <a name="customize-synchronization-options"></a>Szinkronizálási beállítások testreszabása
Ezzel a beállítással módosíthatja a szinkronizálási konfigurációt. Az egyéni konfiguráció telepítési útvonalán a beállítások egy részhalmaza jelenik meg. Ez a beállítás akkor is megjelenik, ha kezdetben az expressz telepítést használta.

* [Adjon hozzá további címtárakat](how-to-connect-install-custom.md#connect-your-directories). A címtár eltávolításával kapcsolatban lásd: [összekötő törlése](how-to-connect-sync-service-manager-ui-connectors.md#delete).
* [A tartomány és a szervezeti egység szűrésének módosítása](how-to-connect-install-custom.md#domain-and-ou-filtering).
* Csoport szűrésének eltávolítása.
* [Módosítsa a választható szolgáltatásokat](how-to-connect-install-custom.md#optional-features).

A kezdeti telepítés többi beállítása nem módosítható, és nem érhető el. Ezek a lehetőségek a következők:

* Módosítsa a userPrincipalName és a sourceAnchor használandó attribútumot.
* Módosítsa az összekapcsolási módszert különböző erdő objektumaihoz.
* Csoportházirend-alapú szűrés engedélyezése.

## <a name="refresh-directory-schema"></a>Címtár-séma frissítése
Ez a beállítás akkor használható, ha a sémát a helyszíni AD DS erdők egyikén módosította. Előfordulhat például, hogy telepítette az Exchange-et, vagy frissített egy Windows Server 2012 sémára az eszköz objektumaival. Ebben az esetben meg kell utasítania Azure AD Connect a séma újbóli beolvasásához AD DS és frissítenie kell a gyorsítótárat. Ezzel a művelettel a szinkronizálási szabályokat is újra létrehozza. Ha hozzáadja az Exchange-sémát, például az Exchange szinkronizálási szabályait a rendszer hozzáadja a konfigurációhoz.

Ha ezt a beállítást választja, a rendszer a konfigurációban lévő összes könyvtárat listázza. Megtarthatja az alapértelmezett beállítást, és frissítheti az összes erdőt, vagy törölheti a kijelölését.

![A környezetben található összes könyvtár listáját tartalmazó oldal](./media/how-to-connect-installation-wizard/refreshschema.png)

## <a name="configure-staging-mode"></a>Átmeneti üzemmód konfigurálása
Ezzel a beállítással engedélyezheti és letilthatja az átmeneti üzemmódot a kiszolgálón. További információt az átmeneti üzemmódról és annak használatáról az [Operations (műveletek](how-to-connect-sync-staging-server.md)) című témakörben találhat.

A beállítás azt mutatja, hogy jelenleg engedélyezve van-e az előkészítés, vagy le van tiltva:  
![A kikapcsolt átmeneti üzemmódot bemutató képernyőkép.](./media/how-to-connect-installation-wizard/stagingmodecurrentstate.png)

Az állapot módosításához jelölje be ezt a beállítást, majd jelölje be vagy törölje a jelölőnégyzet jelölését.  
![Az átmeneti üzemmód aktuális állapotát is megjelenítő beállítás](./media/how-to-connect-installation-wizard/stagingmodeenable.png)

## <a name="change-user-sign-in"></a>Felhasználói bejelentkezés módosítása
Ez a beállítás lehetővé teszi a felhasználói bejelentkezési módszer módosítását jelszó-kivonatos szinkronizálás, átmenő hitelesítés vagy összevonás használatával. Nem lehet módosítani a nem **konfigurálást**.

A beállítással kapcsolatos további információkért lásd: [felhasználói bejelentkezés](plan-connect-user-signin.md#changing-the-user-sign-in-method).

## <a name="next-steps"></a>Következő lépések
* További információ a Azure AD Connect Sync által használt konfigurációs modellről a [deklaratív kiépítés ismertetése](concept-azure-ad-connect-sync-declarative-provisioning.md)című témakörben található.

**Áttekintő témakörök**

* [Azure AD Connect szinkronizálás: a szinkronizálás megismerése és testreszabása](how-to-connect-sync-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)
