---
title: E-mail-értesítések a Azure AD Domain Serviceshoz | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhatja az e-mail-értesítéseket egy Azure Active Directory Domain Services felügyelt tartomány problémáinak riasztására
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: b9af1792-0b7f-4f3e-827a-9426cdb33ba6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 4242092af86f8bc06ef9c9eba068ce07725de01f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96619164"
---
# <a name="configure-email-notifications-for-issues-in-azure-active-directory-domain-services"></a>E-mail-értesítések konfigurálása a Azure Active Directory Domain Services kapcsolatos problémákhoz

Egy Azure Active Directory Domain Services (Azure AD DS) felügyelt tartomány állapotát az Azure platform figyeli. A Azure Portal állapot lapja a felügyelt tartományra vonatkozó riasztásokat jeleníti meg. Annak biztosítása érdekében, hogy a problémák időben megválaszolva legyenek, az e-mail-értesítések beállíthatók úgy, hogy az Azure AD DS felügyelt tartományban észlelt állapotú riasztásokat azonnal jelentsenek.

Ez a cikk bemutatja, hogyan konfigurálhatja a felügyelt tartományhoz tartozó e-mailes értesítés címzettjeit.

## <a name="email-notification-overview"></a>Értesítő e-mail – áttekintés

A felügyelt tartományokkal kapcsolatos problémák elhárításához beállíthatja az e-mailes értesítéseket. Ezek az e-mail-értesítések határozzák meg azt a felügyelt tartományt, amelyen a riasztás megtalálható, valamint az észlelés időpontját és a Azure Portal állapot lapjára mutató hivatkozást. A problémák elhárításához kövesse a megadott hibaelhárítási tanácsokat.

A következő példa az e-mailes értesítést jelzi, hogy a felügyelt tartományon kritikus figyelmeztetés vagy riasztás jött létre:

![Példa e-mail-értesítésre](./media/active-directory-domain-services-alerts/email-alert.png)

> [!WARNING]
> Mindig győződjön meg arról, hogy az e-mailben egy ellenőrzött Microsoft-feladó származik, mielőtt rákattint az üzenetben található hivatkozásokra. Az e-mail-értesítések mindig a címről származnak `azure-noreply@microsoft.com` .

### <a name="why-would-i-receive-email-notifications"></a>Miért kapok e-mailes értesítéseket?

Az Azure AD DS e-mail-értesítéseket küld a felügyelt tartományra vonatkozó fontos frissítésekről. Ezek az értesítések csak olyan sürgős problémákra vonatkoznak, amelyek hatással vannak a szolgáltatásra, és azonnal foglalkozni kell. Minden e-mail-értesítést a felügyelt tartomány riasztása aktivál. A riasztások a Azure Portal is megjelennek, és az [Azure AD DS Health (állapot) lapon][check-health]tekinthetők meg.

Az Azure AD DS nem küld e-maileket a hirdetményekhez, frissítésekhez vagy értékesítési célokra.

### <a name="when-will-i-receive-email-notifications"></a>Mikor kapom meg az e-mailes értesítéseket?

A rendszer azonnal elküld egy értesítést, amikor [új riasztás][troubleshoot-alerts] található egy felügyelt tartományon. Ha a riasztás nincs feloldva, a rendszer négy nap múlva küldi el a további e-mail-értesítéseket.

### <a name="who-should-receive-the-email-notifications"></a>Kik kapják meg az e-mailes értesítéseket?

Az Azure AD DS e-mail-címzettjeinek listáját olyan személyeknek kell tartalmaznia, akik felügyelhetik és módosíthatják a felügyelt tartományt. Az e-mail-lista a riasztások és a problémák első válaszadójának tekintendő.

Az e-mailes értesítésekhez legfeljebb öt további e-mail-címzettet adhat hozzá. Ha ötnél több címzettet szeretne e-mail-értesítések küldéséhez, hozzon létre egy terjesztési listát, és vegye fel az értesítési listára.

Azt is megteheti, hogy az Azure AD-címtár minden *globális rendszergazdája* és az *HRE DC-rendszergazdák* csoport minden tagja megkapja az e-mailes értesítéseket. Az Azure AD DS csak a 100-es e-mail-címekre küld értesítést, beleértve a globális rendszergazdák és a HRE tartományvezérlő-rendszergazdák listáját is.

## <a name="configure-email-notifications"></a>E-mail-értesítések konfigurálása

A meglévő e-mailes értesítés címzettjeinek áttekintéséhez vagy további címzettek hozzáadásához hajtsa végre a következő lépéseket:

1. A Azure Portal keresse meg és válassza a **Azure ad Domain Services** lehetőséget.
1. Válassza ki a felügyelt tartományt, például *aaddscontoso.com*.
1. Az Azure AD DS erőforrás ablak bal oldalán válassza az **értesítési beállítások** lehetőséget. Az e-mail értesítések meglévő címzettjei jelennek meg.
1. E-mail címzett hozzáadásához írja be az e-mail-címet a további címzettek táblába.
1. Ha elkészült, válassza a **Mentés** lehetőséget a felső navigációs sávon.

> [!WARNING]
> Az értesítési beállítások módosításakor a teljes felügyelt tartomány értesítési beállításai frissülnek, nem csupán saját kezűleg.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="i-received-an-email-notification-for-an-alert-but-when-i-logged-on-to-the-azure-portal-there-was-no-alert-what-happened"></a>Értesítést kaptam a riasztásról, de amikor bejelentkezett a Azure Portal nem volt riasztás. Mi történt?

Riasztás feloldásakor a rendszer törli a riasztást a Azure Portalról. A legvalószínűbb ok az, hogy valaki más, aki e-mail-értesítéseket kap, megoldotta a riasztást a felügyelt tartományon, vagy az Azure platformon keresztül lett feloldva.

### <a name="why-can-i-not-edit-the-notification-settings"></a>Miért nem lehet szerkeszteni az értesítési beállításokat?

Ha nem tud hozzáférni a Azure Portal értesítési beállítások lapjához, nem rendelkezik a felügyelt tartomány szerkesztéséhez szükséges engedélyekkel. Kérje meg a globális rendszergazdát, hogy kérjen engedélyt az Azure AD DS erőforrásának szerkesztésére, vagy távolítsa el a címzettek listájáról.

### <a name="i-dont-seem-to-be-receiving-email-notifications-even-though-i-provided-my-email-address-why"></a>Úgy tűnik, hogy nem Fogadok e-mailes értesítéseket, bár az e-mail címem is megérkezett. Miért?

Ellenőrizze az e-mailben küldött levélszemét-vagy Levélszemét-mappát az értesítéshez, és győződjön meg arról, hogy a küldője engedélyezte a következőt: `azure-noreply@microsoft.com` .

## <a name="next-steps"></a>Következő lépések

A jelentett problémák némelyikével kapcsolatos további információkért lásd: [riasztások feloldása felügyelt tartományon][troubleshoot-alerts].

<!-- INTERNAL LINKS -->
[check-health]: check-health.md
[troubleshoot-alerts]: troubleshoot-alerts.md
