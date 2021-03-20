---
title: Az ESR-Azure Active Directory Csoportházirend és MDM beállításai
description: Enterprise State Roaming felügyeleti beállításai
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: reference
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: ab24b3113f9dc69b8f3907037e228ba212a03106
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "85252933"
---
# <a name="group-policy-and-mdm-settings"></a>Csoportházirend és MDM beállítások

Ezeket a csoportházirend-és mobileszköz-kezelési (MDM) beállításokat csak a vállalati tulajdonú eszközökön használja, mivel ezeket a házirendeket a felhasználó teljes eszközére alkalmazza a rendszer. Ha egy MDM házirendet alkalmaz a beállítások szinkronizálásának letiltására egy személyes, a felhasználó tulajdonában lévő eszköz számára, negatív hatással lesz az adott eszköz használatára. Az eszközön az egyéb felhasználói fiókokat is érinteni fogja a szabályzat.

Azok a vállalatok, amelyek személyes (nem felügyelt) eszközök barangolását szeretnék kezelni, a Azure Portal használatával engedélyezhetik vagy letilthatják a barangolást Csoportházirend vagy MDM helyett.
A következő táblázatok ismertetik az elérhető házirend-beállításokat.

> [!NOTE]
> Ez a cikk a Microsoft Edge korábbi HTML-alapú böngészőre vonatkozik, amely a Windows 10 2015-es verziójában indult el. A cikk nem vonatkozik az új, 2020. január 15-én kiadott Microsoft Edge Chromium-alapú böngészőre. Az új Microsoft Edge szinkronizálási működésével kapcsolatos további információkért tekintse meg a [Microsoft Edge Sync](/deployedge/microsoft-edge-enterprise-sync)című cikket.

## <a name="mdm-settings"></a>MDM-beállítások

A MDM házirend-beállítások a Windows 10 és a Windows 10 Mobile rendszerre egyaránt érvényesek.  A Windows 10 Mobile-támogatás csak a felhasználó OneDrive-fiókján keresztül Microsoft-fiók-alapú barangoláshoz van. Az Azure AD-alapú szinkronizálás által támogatott eszközökről az eszközök [és végpontok](enterprise-state-roaming-windows-settings-reference.md) című témakörben talál további információt.

| Név | Leírás |
| --- | --- |
| Microsoft-fiókok közötti kapcsolatok engedélyezése |Lehetővé teszi a felhasználók számára, hogy egy Microsoft-fiók használatával hitelesítsék az eszközt |
| Beállítások szinkronizálásának engedélyezése |Lehetővé teszi a felhasználóknak a Windows-beállítások és az alkalmazásadatok barangolását; A szabályzat letiltásával letilthatja a szinkronizálást és a mobileszközök biztonsági mentését |

## <a name="group-policy-settings"></a>Csoportszabályzat-beállítások

A csoportházirend-beállítások olyan Windows 10-es eszközökre vonatkoznak, amelyek Active Directory tartományhoz csatlakoznak. A táblázat olyan örökölt beállításokat is tartalmaz, amelyek úgy tűnik, hogy a szinkronizálási beállításokat kezelik, de nem működnek Enterprise State Roaming for Windows 10 esetében, amelyeket a leírásban a "ne használja" kifejezéssel jegyeztek fel.

Ezek a beállítások a következő helyeken találhatók: `Computer Configuration > Administrative Templates > Windows Components > Sync your settings` 

| Név | Leírás |
| --- | --- |
| Fiókok: Microsoft-fiókok blokkolása |Ezzel a házirend-beállítással megakadályozható, hogy a felhasználók új Microsoft-fiókokat adjanak hozzá a számítógépen |
| Ne szinkronizáljon |Megakadályozza, hogy a felhasználók barangolják a Windows beállításait és az alkalmazásadatok |
| Ne szinkronizáljon személyre |Letiltja a témák csoport szinkronizálását |
| Ne szinkronizálja a böngésző beállításait |Letiltja az Internet Explorer csoport szinkronizálását |
| Ne szinkronizáljon jelszavakat |Letiltja a jelszavak csoportjának szinkronizálását |
| Ne szinkronizáljon más Windows-beállításokat |Más Windows-beállítások csoport szinkronizálásának letiltása |
| Ne szinkronizálja az asztal személyre szabását |Ne használja; nincs hatása |
| Ne szinkronizáljon mért kapcsolatokon |Letiltja a barangolást mért kapcsolatokon, például a mobil 3G-ben |
| Alkalmazások szinkronizálásának mellőzése |Ne használja; nincs hatása |
| Alkalmazás-beállítások szinkronizálásának mellőzése |Az alkalmazásadatok barangolásának letiltása |
| Ne szinkronizáljon indítási beállításokat |Ne használja; nincs hatása |

## <a name="next-steps"></a>Következő lépések

Az áttekintést lásd: a [nagyvállalati állapot barangolása – áttekintés](enterprise-state-roaming-overview.md).
