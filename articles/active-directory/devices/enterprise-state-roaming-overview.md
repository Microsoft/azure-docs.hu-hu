---
title: Mi a vállalati állapot barangolás Azure Active Directory?
description: Enterprise State Roaming a felhasználók számára egységes felhasználói élményt nyújt a Windows-eszközökön
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: overview
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: c22baf0a08718883f0c0c9844cc395f607b5b20d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "77194279"
---
# <a name="what-is-enterprise-state-roaming"></a>Mi az az Enterprise State Roaming?

A Windows 10 [Azure Active Directory (Azure ad)](../fundamentals/active-directory-whatis.md) felhasználóinak lehetősége van arra, hogy biztonságosan szinkronizálja felhasználói beállításait és az Alkalmazásbeállítások adatait a felhőbe. Enterprise State Roaming a felhasználók számára egységes felhasználói élményt biztosít a Windows-eszközökön, és csökkenti az új eszközök konfigurálásához szükséges időt. A Enterprise State Roaming a normál [fogyasztói beállítások szinkronizálásához](https://go.microsoft.com/fwlink/?linkid=2015135) hasonló módon működik, amely először a Windows 8 rendszerben jelent meg. Emellett Enterprise State Roaming ajánlatok:

* **A vállalati és a fogyasztói adatforgalom elkülönítése** – a szervezetek a saját adatvédelme alatt állnak, és a vállalati adatmennyiséget nem keverik felhasználói Felhőbeli fiókban vagy vállalati felhőalapú fiókban lévő fogyasztói adatként.
* **Fokozott biztonság** – az adatok automatikusan titkosítva maradnak, mielőtt elhagyják a felhasználó Windows 10-es eszközét az Azure Rights Management (Azure RMS) használatával, és az adatok titkosítva maradnak a felhőben. Minden tartalom titkosítva marad a felhőben, kivéve a névtereket, például a beállítások nevét és a Windows-alkalmazások nevét.  
* **Hatékonyabb felügyelet és monitorozás** – lehetővé teszi a szervezeten belüli beállítások szinkronizálását és az Azure ad-portál integrációját használó eszközök felügyeletét és láthatóságát. 

Enterprise State Roaming több Azure-régióban is elérhető. Az elérhető régiók frissített listáját az [Azure-szolgáltatások régiók](https://azure.microsoft.com/regions/#services) szerint lapján találja Azure Active Directory alatt.

| Cikk | Description |
| --- | --- |
| [A vállalati állapothordozás engedélyezése az Azure Active Directoryban](enterprise-state-roaming-enable.md) |Enterprise State Roaming bármely, prémium szintű Azure Active Directory (Azure AD-) előfizetéssel rendelkező szervezet számára elérhető. Az Azure AD-előfizetés beszerzésével kapcsolatos további információkért tekintse meg az [Azure ad termékét](https://azure.microsoft.com/services/active-directory) ismertető oldalt. |
| [Beállítások és adatroaming GYIK](enterprise-state-roaming-faqs.md) |Ez a cikk bizonyos kérdésekre ad választ, amelyekkel a rendszergazdák a beállítások és az alkalmazásadatok szinkronizálásával rendelkezhetnek. |
| [A beállítások szinkronizálásának csoportházirend-és MDM beállításai](enterprise-state-roaming-group-policy-settings.md) |A Windows 10 Csoportházirend és mobileszköz-kezelési (MDM) házirend-beállításokat biztosít a beállítások szinkronizálásának korlátozásához. |
| [Windows 10 roaming beállítások referenciája](enterprise-state-roaming-windows-settings-reference.md) |A Windows 10 rendszerben barangolni és/vagy biztonsági mentésre kerülő beállítások listája. |
| [Hibaelhárítás](enterprise-state-roaming-troubleshooting.md) |Ez a cikk néhány alapvető lépést ismertet a hibaelhárításhoz, és tartalmazza az ismert problémák listáját. |

## <a name="next-steps"></a>Következő lépések

További információ a vállalati állapot barangolásának engedélyezéséről: a [vállalati állapot barangolásának engedélyezése](enterprise-state-roaming-enable.md).
