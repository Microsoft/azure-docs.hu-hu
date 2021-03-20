---
title: 'Azure AD Connect Sync: az AD Lomtár engedélyezése | Microsoft Docs'
description: Ez a témakör az Active Directory Lomtár funkcióinak használatát javasolja Azure AD Connect.
services: active-directory
keywords: AD Lomtár, véletlen törlés, forrás-horgony
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: afec4207-74f7-4cdd-b13a-574af5223a90
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 12/17/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 12073a75cd248c9226c7ce5ecc21b64617823b32
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89279635"
---
# <a name="azure-ad-connect-sync-enable-ad-recycle-bin"></a>Azure AD Connect Sync: az AD Lomtár engedélyezése
Javasoljuk, hogy engedélyezze az AD Lomtár szolgáltatást a helyszíni Active Directory-címtárban, amelyek szinkronizálva vannak az Azure AD-vel. 

Ha véletlenül törölt egy helyszíni AD felhasználói objektumot, és a szolgáltatással visszaállítja azt, az Azure AD visszaállítja a megfelelő Azure AD felhasználói objektumot.  Az AD Lomtár szolgáltatással kapcsolatos további információkért tekintse meg a [törölt Active Directory objektumok visszaállítására vonatkozó forgatókönyv áttekintése](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd379542(v=ws.10))című cikket.

## <a name="benefits-of-enabling-the-ad-recycle-bin"></a>Az AD Lomtár engedélyezésének előnyei
Ez a funkció segítséget nyújt az Azure AD felhasználói objektumok visszaállításához a következő módon:

* Ha véletlenül törölt egy helyszíni AD felhasználói objektumot, a rendszer törli a megfelelő Azure AD felhasználói objektumot a következő szinkronizálási ciklusban. Az Azure AD alapértelmezés szerint 30 napig tárolja a törölt Azure AD felhasználói objektumot.

* Ha engedélyezve van a helyszíni Active Directory Lomtár funkciója, visszaállíthatja a törölt helyszíni AD felhasználói objektumot a forrás-szerkesztőpont értékének módosítása nélkül. Ha a helyreállított helyszíni AD felhasználói objektum szinkronizálva van az Azure AD-val, az Azure AD visszaállítja a megfelelő, helyreállított Azure AD felhasználói objektumot. A forrás-Anchor attribútummal kapcsolatos információkért tekintse meg a [Azure ad Connect: tervezési fogalmak](./plan-connect-design-concepts.md#sourceanchor)című cikket.

* Ha nincs engedélyezve a helyszíni AD Lomtár szolgáltatás, előfordulhat, hogy létre kell hoznia egy AD-felhasználói objektumot a törölt objektum cseréjéhez. Ha Azure AD Connect szinkronizációs szolgáltatás úgy van konfigurálva, hogy a forrás-rögzítési attribútumhoz a rendszer által generált AD-attribútumot (például ObjectGuid) használja, az újonnan létrehozott AD felhasználói objektum nem lesz ugyanazzal a forrás-szerkesztőpont értékkel, mint a törölt AD felhasználói objektum. Ha az újonnan létrehozott AD felhasználói objektum szinkronizálva van az Azure AD-vel, az Azure AD egy új Azure AD felhasználói objektumot hoz létre, és nem állítja vissza a Soft Deleted Azure AD felhasználói objektumot.

> [!NOTE]
> Alapértelmezés szerint az Azure AD az Azure AD felhasználói objektumainak a véglegesen törölt állapotba való törlését 30 napig törli. A rendszergazdák azonban fel tudják gyorsítani az ilyen objektumok törlését. Az objektumok végleges törlése után már nem állíthatók helyre, még akkor is, ha a helyszíni AD Lomtár szolgáltatás engedélyezve van.

## <a name="next-steps"></a>Következő lépések
**Áttekintő témakörök**

* [Azure AD Connect szinkronizálás: a szinkronizálás megismerése és testreszabása](how-to-connect-sync-whatis.md)

* [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)