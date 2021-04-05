---
title: A felhőalapú csoportokhoz hozzárendelt szerepkörök hibaelhárítása – Azure Active Directory | Microsoft Docs
description: Ismerkedjen meg néhány gyakori kérdéssel és hibaelhárítási tippekkel a szerepkörök csoporthoz való hozzárendeléséhez Azure Active Directoryban.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: article
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0ff906654dc91c7b1980292efd2a737503e684e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98742913"
---
# <a name="troubleshooting-roles-assigned-to-cloud-groups"></a>A felhőbeli csoportokhoz hozzárendelt szerepkörökkel kapcsolatos hibák elhárítása

Íme néhány gyakori kérdés és hibaelhárítási tipp a szerepkörök csoportokhoz való hozzárendeléséhez Azure Active Directory (Azure AD).

**K:** Rendszergazdák vagyok, de nem látom **, hogy az Azure ad-szerepkörök hozzárendelhetők a csoport** kapcsolóhoz.

**A:** Csak a Kiemelt szerepkörű rendszergazdák vagy a globális rendszergazdák hozhatnak létre olyan csoportot, amely jogosult szerepkör-hozzárendelésre. Ez a vezérlő csak a szerepkörökben szereplő felhasználók számára jelenik meg.

**K:** Ki módosíthatja az Azure AD-szerepkörökhöz rendelt csoportok tagságát?

**A:** Alapértelmezés szerint csak a Kiemelt szerepkörű rendszergazdák és a globális rendszergazdák kezelhetik a szerepkörhöz hozzárendelhető csoportok tagságát, de a szerepkörhöz hozzárendelhető csoportok felügyeletét a csoportok tulajdonosainak hozzáadásával is delegálhatja.

**K**: a cégem ügyfélszolgálati rendszergazdája vagyok, de nem tudom frissíteni egy olyan felhasználó jelszavát, aki egy címtár-olvasó. Miért történik ez?

**A**: Előfordulhat, hogy a felhasználó egy szerepkör-hozzárendelésre jogosult csoportból kapta meg a címtár-olvasót. A szerepkörhöz hozzárendelhető csoportok összes tagja és tulajdonosa védve van. A védett felhasználók hitelesítő adatainak alaphelyzetbe állításához csak a privilegizált hitelesítő rendszergazda vagy a globális rendszergazdai szerepkör felhasználói módosíthatnak.

**K:** Nem tudom frissíteni egy felhasználó jelszavát. Nincs hozzárendelve magasabb jogosultsági szintű szerepkör. Mi történik?

**A:** A felhasználó a szerepkörhöz hozzárendelhető csoport tulajdonosa lehet. A jogosultság megemelésének elkerülése érdekében a szerepkörhöz hozzárendelhető csoportok tulajdonosait védik. Ilyen eset lehet például, ha egy csoport Contoso_Security_Admins van hozzárendelve a biztonsági rendszergazdai szerepkörhöz, ahol Bob a csoport tulajdonosa, és Alice a szervezet jelszavas rendszergazdája. Ha ez a védelem nem volt jelen, Alice visszaállíthatja a Bob hitelesítő adatait, és átveszi a személyazonosságát. Ezt követően Alice felveheti magát vagy bárkit a csoportba Contoso_Security_Admins csoportba, hogy a szervezet biztonsági rendszergazdája legyen. Annak megállapításához, hogy a felhasználó tagja-e a csoportnak, kérje le az adott felhasználó tulajdonában lévő objektumok listáját, és ellenőrizze, hogy a csoportok isAssignableToRole értéke TRUE (igaz) értékre van-e állítva. Ha igen, akkor a felhasználó védelme megtörténik, és a viselkedés a tervezés szerint történik. Tekintse meg ezeket a dokumentációkat a tulajdonosi objektumok beszerzéséhez:

- [Get-AzureADUserOwnedObject](/powershell/module/azuread/get-azureaduserownedobject)  
- [OwnedObjects listázása](/graph/api/user-list-ownedobjects?tabs=http)

**K:** Létrehozhatok hozzáférési felülvizsgálatot az Azure AD-szerepkörökhöz hozzárendelhető csoportokban (pontosabban a isAssignableToRole tulajdonsággal rendelkező csoportok értéke true)?  

**A:** Igen, lehet. Ha a hozzáférési felülvizsgálat legújabb verziójával rendelkezik, a véleményezők alapértelmezés szerint a saját hozzáférésre lesznek irányítva, és csak a globális rendszergazdák hozhatnak létre hozzáférési felülvizsgálatokat a szerepkörhöz hozzárendelhető csoportok számára. Ha azonban a hozzáférési felülvizsgálat régebbi verziója van, akkor a felülvizsgálók alapértelmezés szerint a hozzáférési panelre lesznek irányítva, és a globális rendszergazdák és a felhasználói rendszergazda is létrehozhat hozzáférési felülvizsgálatokat a szerepkörhöz hozzárendelhető csoportok számára. Az új felhasználói élmény az összes ügyfelünk számára elérhető lesz, a 2020. július 28-án, de ha hamarabb szeretné frissíteni, kérjen egy kérést az [Azure ad hozzáférési felülvizsgálatok számára – frissített felülvizsgáló élmény a hozzáférési regisztrációban](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR5dv-S62099HtxdeKIcgO-NUOFJaRDFDWUpHRk8zQ1BWVU1MMTcyQ1FFUi4u).

**K:** Létrehozhatok olyan hozzáférési csomagot, amely az Azure AD-szerepkörökhöz hozzárendelhető csoportokat is létrehozhatja?

**A:** Igen, lehet. A globális rendszergazda és a felhasználó rendszergazdája jogosult a csoportok egy hozzáférési csomagban való üzembe helyezésére. Nincs változás a globális rendszergazda számára, de a felhasználói rendszergazdai szerepkörre vonatkozó engedélyek kis mértékben módosulnak. A szerepkörhöz hozzárendelhető csoportok hozzáférési csomagba való helyezéséhez felhasználói rendszergazdának kell lennie, és a szerepkörhöz hozzárendelhető csoport tulajdonosának is kell lennie. Az alábbi teljes táblázat azt mutatja be, hogy kik hozhatnak létre hozzáférési csomagot a vállalati licencek kezelése szolgáltatásban:

Azure AD-címtárbeli szerepkör | Jogosultsági felügyeleti szerepkör | Hozzáadhat biztonsági csoportot\* | Hozzáadhat Microsoft 365 csoportot\* | Hozzáadhat alkalmazást | Hozzáadhat SharePoint Online-webhelyet
----------------------- | --------------------------- | ----------------------- | ------------------------- | ----------- |  -----------------------------
Globális rendszergazda | n.a. | ✔️ | ✔️ | ✔️  | ✔️
Felhasználói rendszergazda  | n.a.  | ✔️  | ✔️  | ✔️
Intune-rendszergazda | Katalógus tulajdonosa | ✔️  | ✔️  | &nbsp;  | &nbsp;
Exchange-rendszergazda  | Katalógus tulajdonosa  | &nbsp; | ✔️  | &nbsp;  | &nbsp;
Teams szolgáltatás rendszergazdája | Katalógus tulajdonosa  | &nbsp; | ✔️  | &nbsp;  | &nbsp;
SharePoint-rendszergazda | Katalógus tulajdonosa | &nbsp; | ✔️  | &nbsp;  | ✔️ 
Alkalmazás-rendszergazda | Katalógus tulajdonosa  | &nbsp;  | &nbsp; | ✔️  | &nbsp;
Cloud Application Administrator | Katalógus tulajdonosa  | &nbsp;  | &nbsp; | ✔️  | &nbsp;
User | Katalógus tulajdonosa | Csak akkor, ha a csoport tulajdonosa | Csak akkor, ha a csoport tulajdonosa | Csak akkor, ha az alkalmazás tulajdonosa  | &nbsp;

\*A csoport nem hozzárendelhető szerepkör; Ez a isAssignableToRole = false. Ha egy csoport szerepkörhöz rendelhető, akkor a hozzáférési csomagot létrehozó személynek a szerepkörhöz hozzárendelhető csoport tulajdonosának is kell lennie.

**K:** Nem találom a "hozzárendelés eltávolítása" lehetőséget a "hozzárendelt szerepkörök" lehetőségnél. Hogyan törölni a szerepkör-hozzárendelést egy felhasználóhoz?

**A:** Ez a válasz csak prémium szintű Azure AD P1 szervezetekre alkalmazható.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és nyissa meg **Azure Active Directory**.
1. Válassza a felhasználók lehetőséget, és nyisson meg egy felhasználói profilt.
1. Válassza ki a **hozzárendelt szerepkörök** elemet.
1. Válassza a fogaskerék ikont. Megnyílik egy panel, amely megadhatja ezt az információt. A közvetlen hozzárendelések mellett "Eltávolítás" gomb is található. A közvetett szerepkör-hozzárendelés eltávolításához távolítsa el a felhasználót a szerepkörhöz hozzárendelt csoportból.

**K:** Hogyan a szerepkörhöz hozzárendelhető összes csoportot megtekintheti?

**A:** Kövesse az alábbi lépéseket:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és nyissa meg **Azure Active Directory**.
1. Válassza   >  **a csoportok minden csoport** elemet.
1. Válassza a **szűrők hozzáadása** lehetőséget.
1. Szűrés a **szerepkörhöz rendelhető**.

**K:** Hogyan tudni, hogy melyik szerepkört rendeli hozzá közvetlenül vagy közvetve a résztvevőhöz?

**A:** Kövesse az alábbi lépéseket:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és nyissa meg **Azure Active Directory**.
1. Válassza a felhasználók lehetőséget, és nyisson meg egy felhasználói profilt.
1. Válassza a **hozzárendelt szerepkörök** lehetőséget, majd:

    - Prémium szintű Azure AD P1 licenccel rendelkező szervezetek: válassza a fogaskerék ikont. Megnyílik egy panel, amely megadhatja ezt az információt.
    - Prémium szintű Azure AD P2 licencelt szervezeteknél: a **tagság** oszlopban talál közvetlen és örökölt licencelési információkat.

**K:** Miért kell kikényszeríteni egy új felhőalapú csoport létrehozását a szerepkörhöz való hozzárendeléshez?  

**A:** Ha egy meglévő csoportot rendel hozzá egy szerepkörhöz, a meglévő csoport tulajdonosa további tagokat adhat hozzá ehhez a csoporthoz anélkül, hogy az új tagok betartják a szerepkört. Mivel a szerepkörhöz hozzárendelhető csoportok hatékonyak, számos korlátozást teszünk elérhetővé a védelemhez. Nem kell módosítania a csoportot, amely a csoportot kezelő személy számára meglepő lenne.

## <a name="next-steps"></a>Következő lépések

- [Felhőbeli csoportok használata a szerepkör-hozzárendelések kezeléséhez](groups-concept.md)
- [Szerepkörhöz hozzárendelhető csoport létrehozása](groups-create-eligible.md)
