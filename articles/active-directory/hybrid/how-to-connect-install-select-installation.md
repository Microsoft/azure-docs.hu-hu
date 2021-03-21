---
title: 'Azure AD Connect: válassza ki a telepítési típust | Microsoft Docs'
description: Ez a témakör végigvezeti a Azure AD Connecthez használandó telepítési típus kiválasztásának módján.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7275d115210100bdd4a3a2eb683c867a6a4a4f4a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95996610"
---
# <a name="select-which-installation-type-to-use-for-azure-ad-connect"></a>Az Azure AD Connect telepítési típusának kiválasztása
A Azure AD Connect két telepítési típust tartalmaz az új telepítéshez: expressz és testre szabott. Ez a témakör segít eldönteni, hogy melyik beállítást kell használni a telepítés során.

## <a name="express"></a>Express
Az Express a leggyakoribb lehetőség, amelyet az összes új telepítés körülbelül 90%-ában használ. A szolgáltatás úgy lett kialakítva, hogy olyan konfigurációt biztosítson, amely a leggyakoribb felhasználói forgatókönyvekhez használható.

A következőket feltételezi:

- A helyszínen egyetlen Active Directory erdő található.
- Rendelkezik egy vállalati rendszergazdai fiókkal, amelyet a telepítéshez használhat.
- A helyszíni Active Directory kevesebb mint 100 000 objektummal rendelkezik.

A következőket kapja:

- [Jelszó-kivonat szinkronizálása](how-to-connect-password-hash-synchronization.md) a helyszínről az Azure ad-be az egyszeri bejelentkezéshez.
- Egy olyan konfiguráció, amely szinkronizálja a [felhasználókat, a csoportokat, a névjegyeket és a Windows 10 rendszerű számítógépeket](concept-azure-ad-connect-sync-default-configuration.md).
- Minden jogosult objektum szinkronizálása az összes tartományban és az összes szervezeti egységben.
- Az [automatikus frissítés](how-to-connect-install-automatic-upgrade.md) lehetővé teszi, hogy mindig a legújabb elérhető verziót használja.

Lehetőségek, ahol továbbra is használhatja az Expresst:

- Ha nem kívánja szinkronizálni az összes szervezeti egységet, továbbra is használhatja az Express-et, és az utolsó oldalon a kijelölés megszüntetése * * elindíthatja a szinkronizálási folyamatot... * * *. Ezután futtassa újra a telepítővarázslót, és módosítsa a szervezeti egységeket a [konfigurációs beállításokban](how-to-connect-installation-wizard.md#customize-synchronization-options) , és engedélyezze az ütemezett szinkronizálást.
- Engedélyezni szeretné a prémium szintű Azure AD egyik funkcióját, például a jelszó visszaírási. Első lépésként fejezze be az Expresst a kezdeti telepítés befejezéséhez. Ezután futtassa újra a telepítővarázslót, és módosítsa a [konfigurációs beállításokat](how-to-connect-installation-wizard.md#customize-synchronization-options).

## <a name="custom"></a>Egyéni
A testreszabott elérési út több lehetőséget is lehetővé tesz, mint az Express. Ezt minden olyan esetben fel kell használni, ahol az Express esetében az előző szakaszban leírt konfiguráció nem jellemző a szervezet számára.

A következő esetekben használja:

- Nincs hozzáférése Active Directory vállalati rendszergazdai fiókhoz.
- Több erdővel rendelkezik, vagy a későbbiekben több erdő szinkronizálását tervezi.
- Az erdőben lévő tartományok nem érhetők el a csatlakozási kiszolgálóról.
- Az összevonás vagy átmenő hitelesítés használatát tervezi a felhasználói bejelentkezéshez.
- Több mint 100 000 objektummal rendelkezik, és teljes SQL Servert kell használnia.
- A Group-alapú szűrést érdemes használni, nem csak a tartomány-vagy OU-alapú szűrést.

## <a name="upgrade-from-dirsync"></a>Frissítés a DirSync szolgáltatásról
Ha jelenleg használja az rSync-et, kövesse a [frissítés az rsync](how-to-dirsync-upgrade-get-started.md) használatával a meglévő konfiguráció frissítéséhez című szakasz lépéseit. Két különböző frissítési lehetőség létezik:

- Helyben történő frissítéssel telepítse a kapcsolódást ugyanarra a kiszolgálóra.
- Párhuzamos üzembe helyezés a kapcsolódás új kiszolgálón való telepítéséhez, miközben a meglévő rSync-kiszolgáló továbbra is működőképes.

## <a name="upgrade-from-azure-ad-sync"></a>Frissítés Azure AD-szinkronizálóról
Ha jelenleg Azure AD-szinkronizáló használ, akkor [ugyanazokat a lépéseket](how-to-upgrade-previous-version.md) követheti, mint amikor az egyik összekapcsolási verzióról egy újabbra frissít. Két különböző frissítési lehetőség létezik:

- Helyben történő frissítéssel telepítse a kapcsolódást ugyanarra a kiszolgálóra.
- Swing – Migrálás az új kiszolgálón való kapcsolódásra, miközben a meglévő Azure AD-szinkronizáló-kiszolgáló továbbra is működőképes.

## <a name="migrate-from-fim2010-or-mim2016"></a>Áttelepítés FIM2010 vagy MIM2016
Ha jelenleg a Forefront Identity Manager 2010-es vagy Microsoft Identity Manager 2016-es verzióját használja az Azure AD-összekötővel, az egyetlen lehetőség az áttelepítés. Kövesse a [swing-Migration](how-to-upgrade-previous-version.md#swing-migration)című témakörben leírt lépéseket. A lépéseket követve cserélje le a Azure AD-szinkronizálót a FIM2010/MIM2016.

## <a name="next-steps"></a>Következő lépések
Attól függően, hogy melyik beállítást választotta, a bal oldali tartalomjegyzék használatával megtalálhatja a részletes lépéseket tartalmazó cikket.
