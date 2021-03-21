---
title: 'Azure AD Connect Sync: a ADSync-szolgáltatásfiók módosítása | Microsoft Docs'
description: Ez a témakör a titkosítási kulcsot ismerteti, valamint azt, hogy miként lehet azt lemondani a jelszó módosítása után.
services: active-directory
keywords: Azure AD Sync Service-fiók, jelszó
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 76b19162-8b16-4960-9e22-bd64e6675ecc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4dcc7ed6076c3bac723d709f50f1b3ab2ce8f58
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95996559"
---
# <a name="changing-the-adsync-service-account-password"></a>A ADSync-szolgáltatásfiók jelszavának módosítása
Ha módosítja a ADSync-szolgáltatásfiók jelszavát, a szinkronizálási szolgáltatás nem fog megfelelően elindulni, amíg el nem hagyta a titkosítási kulcsot, és újra nem inicializálta a ADSync-szolgáltatásfiók jelszavát. 

Azure AD Connect a szinkronizálási szolgáltatások részeként egy titkosítási kulcsot használ a AD DS Connector-fiók és a ADSync-szolgáltatásfiók jelszavának tárolására.  Ezek a fiókok titkosítva vannak, mielőtt azokat a rendszer az adatbázisban tárolja. 

A használt titkosítási kulcs [védelme a Windows adatvédelem (DPAPI)](/previous-versions/ms995355(v=msdn.10))használatával történik. A DPAPI a **AdSync-szolgáltatásfiók** használatával védi a titkosítási kulcsot. 

Ha módosítania kell a szolgáltatásfiók jelszavát, használhatja a [AdSync-szolgáltatásfiók titkosítási kulcsának elhagyása](#abandoning-the-adsync-service-account-encryption-key) a következő eljárásokkal való elvégzéséhez szükséges eljárásokat.  Ezeket az eljárásokat akkor is érdemes használni, ha valamilyen okból le kell hagyni a titkosítási kulcsot.

## <a name="issues-that-arise-from-changing-the-password"></a>A jelszó módosításakor felmerülő problémák
A szolgáltatásfiók jelszavának módosításakor két dolgot kell végrehajtania.

Először módosítania kell a jelszót a Windows szolgáltatásvezérlő kezelőjében.  A probléma megoldása előtt a következő hibák jelennek meg:


- Ha a Windows szolgáltatásvezérlő kezelőjében megpróbálja elindítani a szinkronizálási szolgáltatást, a következő hibaüzenet jelenik meg: "a **Windows nem tudja elindítani a Microsoft Azure ad Sync szolgáltatást a helyi számítógépen**". **1069-es hiba: a szolgáltatás bejelentkezési hiba miatt nem indult el.**"
- A Windows Eseménynapló alatt a rendszer eseménynaplója hibát tartalmaz a 7038-es azonosítójú **eseménnyel** és az üzenettel kapcsolatban **: "a AdSync szolgáltatás nem tudott bejelentkezni a jelenleg konfigurált jelszóval a következő hiba miatt: a Felhasználónév vagy a jelszó helytelen.**"

Másodszor, ha a jelszó frissül, a szinkronizálási szolgáltatás a DPAPI használatával már nem tudja lekérni a titkosítási kulcsot. A titkosítási kulcs nélkül a szinkronizációs szolgáltatás nem tudja visszafejteni a helyszíni AD-hez és az Azure AD-hoz való szinkronizáláshoz szükséges jelszavakat.
A következő hibákat fogja látni:

- Ha a Windows szolgáltatásvezérlő szolgáltatásban megpróbálja elindítani a szinkronizálási szolgáltatást, és nem tudja beolvasni a titkosítási kulcsot, a következő hibaüzenet jelenik meg: "a<strong>Windows nem tudta elindítani a Microsoft Azure ad-szinkronizálást a helyi számítógépen. További információkért tekintse át a rendszer eseménynaplóját. Ha ez egy nem Microsoft-szolgáltatás, forduljon a szolgáltatás forgalmazójához, és tekintse meg a Service-specifikus hibakódot – 21451857952</strong>. "
- A Windows Eseménynapló alatt az alkalmazás eseménynaplója hibát tartalmaz a 6028-es azonosítójú **eseménynél** , és hibaüzenet jelenik meg: *"a kiszolgáló titkosítási kulcsa nem érhető el."*

Annak érdekében, hogy ne kapja meg ezeket a hibákat, kövesse a [AdSync-szolgáltatásfiók titkosítási kulcsának elhagyása](#abandoning-the-adsync-service-account-encryption-key) a jelszó módosításakor című témakörben leírt eljárásokat.
 
## <a name="abandoning-the-adsync-service-account-encryption-key"></a>A ADSync szolgáltatás fiókjának titkosítási kulcsának megszüntetése
>[!IMPORTANT]
>Az alábbi eljárások csak Azure AD Connect build 1.1.443.0 vagy régebbi verzióra vonatkoznak. Ezt nem lehet a Azure AD Connect újabb verzióihoz használni, mert a titkosítási kulcsot az Azure AD kapcsolódása kezeli, ha módosítja az AD Sync szolgáltatás fiókjának jelszavát, így a következő lépések nem szükségesek az újabb verziókban.   

A titkosítási kulcs megszüntetéséhez kövesse az alábbi eljárásokat.

### <a name="what-to-do-if-you-need-to-abandon-the-encryption-key"></a>Mi a teendő, ha le kell hagyni a titkosítási kulcsot

Ha el kell hagynia a titkosítási kulcsot, a következő eljárásokkal hajthatja végre ezt a műveletet.

1. [A szinkronizálási szolgáltatás leállítása](#stop-the-synchronization-service)

1. [A meglévő titkosítási kulcs lemondása](#abandon-the-existing-encryption-key)

2. [Adja meg az AD DS Connector-fiók jelszavát](#provide-the-password-of-the-ad-ds-connector-account)

3. [A ADSync-szolgáltatásfiók jelszavának újrainicializálása](#reinitialize-the-password-of-the-adsync-service-account)

4. [A szinkronizálási szolgáltatás elindítása](#start-the-synchronization-service)

#### <a name="stop-the-synchronization-service"></a>A szinkronizálási szolgáltatás leállítása
Először le is állíthatja a szolgáltatást a Windows szolgáltatásvezérlő kezelőjében.  Győződjön meg arról, hogy a szolgáltatás nem fut a leállítási kísérlet során.  Ha igen, várjon, amíg befejeződik, majd állítsa le.


1. Nyissa meg a Windows Service Control Managert (START → szolgáltatások).
2. Válassza a **Microsoft Azure ad szinkronizálás** lehetőséget, majd kattintson a Leállítás gombra.

#### <a name="abandon-the-existing-encryption-key"></a>A meglévő titkosítási kulcs lemondása
Hagyjon le egy meglévő titkosítási kulcsot, hogy az új titkosítási kulcs hozható létre:

1. Jelentkezzen be a Azure AD Connect-kiszolgálóra rendszergazdaként.

2. Indítson el egy új PowerShell-munkamenetet.

3. Navigáljon a mappához: `'$env:ProgramFiles\Microsoft Azure AD Sync\bin\'`

4. Futtassa a parancsot: `./miiskmu.exe /a`

![A parancs futtatása után a PowerShellt bemutató képernyőkép.](./media/how-to-connect-sync-change-serviceacct-pass/key5.png)

#### <a name="provide-the-password-of-the-ad-ds-connector-account"></a>Adja meg az AD DS Connector-fiók jelszavát
Mivel az adatbázisban tárolt meglévő jelszavakat már nem lehet visszafejteni, meg kell adnia a szinkronizálási szolgáltatást az AD DS-összekötő fiókjának jelszavával. A szinkronizálási szolgáltatás titkosítja a jelszavakat az új titkosítási kulccsal:

1. Indítsa el a Synchronization Service Managert (START → szinkronizációs szolgáltatás).
</br>![Service Manager szinkronizálása](./media/how-to-connect-sync-change-serviceacct-pass/startmenu.png)  
2. Nyissa meg az **Összekötők** lapot.
3. Válassza ki az **ad-összekötőt** , amely megfelel a helyszíni ad-nek. Ha egynél több AD-összekötővel rendelkezik, ismételje meg az alábbi lépéseket mindegyiknél.
4. A **műveletek** területen válassza a **Tulajdonságok** lehetőséget.
5. Az előugró párbeszédpanelen válassza a **kapcsolódás Active Directory erdőhöz** lehetőséget:
6. Adja meg a **jelszó** szövegmezőben a AD DS fiók jelszavát. Ha nem ismeri a jelszavát, a lépés végrehajtása előtt be kell állítania egy ismert értéket.
7. Az új jelszó mentéséhez és az előugró ablak bezárásához kattintson **az OK** gombra.
![A "tulajdonságok" ablak "kapcsolódás Active Directory erdőhöz" lapját bemutató képernyőkép.](./media/how-to-connect-sync-change-serviceacct-pass/key6.png)

#### <a name="reinitialize-the-password-of-the-adsync-service-account"></a>A ADSync-szolgáltatásfiók jelszavának újrainicializálása
Közvetlenül nem adhatja meg az Azure AD-szolgáltatásfiók jelszavát a szinkronizálási szolgáltatás számára. Ehelyett az **Add-ADSyncAADServiceAccount** parancsmagot kell használnia az Azure ad-szolgáltatásfiók újrainicializálásához. A parancsmag visszaállítja a fiók jelszavát, és elérhetővé teszi a szinkronizálási szolgáltatás számára:

1. Indítson el egy új PowerShell-munkamenetet a Azure AD Connect-kiszolgálón.
2. Futtassa a parancsmagot `Add-ADSyncAADServiceAccount` .
3. Az előugró ablakban adja meg az Azure ad-bérlő globális rendszergazdai hitelesítő adatait.
![Azure AD Connect Sync encryption Key segédprogram](./media/how-to-connect-sync-change-serviceacct-pass/key7.png)
4. Ha a művelet sikeres, megjelenik a PowerShell parancssora.

#### <a name="start-the-synchronization-service"></a>A szinkronizálási szolgáltatás elindítása
Most, hogy a szinkronizálási szolgáltatásnak van hozzáférése a titkosítási kulcshoz és az összes szükséges jelszóhoz, újraindíthatja a szolgáltatást a Windows szolgáltatásvezérlő kezelőjében:


1. Nyissa meg a Windows Service Control Managert (START → szolgáltatások).
2. Válassza a **Microsoft Azure ad szinkronizálás** lehetőséget, majd kattintson az Újraindítás gombra.

## <a name="next-steps"></a>Következő lépések
**Áttekintő témakörök**

* [Azure AD Connect szinkronizálás: a szinkronizálás megismerése és testreszabása](how-to-connect-sync-whatis.md)

* [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)
