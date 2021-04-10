---
title: Felügyeleti egységek – hibaelhárítás és gyakori kérdések – Azure Active Directory | Microsoft Docs
description: Vizsgálja meg a felügyeleti egységeket a korlátozott hatókörű engedélyek megadásához Azure Active Directoryban.
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: roles
ms.workload: identity
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: c1f41dca3b52ce75ba2342506f621cca0618a3bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102565886"
---
# <a name="azure-ad-administrative-units-troubleshooting-and-faq"></a>Azure AD felügyeleti egységek: Hibaelhárítás és gyakori kérdések

A Azure Active Directory (Azure AD) részletesebb felügyeleti felügyeletéhez a felhasználókat hozzárendelheti egy Azure AD-szerepkörhöz olyan hatókörrel, amely egy vagy több felügyeleti egységre van korlátozva. A gyakori feladatokhoz használható PowerShell-parancsfájlokat lásd: [a felügyeleti egységek használata](/powershell/azure/active-directory/working-with-administrative-units).

## <a name="frequently-asked-questions"></a>Gyakori kérdések

**K: Miért nem tudok felügyeleti egységet létrehozni?**

**A:** Csak a *globális rendszergazda* vagy a *Kiemelt szerepkörű rendszergazda* hozhat létre felügyeleti egységet az Azure ad-ben. Győződjön meg arról, hogy a felügyeleti egységet létrehozó felhasználó hozzá van rendelve a *globális rendszergazda* vagy a *Kiemelt szerepkörű rendszergazda* szerepkörhöz.

**K: hozzáadtam egy csoportot egy felügyeleti egységhez. Miért még nem jelennek meg a csoporttagok?**

**A:** Amikor csoportot ad hozzá egy felügyeleti egységhez, az nem eredményezi a csoport összes tagjának hozzáadását. A felhasználókat közvetlenül a felügyeleti egységhez kell rendelni.

**K: most Hozzáadtam (vagy eltávolítottam) a felügyeleti egység egyik tagját. Miért nem jelenik meg a tag (vagy még nem jelenik meg) a felhasználói felületen?**

**A:** Előfordulhat, hogy egy felügyeleti egység egy vagy több tagjának hozzáadása vagy eltávolítása eltarthat néhány percig a **felügyeleti egységek** ablaktáblán. Azt is megteheti, hogy közvetlenül a kapcsolódó erőforrás tulajdonságait látja el, és megtekinti, hogy a művelet befejeződött-e. A felügyeleti egységekben lévő felhasználókról és csoportokról további információt a [felügyeleti egységek listájának megtekintése a felhasználók](admin-units-add-manage-users.md) számára című témakörben talál, és [megtekintheti egy csoport felügyeleti egységeinek listáját](admin-units-add-manage-groups.md).

**K: delegált jelszavas rendszergazda vagyok egy felügyeleti egységen. Miért nem lehet alaphelyzetbe állítani egy adott felhasználó jelszavát?**

**A:** Rendszergazdai egység rendszergazdájaként csak a felügyeleti egységhez rendelt felhasználók jelszavait állíthatja alaphelyzetbe. Győződjön meg arról, hogy a jelszó-visszaállítást elmulasztó felhasználó tartozik ahhoz a felügyeleti egységhez, amelyhez hozzá van rendelve. Ha a felhasználó ugyanahhoz a felügyeleti egységhez tartozik, de továbbra sem tudja alaphelyzetbe állítani a felhasználó jelszavát, ellenőrizze a felhasználóhoz rendelt szerepköröket. 

A Jogosultságszint-emelés megszerzésének megakadályozása érdekében a felügyeleti egység hatókörű rendszergazdája nem állíthatja alaphelyzetbe egy olyan felhasználó jelszavát, aki egy, a szervezetre kiterjedő hatókörrel rendelkező szerepkörhöz van rendelve.

**K: Miért szükségesek a felügyeleti egységek? Nem sikerült használni a biztonsági csoportokat a hatókör definiálásának módjaként?**

**A:** A biztonsági csoportok meglévő célra és engedélyezési modellel rendelkeznek. A *felhasználói rendszergazdák* például kezelhetik az Azure ad-szervezet összes biztonsági csoportjának tagságát. A szerepkör csoportok használatával kezelheti az olyan alkalmazásokhoz való hozzáférést, mint a Salesforce. A *felhasználói rendszergazda* nem tudja kezelni magát a delegálási modellt, ami azt eredményezheti, hogy a biztonsági csoportok kiterjeszthetők az "erőforrás-csoportosítás" forgatókönyvek támogatására. 

A felügyeleti egységek (például a Windows Server Active Directory szervezeti egységei) célja, hogy lehetővé teszik a címtár-objektumok széles körének felügyeletét. Maguk a biztonsági csoportok az erőforrás-hatókörök tagjai lehetnek. Biztonsági csoportok használata a rendszergazda által kezelhető biztonsági csoportok meghatározásához zavaró lehet.

**K: mit jelent a csoport hozzáadása egy felügyeleti egységhez?**

**A:** Ha hozzáad egy csoportot egy felügyeleti egységhez, a csoport maga az adott felügyeleti egységre is kiterjedő *felhasználói rendszergazda* felügyeleti hatókörbe kerül. A felügyeleti egység felhasználói rendszergazdái a csoport nevét és tagságát is kezelhetik. Nem biztosítja a *felhasználónak* a csoport felhasználóinak felügyeletére vonatkozó engedélyeit (például a jelszavuk alaphelyzetbe állításához). Ahhoz, hogy *a felhasználók felügyelhetik a felhasználókat* , a felhasználóknak közvetlenül a felügyeleti egység tagjainak kell lenniük.

**K: lehet, hogy egy erőforrás (felhasználó vagy csoport) egynél több felügyeleti egység tagja?**

**A:** Igen, egy erőforrás több felügyeleti egység tagja is lehet. Az erőforrást az összes szervezetre kiterjedő és felügyeleti egység – hatókörű rendszergazda felügyelheti, akik rendelkeznek engedéllyel az erőforráson.

**K: vannak elérhető felügyeleti egységek a B2C-szervezeteknél?**

**A:** Nem, a B2C-szervezeteknek nem állnak rendelkezésre felügyeleti egységek.

**K: támogatottak a beágyazott felügyeleti egységek?**

**A:** Nem, a beágyazott felügyeleti egységek nem támogatottak.

**K: a PowerShell és a Graph API támogatja a felügyeleti egységeket?**

**V:** Igen. A felügyeleti egységek támogatását a PowerShell- [parancsmag dokumentációjában](/powershell/module/Azuread/) és a [minta parancsfájlokban](/powershell/azure/active-directory/working-with-administrative-units)találja.

A [administrativeUnit erőforrástípus](/graph/api/resources/administrativeunit) támogatásának keresése a Microsoft Graphban.

## <a name="next-steps"></a>Következő lépések

- [Szerepkörök hatókörének korlátozása felügyeleti egységek használatával](administrative-units.md)
- [Felügyeleti egységek kezelése](admin-units-manage.md)
