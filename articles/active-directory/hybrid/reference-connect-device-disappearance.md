---
title: Az Azure AD Connect 1.4. xx. x és az eszközök megszűnésének ismertetése | Microsoft Docs
description: Ez a dokumentum egy olyan problémát ismertet, amely a Azure AD Connect 1.4. xx. x verziójával felmerül.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: bc159452c81a673ca4a7ed46aa7eff19fd9209eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "73176025"
---
# <a name="understanding-azure-ad-connect-14xxx-and-device-disappearance"></a>Az Azure AD Connect 1.4. xx. x és az eszközön való megjelenés ismertetése
A Azure AD Connect 1.4. xx. x verziójával néhány ügyfél láthatja, hogy a Windows-eszközeik némelyike vagy mindegyike eltűnik az Azure AD-ből. Ez nem okoz gondot, mivel az Azure AD nem használja fel az eszköz identitásait a feltételes hozzáférés engedélyezésekor. Ez a változás nem törli azokat a Windows-eszközöket, amelyek megfelelően regisztrálva lettek az Azure AD-ben hibrid Azure AD-csatlakozáshoz.

Ha úgy látja, hogy az Azure AD-ban az eszközök objektumainak törlése meghaladja az Exportálás törlési küszöbértékét, javasoljuk, hogy az ügyfél engedélyezze a törlést. [Útmutató: a törlési folyamat engedélyezése, ha túllépik a törlési küszöbértéket](how-to-connect-sync-feature-prevent-accidental-deletes.md)

## <a name="background"></a>Háttér
A hibrid Azure AD-hez regisztrált Windows-eszközök az Azure AD-ben, az eszköz objektumaiként jelennek meg. Ezeket az eszközöket feltételes hozzáféréshez használhatja. A Windows 10-es eszközök a felhőbe való szinkronizálása Azure AD Connect, a Down szintű Windows-eszközök közvetlen regisztrálása AD FS vagy zökkenőmentes egyszeri bejelentkezés használatával történik.

## <a name="windows-10-devices"></a>Windows 10-eszközök
Azure AD Connect a felhőbe csak a hibrid Azure AD JOIN által konfigurált, meghatározott userCertificate attribútum értékkel rendelkező Windows 10-es eszközöket kell szinkronizálni. Azure AD Connect korábbi verzióiban ez a követelmény nem lett szigorúan kikényszerítve, ami az Azure AD-ben szükségtelen eszköz-objektumokat eredményezett. Az Azure AD-beli eszközök mindig "függő" állapotban maradnak, mivel ezek az eszközök nem lettek regisztrálva az Azure AD-ben. 

A Azure AD Connect ezen verziója csak a hibrid Azure AD-hez csatlakoztatott Windows 10-es eszközök szinkronizálását hajtja végre. Az Azure AD JOIN-specifikus userCertificate nem rendelkező Windows 10-es eszközök el lesznek távolítva az Azure AD-ből.

## <a name="down-level-windows-devices"></a>Windows-eszközök Down-Level
Azure AD Connect soha ne legyenek szinkronizálva a [Windows rendszerű eszközök](../devices/hybrid-azuread-join-plan.md#windows-down-level-devices). Az Azure AD-ben korábban szinkronizált eszközök már nem megfelelően lesznek törölve az Azure AD-ből. Ha Azure AD Connect a [régebbi Windows-eszközök](../devices/hybrid-azuread-join-plan.md#windows-down-level-devices)törlését kísérli meg, akkor az eszköz nem a [Microsoft Workplace JOIN által a nem Windows 10 rendszerű számítógépek MSI-hez](https://www.microsoft.com/download/details.aspx?id=53554) készült, és a többi Azure ad-szolgáltatás nem tudja használni.

Előfordulhat, hogy néhány ügyfélnek újra kell látogatnia a [hibrid Azure Active Directory csatlakoztatásának megtervezése](../devices/hybrid-azuread-join-plan.md) a Windows-eszközök megfelelő regisztrálásához, és biztosítania kell, hogy az ilyen eszközök teljes mértékben részt vegyenek az eszköz alapú feltételes hozzáférésben. 

## <a name="how-can-i-verify-which-devices-are-deleted-with-this-update"></a>Hogyan tudom ellenőrizni, hogy mely eszközök törlődnek ezzel a frissítéssel?

A következő PowerShell-parancsfájl használatával ellenőrizheti, hogy mely eszközök törlődnek: https://gallery.technet.microsoft.com/scriptcenter/Export-Hybrid-Azure-AD-f8e51436

Ez a szkript jelentést készít a Active Directory számítógép-objektumokban tárolt tanúsítványokról, pontosabban a hibrid Azure AD JOIN szolgáltatás által kiállított tanúsítványokról.
Ellenőrzi az AD-ben található számítógép-objektum UserCertificate tulajdonságában lévő tanúsítványokat, és minden nem lejárt tanúsítvány esetében ellenőrzi, hogy a tanúsítvány ki lett-e adva a hibrid Azure AD JOIN szolgáltatáshoz (azaz a tulajdonos neve megegyezik a CN = {ObjectGUID} értékkel).
Mielőtt a Azure AD Connect az Azure AD-vel szinkronizálja azokat a számítógépeket, amelyek legalább egy érvényes tanúsítványt tartalmaznak, de a Azure AD Connect 1,4-es verziótól kezdődően a szinkronizációs motor képes azonosítani a hibrid Azure AD JOIN-tanúsítványokat, és "cloudfilter" a számítógép-objektumot az Azure AD-be való szinkronizálással, hacsak nincs érvényes hibrid Azure AD JOIN tanúsítvány.
Azok az Azure AD-eszközök, amelyek már szinkronizálva lettek az Active Directoryval, de nem rendelkeznek érvényes hibrid Azure AD JOIN tanúsítvánnyal, a Szinkronizáló motor törli a (CloudFiltered = TRUE) értéket.

## <a name="next-steps"></a>Következő lépések
- [Azure AD Connect korábbi verziók](reference-connect-version-history.md)
