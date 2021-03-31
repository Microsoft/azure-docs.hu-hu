---
title: 'Azure AD Connect: az objektum-szinkronizálás hibáinak megoldása | Microsoft Docs'
description: Ez a témakör azt ismerteti, hogyan lehet elhárítani az objektumok szinkronizálásával kapcsolatos problémákat a hibaelhárítási feladat használatával.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 73d4239dd34f2a64aa7b3edbf88bad4348e01291
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "85356202"
---
# <a name="troubleshoot-object-synchronization-with-azure-ad-connect-sync"></a>Objektumszinkronizálás hibaelhárítása Azure AD Connect-szinkronizálással
Ez a cikk az objektumok szinkronizálásával kapcsolatos hibák elhárításához nyújt lépéseket a hibaelhárítási feladat használatával. Ha szeretné megtudni, hogyan működik a hibaelhárítás Azure Active Directory (Azure AD) kapcsolódásban, tekintse meg [ezt a rövid videót](https://aka.ms/AADCTSVideo).

## <a name="troubleshooting-task"></a>Hibaelhárítási feladat
Az 1.1.749.0-s vagy újabb verziójú Azure AD Connect üzemelő példánya esetében használja a varázsló hibaelhárítási feladatát az objektumszinkronizálási problémák elhárításához. A korábbi verziók esetében az [itt](tshoot-connect-object-not-syncing.md)leírtak szerint manuálisan végezze el a hibakeresést.

### <a name="run-the-troubleshooting-task-in-the-wizard"></a>A hibaelhárítási feladat futtatása a varázslóban
A hibaelhárítási feladat a varázslóban való futtatásához hajtsa végre a következő lépéseket:

1.  Nyisson meg egy új Windows PowerShell-munkamenetet a Azure AD Connect-kiszolgálón a Futtatás rendszergazdaként beállítással.
2.  Futtassa a vagy a parancsot `Set-ExecutionPolicy RemoteSigned` `Set-ExecutionPolicy Unrestricted` .
3.  Indítsa el a Azure AD Connect varázslót.
4.  Navigáljon a további feladatok lapra, válassza a hibakeresés lehetőséget, majd kattintson a Tovább gombra.
5.  A hibaelhárítás lapon kattintson a Launch (indítás) elemre a PowerShell hibaelhárítási menüjének elindításához.
6.  A főmenüben válassza az objektumok szinkronizálásának hibakeresése elemet.
![Objektum-szinkronizálás hibáinak megoldása](media/tshoot-connect-objectsync/objsynch11.png)

### <a name="troubleshooting-input-parameters"></a>Bemeneti paraméterek hibaelhárítása
A hibaelhárítási feladat a következő bemeneti paramétereket igényli:
1.  **Objektum megkülönböztető neve** – ez a hibaelhárítást igénylő objektum megkülönböztető neve
2.  **Ad-összekötő neve** – ez annak az ad-erdőnek a neve, ahol a fenti objektum található.
3.  Azure AD-bérlő globális rendszergazdai hitelesítő adatainak ![ globális rendszergazdai hitelesítő adatai](media/tshoot-connect-objectsync/objsynch1.png)

### <a name="understand-the-results-of-the-troubleshooting-task"></a>A hibaelhárítási feladat eredményeinek megismerése
A hibaelhárítási feladat a következő ellenőrzéseket hajtja végre:

1.  UPN-eltérés észlelése, ha az objektum szinkronizálva van Azure Active Directory
2.  Ellenőrizze, hogy az objektum szűrve van-e a tartomány szűrése miatt
3.  Ellenőrizze, hogy a rendszer szűri-e az objektumot a szervezeti egység szűrése miatt
4.  Ellenőrizze, hogy a csatolt postaláda miatt le van-e tiltva az objektumok szinkronizálása
5. Ellenőrizze, hogy az objektum olyan dinamikus terjesztési csoport-e, amely nem szinkronizálható

A szakasz további része a feladat által visszaadott eredményeket ismerteti. Az egyes esetekben a feladat a probléma megoldásához javasolt műveletek alapján elemzést nyújt.

## <a name="detect-upn-mismatch-if-object-is-synced-to-azure-active-directory"></a>UPN-eltérés észlelése, ha az objektum szinkronizálva van Azure Active Directory
### <a name="upn-suffix-is-not-verified-with-azure-ad-tenant"></a>Az UPN-utótagot nem ellenőrzi az Azure AD-bérlő
Ha a UserPrincipalName (UPN)/Alternate a bejelentkezési azonosító utótagja nem lett ellenőrizve az Azure AD-Bérlővel, akkor a Azure Active Directory az UPN-utótagot az alapértelmezett "onmicrosoft.com" tartománynévvel helyettesíti.

![Az Azure AD lecseréli az egyszerű felhasználónevet](media/tshoot-connect-objectsync/objsynch2.png)

### <a name="azure-ad-tenant-dirsync-feature-synchronizeupnformanagedusers-is-disabled"></a>Az Azure AD-bérlő "SynchronizeUpnForManagedUsers" funkciója le van tiltva
Ha az Azure AD-bérlő "SynchronizeUpnForManagedUsers" funkciója le van tiltva, a Azure Active Directory nem engedélyezi a szinkronizációs frissítések számára a felügyelt hitelesítéssel rendelkező licencelt felhasználói fiókok UserPrincipalName/alternatív bejelentkezési AZONOSÍTÓját.

![SynchronizeUpnForManagedUsers](media/tshoot-connect-objectsync/objsynch4.png)

## <a name="object-is-filtered-due-to-domain-filtering"></a>Az objektumot a rendszer a tartomány szűrése miatt szűri
### <a name="domain-is-not-configured-to-sync"></a>A tartomány nincs szinkronra konfigurálva
Az objektum kívül esik a hatókörön, mert a tartomány nincs konfigurálva. Az alábbi példában az objektum nem szinkronizált hatókörű, mert a tartomány, amelyhez tartozik, a rendszer szűri a szinkronizálást.

![A tartomány nincs szinkronra konfigurálva](media/tshoot-connect-objectsync/objsynch5.png)

### <a name="domain-is-configured-to-sync-but-is-missing-run-profilesrun-steps"></a>A tartomány szinkronizálásra van konfigurálva, de hiányzik a futtatási profilok/futtatási lépések
Az objektum hatókörön kívül van, mivel a tartományból hiányzik a futtatási profilok/futtatási lépések. Az alábbi példában az objektum kívül esik a szinkronizálás hatókörén, mert a tartomány, amelyhez tartozik, a teljes importálási profil futtatási lépései hiányoznak.
![hiányzó futtatási profilok](media/tshoot-connect-objectsync/objsynch6.png)

## <a name="object-is-filtered-due-to-ou-filtering"></a>Az objektum a szervezeti egység szűrése miatt szűrve
Az objektum a szervezeti egység szűrési konfigurációja miatt kívül esik a szinkronizálás hatókörén. Az alábbi példában az objektum a OU = desync, DC = bvtadwbackdc, DC = com elemhez tartozik.  Ez a szervezeti egység nem szerepel a szinkronizálási hatókörben.</br>

![OU](./media/tshoot-connect-objectsync/objsynch7.png)

## <a name="linked-mailbox-issue"></a>Csatolt postaláda-probléma
Egy csatolt postaládát egy másik megbízható fiók erdőben található külső fő fiókhoz kell társítani. Ha nincs ilyen külső főfiók, akkor a Azure AD Connect nem fogja szinkronizálni a felhasználói fiókot az Exchange-erdőben lévő csatolt postaládának az Azure AD-Bérlővel.</br>
![Csatolt postaláda](./media/tshoot-connect-objectsync/objsynch12.png)

## <a name="dynamic-distribution-group-issue"></a>Dinamikus terjesztési csoport hibája
A helyszíni Active Directory és Azure Active Directory közötti különböző különbségek miatt a Azure AD Connect nem szinkronizálja a dinamikus terjesztési csoportokat az Azure AD-Bérlővel.

![Dinamikus terjesztési csoport](./media/tshoot-connect-objectsync/objsynch13.png)

## <a name="html-report"></a>HTML-jelentés
Az objektum elemzése mellett a hibaelhárítási feladat egy olyan HTML-jelentést is létrehoz, amely az objektummal kapcsolatos összes ismerettel rendelkezik. A HTML-jelentés a támogatási csapattal is megosztható további hibaelhárítási műveletek elvégzéséhez, ha szükséges.

![HTML-jelentés](media/tshoot-connect-objectsync/objsynch8.png)

## <a name="next-steps"></a>Következő lépések
További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).
