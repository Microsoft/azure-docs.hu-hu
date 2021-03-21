---
title: 'Azure AD Connect: msExchUserHoldPolicies és cloudMsExchUserHoldPolicies | Microsoft Docs'
description: Ez a témakör a msExchUserHoldPolicies és a cloudMsExchUserHoldPolicies attribútumok attribútum-viselkedését ismerteti.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 09/15/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0fbda588d99de44c77118586519055a8fc474104
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96861765"
---
# <a name="azure-ad-connect---msexchuserholdpolicies-and-cloudmsexchuserholdpolicies"></a>Azure AD Connect – msExchUserHoldPolicies és cloudMsExchUserHoldPolicies
Az alábbi dokumentum ismerteti ezeket az attribútumokat, amelyeket az Exchange használ, valamint az alapértelmezett szinkronizálási szabályok szerkesztésének megfelelő módját.

## <a name="what-are-msexchuserholdpolicies-and-cloudmsexchuserholdpolicies"></a>Mi a msExchUserHoldPolicies és a cloudMsExchUserHoldPolicies?
Az Exchange Serverhez két típusú [tároló érhető el: a peres](/Exchange/policy-and-compliance/holds/holds) eljárás megtartása és a In-Place. Ha a peres eljárás engedélyezve van, az összes postaláda minden elemet megtart.  Egy In-Place tároló csak azokat az elemeket őrzi meg, amelyek megfelelnek a In-Place elektronikus iratkezelési eszközzel megadott keresési lekérdezés feltételeinek.

A MsExchUserHoldPolcies és a cloudMsExchUserHoldPolicies attribútumok lehetővé teszik a helyszíni AD és az Azure AD számára annak meghatározását, hogy mely felhasználók tartanak fenn, attól függően, hogy helyszíni Exchange-et vagy Exchange-et használnak-e.

## <a name="msexchuserholdpolicies-synchronization-flow"></a>msExchUserHoldPolicies szinkronizálási folyamata
Alapértelmezés szerint a MsExchUserHoldPolcies szinkronizálja Azure AD Connect közvetlenül a metaverse-ban található msExchUserHoldPolicies attribútumhoz, majd az Azure AD msExchUserHoldPolicies attribútumához.

Az alábbi táblázatok ismertetik a folyamatot:

Helyszíni Active Directorytól bejövő:

|Active Directory attribútum|Attribútum neve|Folyamat típusa|Metaverse-attribútum|Szinkronizálási szabály|
|-----|-----|-----|-----|-----|
|Helyszíni Active Directory|msExchUserHoldPolicies|Direct|msExchUserHoldPolicies|Az AD-User Exchange-ből|

Kimenő az Azure AD-be:

|Metaverse-attribútum|Attribútum neve|Folyamat típusa|Azure AD-attribútum|Szinkronizálási szabály|
|-----|-----|-----|-----|-----|
|Azure Active Directory|msExchUserHoldPolicies|Direct|msExchUserHoldPolicies|HRE – UserExchangeOnline|

## <a name="cloudmsexchuserholdpolicies-synchronization-flow"></a>cloudMsExchUserHoldPolicies szinkronizálási folyamata
Alapértelmezés szerint a rendszer a cloudMsExchUserHoldPolicies szinkronizálja Azure AD Connect közvetlenül a metaverse cloudMsExchUserHoldPolicies attribútumához. Ezt követően, ha a msExchUserHoldPolicies nem null értékű a metaverse-ben, az attribútum a Active Directory.

Az alábbi táblázatok ismertetik a folyamatot:

Bejövő Azure AD-ből:

|Active Directory attribútum|Attribútum neve|Folyamat típusa|Metaverse-attribútum|Szinkronizálási szabály|
|-----|-----|-----|-----|-----|
|Helyszíni Active Directory|cloudMsExchUserHoldPolicies|Direct|cloudMsExchUserHoldPolicies|HRE – felhasználói Exchange|

Kimenő – helyszíni Active Directory:

|Metaverse-attribútum|Attribútum neve|Folyamat típusa|Azure AD-attribútum|Szinkronizálási szabály|
|-----|-----|-----|-----|-----|
|Azure Active Directory|cloudMsExchUserHoldPolicies|IF (NEM NULL)|msExchUserHoldPolicies|Ki az AD – UserExchangeOnline|

## <a name="information-on-the-attribute-behavior"></a>Az attribútum viselkedésére vonatkozó információk
A msExchangeUserHoldPolicies egyetlen szolgáltatói attribútum.  Egyetlen Authority attribútum állítható be egy objektumra (ebben az esetben a felhasználói objektumra) a helyszíni címtárban vagy a Felhőbeli címtárban.  A felügyeleti szabályok kezdete azt diktálja, hogy ha az attribútum a helyszíni rendszerből van szinkronizálva, akkor az Azure AD nem fogja tudni frissíteni ezt az attribútumot.

Annak lehetővé tétele, hogy a felhasználók a felhőben lévő felhasználói objektumon állítsanak be tartási szabályzatot, a rendszer a cloudMSExchangeUserHoldPolicies attribútumot használja. Ezt az attribútumot használja a rendszer, mert az Azure AD nem tudja közvetlenül beállítani a msExchangeUserHoldPolicies a fent ismertetett szabályok alapján.  Ezt az attribútumot ezután a rendszer visszaszinkronizálja a helyszíni címtárral, ha a msExchangeUserHoldPolicies nem null értékű, és lecseréli a msExchangeUserHoldPolicies aktuális értékét.

Bizonyos körülmények között, például ha mindkettőt a helyszínen és az Azure-ban módosították egyszerre, akkor ez bizonyos problémákat okozhat.  

## <a name="next-steps"></a>Következő lépések
További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).
