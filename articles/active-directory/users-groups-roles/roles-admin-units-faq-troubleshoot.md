---
title: Felügyeleti egységek – hibaelhárítás és gyakori kérdések – Azure Active Directory | Microsoft Docs
description: Vizsgálja meg a felügyeleti egységeket a korlátozott hatókörű engedélyek megadásához Azure Active Directoryban.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 09/22/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7f9af8ee3bf87cfd5d2e74adfce618c7cf8c7e63
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91450333"
---
# <a name="azure-ad-administrative-units-troubleshooting-and-faq"></a>Azure AD felügyeleti egységek: Hibaelhárítás és gyakori kérdések

A Azure Active Directory (Azure AD) részletesebb felügyeleti felügyeletéhez a felhasználókat hozzárendelheti egy Azure AD-szerepkörhöz olyan hatókörrel, amely egy vagy több felügyeleti egységre (AUs) korlátozódik. A gyakori feladatokhoz használható PowerShell-parancsfájlokat lásd: [a felügyeleti egységek használata](/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0&preserve-view=true).

## <a name="frequently-asked-questions"></a>Gyakori kérdések

**K: Miért nem tudok felügyeleti egységet létrehozni?**

**A:** Csak a *globális rendszergazda* vagy a *Kiemelt szerepkörű rendszergazda* hozhat létre felügyeleti egységet az Azure ad-ben. Győződjön meg arról, hogy a felügyeleti egységet létrehozó felhasználó hozzá van rendelve a *globális rendszergazda* vagy a *Kiemelt szerepkörű rendszergazda* szerepkörhöz.

**K: hozzáadtam egy csoportot a felügyeleti egységhez. Miért még nem jelennek meg a csoporttagok?**

**A:** Ha hozzáad egy csoportot a felügyeleti egységhez, az nem eredményezi a csoport összes tagjának hozzáadását. A felhasználókat közvetlenül a felügyeleti egységhez kell rendelni.

**K: most Hozzáadtam (vagy eltávolítottam) a felügyeleti egység egyik tagját. Miért nem jelenik meg a tag (vagy még nem jelenik meg) a felhasználói felületen?**

**A:** Előfordulhat, hogy a felügyeleti egység egy vagy több tagja hozzáadásának vagy eltávolításának feldolgozása néhány percet igénybe vehet a **felügyeleti egységek** lapon. Azt is megteheti, hogy közvetlenül a kapcsolódó erőforrás tulajdonságait látja el, és megtekinti, hogy a művelet befejeződött-e. További információ a felhasználókról és csoportokról az AUs-ban: a [felhasználók felügyeleti egységeinek listázása](roles-admin-units-add-manage-users.md) és a [csoportok felügyeleti egységeinek listázása](roles-admin-units-add-manage-groups.md).

**K: delegált jelszavas rendszergazda vagyok egy felügyeleti egységen. Miért nem lehet alaphelyzetbe állítani egy adott felhasználó jelszavát?**

**A:** Rendszergazdai egység rendszergazdájaként csak a felügyeleti egységhez rendelt felhasználók jelszavait állíthatja alaphelyzetbe. Győződjön meg arról, hogy a jelszó-visszaállítást elmulasztó felhasználó tartozik ahhoz a felügyeleti egységhez, amelyhez hozzá van rendelve. Ha a felhasználó ugyanahhoz a felügyeleti egységhez tartozik, de továbbra sem tudja visszaállítani a jelszavát, ellenőrizze a felhasználóhoz rendelt szerepköröket. 

A Jogosultságszint-emelés megszerzésének megakadályozása érdekében a felügyeleti egység hatókörű rendszergazdája nem állíthatja alaphelyzetbe egy olyan felhasználó jelszavát, aki egy, a szervezetre kiterjedő hatókörrel rendelkező szerepkörhöz van rendelve.

**K: Miért szükségesek a felügyeleti egységek? Nem sikerült használni a biztonsági csoportokat a hatókör definiálásának módjaként?**

**A:** A biztonsági csoportok meglévő célra és engedélyezési modellel rendelkeznek. A *felhasználói rendszergazdák*például kezelhetik az Azure ad-szervezet összes biztonsági csoportjának tagságát. A szerepkör csoportok használatával kezelheti az olyan alkalmazásokhoz való hozzáférést, mint a Salesforce. A *felhasználói rendszergazda* nem tudja kezelni magát a delegálási modellt, ami azt eredményezheti, hogy a biztonsági csoportok kiterjeszthetők az "erőforrás-csoportosítás" forgatókönyvek támogatására. A felügyeleti egységek (például a Windows Server Active Directory szervezeti egységei) célja, hogy lehetővé teszik a címtár-objektumok széles körének felügyeletét. Maguk a biztonsági csoportok az erőforrás-hatókörök tagjai lehetnek. Biztonsági csoportok használata a rendszergazda által kezelhető biztonsági csoportok meghatározásához zavaró lehet.

**K: mit jelent a csoport hozzáadása egy felügyeleti egységhez?**

**A:** Ha hozzáad egy csoportot egy felügyeleti egységhez, a csoport maga az adott felügyeleti egységre is kiterjedő *felhasználói rendszergazda* felügyeleti hatókörbe kerül. A felügyeleti egység felhasználói rendszergazdái a csoport nevét és tagságát is kezelhetik. Nem biztosítja a *rendszergazda* számára a felügyeleti egység engedélyeit a csoport felhasználóinak kezeléséhez (például a jelszavuk alaphelyzetbe állításához). Ahhoz, hogy *a felhasználók felügyelhetik a felhasználókat* , a felhasználóknak közvetlenül a felügyeleti egység tagjainak kell lenniük.

**K: lehet, hogy egy erőforrás (felhasználó vagy csoport) egynél több felügyeleti egység tagja?**

**A:** Igen, egy erőforrás több felügyeleti egység tagja is lehet. Az erőforrást az összes szervezetre kiterjedő és felügyeleti egység – hatókörű rendszergazda felügyelheti, akik rendelkeznek engedéllyel az erőforráson.

**K: vannak elérhető felügyeleti egységek a B2C-szervezeteknél?**

**A:** Nem, a B2C-szervezeteknek nem állnak rendelkezésre felügyeleti egységek.

**K: támogatottak a beágyazott felügyeleti egységek?**

**A:** Nem, a beágyazott felügyeleti egységek nem támogatottak.

**K: a PowerShell és a Graph API támogatja a felügyeleti egységeket?**

**V:** Igen. A felügyeleti egységek támogatását a PowerShell- [parancsmag dokumentációjában](/powershell/module/Azuread/?view=azureadps-2.0&preserve-view=true) és a [minta parancsfájlokban](/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0&preserve-view=true)találja.

A [administrativeUnit erőforrástípus](/graph/api/resources/administrativeunit?view=graph-rest-1.0&preserve-view=true) támogatásának keresése a Microsoft Graphban.

## <a name="next-steps"></a>Következő lépések

- [Szerepkörök hatókörének korlátozása felügyeleti egységek használatával](directory-administrative-units.md)
- [Felügyeleti egységek kezelése](roles-admin-units-manage.md)