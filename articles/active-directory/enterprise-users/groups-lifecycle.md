---
title: Microsoft 365 csoportok elévülésének beállítása – Azure Active Directory | Microsoft Docs
description: Microsoft 365 csoportok lejáratának beállítása Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 34ac4a3b7f55c42711e4bc33c75bba964b49c65e
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "106551628"
---
# <a name="configure-the-expiration-policy-for-microsoft-365-groups"></a>Microsoft 365 csoportok elévülési szabályzatának konfigurálása

Ebből a cikkből megtudhatja, hogyan kezelheti Microsoft 365 csoportok életciklusát egy lejárati szabályzat beállításával. Az elévülési szabályzatot csak Azure Active Directory (Azure AD) Microsoft 365 csoportjaihoz állíthatja be.

Miután beállított egy csoportot a lejárat után:

- A felhasználói tevékenységgel rendelkező csoportokat a rendszer automatikusan megújítja a közeljövőben.
- A csoport tulajdonosai értesítést kapnak a csoport megújításáról, ha a csoport nem automatikusan megújul.
- A nem megújított csoportok törlődnek.
- A törölt Microsoft 365 csoportok 30 napon belül visszaállíthatók a csoport tulajdonosai vagy a rendszergazda számára.

Jelenleg csak egy lejárati szabályzat konfigurálható egy Azure AD-szervezet összes Microsoft 365 csoportján.

> [!NOTE]
> Microsoft 365 csoportok elévülési szabályzatának konfigurálása és használata megköveteli, hogy az összes olyan csoport tagjaihoz, amelyekhez a lejárati szabályzat érvényes, de ne rendeljen hozzá prémium szintű Azure AD licenceket.

További információ az Azure AD PowerShell-parancsmagok letöltéséről és telepítéséről: [Azure Active Directory PowerShell a Graph 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137).

## <a name="activity-based-automatic-renewal"></a>Tevékenységalapú automatikus megújítás

Az Azure AD-intelligenciával a csoportok mostantól automatikusan megújulnak, attól függően, hogy legutóbb használták-e őket. Ez a szolgáltatás szükségtelenné teszi a csoport tulajdonosai számára a manuális beavatkozást, mivel az Microsoft 365-szolgáltatások, például az Outlook, a SharePoint vagy a Teams különböző csoportjaiban található felhasználói tevékenységen alapul. Ha például egy tulajdonos vagy egy csoporttag hasonló módon tölt fel egy dokumentumot a SharePointban, látogasson el a csapat csatornára, vagy küldjön egy e-mailt a csoportnak az Outlookban, a csoport a csoport érvényességének lejárta előtt 35 nappal automatikusan megújul, és a tulajdonos nem kap megújítási értesítéseket.

### <a name="activities-that-automatically-renew-group-expiration"></a>A csoport lejáratát automatikusan megújító tevékenységek

A következő felhasználói műveletek az automatikus csoport megújítását okozzák:

- SharePoint: fájlok megtekintése, szerkesztése, letöltése, áthelyezése, megosztása vagy feltöltése
- Outlook: csatlakozás csoport, olvasási/írási csoport üzenete a csoport területéről, például egy üzenet (az Outlook Web Accessben)
- Csapatok: csapatos csatorna meglátogatása

### <a name="auditing-and-reporting"></a>Naplózás és jelentéskészítés

A rendszergazdák az Azure AD-ben lekérhetik az automatikusan megújított csoportok listáját a tevékenység-naplózási naplókból.

![Csoportok automatikus megújítása tevékenység alapján](./media/groups-lifecycle/audit-logs-autorenew-group.png)

## <a name="roles-and-permissions"></a>Szerepkörök és engedélyek

A következők olyan szerepkörök, amelyek az Azure AD-ben Microsoft 365 csoportok számára a lejárat konfigurálását és használatát is használhatják.

Szerepkör | Engedélyek
-------- | --------
Globális rendszergazda, csoport rendszergazdája vagy felhasználói rendszergazda | Létrehozhatja, elolvashatja, frissítheti vagy törölheti a Microsoft 365 csoportok lejárati szabályzatának beállításait<br>Bármely Microsoft 365 csoport megújítható
Felhasználó | Megújíthatja a saját Microsoft 365 csoportját<br>Visszaállíthatja a saját Microsoft 365 csoportját<br>A lejárati szabályzat beállításainak olvasása

A törölt csoportok visszaállítására vonatkozó engedélyekkel kapcsolatos további információkért lásd: [törölt Microsoft 365 csoport visszaállítása Azure Active Directoryban](groups-restore-deleted.md).

## <a name="set-group-expiration"></a>Csoportok elévülésének beállítása

1. Nyissa meg az [Azure ad felügyeleti központot](https://aad.portal.azure.com) egy olyan fiókkal, amely globális rendszergazda az Azure ad-szervezetben.

2. Válassza a **csoportok** lehetőséget, majd válassza a **lejárat** lehetőséget a lejárat beállításainak megnyitásához.
  
   ![Csoportok lejárati beállításai](./media/groups-lifecycle/expiration-settings.png)

3. A **lejárat** lapon a következőket végezheti el:

    - Állítsa be a csoport élettartamát napokban. Kiválaszthatja az egyik előre definiált értéket, vagy egy egyéni értéket (30 nap vagy több értéknek kell lennie).
    - Itt adhatja meg azt az e-mail címet, ahol a megújítási és lejárati értesítéseket el kell juttatni, ha egy csoportnak nincs tulajdonosa.
    - Válassza ki, hogy mely Microsoft 365 csoportok lejárnak. A következőhöz adhatja meg a lejárat időtartamát:
      - **Az összes** Microsoft 365 csoportok
      - A **kiválasztott** Microsoft 365 csoportok listája
      - **Nincs** az összes csoport lejáratának korlátozása
    - Ha elkészült, mentse a beállításokat a **Mentés gombra** kattintva.

> [!NOTE]
> - Amikor első alkalommal állítja be a lejáratot, a lejárati időintervallumnál régebbi csoportok 35 napra vannak beállítva, kivéve, ha a csoport automatikusan megújul, vagy a tulajdonos megújítja.
> - Dinamikus csoport törlésekor és visszaállításakor a rendszer új csoportként tekinti meg, és a szabálynak megfelelően újra feltölti őket. Ez a folyamat akár 24 órát is igénybe vehet.
> - A csapatokban használt csoportok lejárati megjegyzései megjelennek a csapatok tulajdonosai hírcsatornában.
> - Ha engedélyezi a lejáratot a kiválasztott csoportokhoz, legfeljebb 500 csoportot adhat hozzá a listához. Ha több mint 500 csoportot kell felvennie, akkor az összes csoport esetében engedélyezheti a lejáratot. Ebben az esetben az 500-Group korlátozás nem érvényes.

## <a name="email-notifications"></a>E-mail-értesítések

Ha a csoportok nincsenek automatikusan megújítva, a rendszer az e-mailes értesítéseket (például a csoport tulajdonosait 30 nap, 15 nap és 1 nap) elküldi az Microsoft 365 csoport tulajdonosainak. Az e-mailek nyelvét a csoportok tulajdonosának előnyben részesített nyelve vagy az Azure AD nyelvi beállítása határozza meg. Ha a csoport tulajdonosa definiált egy előnyben részesített nyelvet, vagy több tulajdonosnak azonos az előnyben részesített nyelve, akkor a rendszer ezt a nyelvet használja. Minden más esetben az Azure AD nyelvi beállítását használja a rendszer.

![Lejárati e-mail értesítések](./media/groups-lifecycle/expiration-notification.png)

A csoport **tulajdonosai értesítési** e-mailben a csoport tulajdonosainak közvetlenül érhetik el a csoport részletei lapot a [hozzáférési panelen](https://account.activedirectory.windowsazure.com/r#/applications). Ebben az esetben a felhasználók további információkat kaphatnak a csoportról, például a leírásáról, a legutóbbi megújítás időpontját, a lejárat idejét, valamint a csoport megújításának lehetőségét is. A csoport részletei lap a Microsoft 365 csoport erőforrásaira mutató hivatkozásokat is tartalmaz, így a csoport tulajdonosa kényelmesen megtekintheti a csoport tartalmát és tevékenységeit.

Ha egy csoport lejár, a csoport egy nappal a lejárati dátum után törlődik. A rendszer elküld egy e-mailes értesítést a Microsoft 365 csoport tulajdonosainak a Microsoft 365 csoport lejáratáról és későbbi törléséről.

![E-mail értesítések csoport törlése](./media/groups-lifecycle/deletion-notification.png)

A csoport a törléstől számított 30 napon belül visszaállítható, ha a **visszaállítási csoportot** vagy a PowerShell-parancsmagokat használja a [törölt Microsoft 365 csoport visszaállítása Azure Active Directoryban](groups-restore-deleted.md)című témakörben leírtak szerint. Vegye figyelembe, hogy a 30 napos csoportos visszaállítási időszak nem szabható testre.

Ha a visszaállítani kívánt csoport dokumentumokat, SharePoint-webhelyeket vagy más állandó objektumokat tartalmaz, akár 24 órát is igénybe vehet, hogy teljesen visszaállítsa a csoportot és annak tartalmát.

## <a name="how-to-retrieve-microsoft-365-group-expiration-date"></a>Microsoft 365 csoport lejárati dátumának beolvasása

A hozzáférési panelen kívül a felhasználók megtekinthetik a csoport részleteit, beleértve a lejárati dátumot és az utolsó megújított dátumot, a Microsoft 365 csoport lejárati idejét Microsoft Graph REST API Bétaverzióból kérheti le. a expirationDateTime tulajdonság engedélyezve van Microsoft Graph Beta-ban. Lekérhető GET kéréssel. További részletekért tekintse meg [ezt a példát](/graph/api/group-get?view=graph-rest-beta#example).

> [!NOTE]
> A csoporttagságok a hozzáférési panelen való kezeléséhez a "nem" értékre kell állítani a "hozzáférés a csoportok számára a hozzáférési panelen" beállítást a Azure Active Directory csoportok általános beállításában.

## <a name="how-microsoft-365-group-expiration-works-with-a-mailbox-on-legal-hold"></a>Hogyan működik Microsoft 365 csoport lejárata a jogi úton lévő postafiókkal

Ha egy csoport lejár, és törlődik, akkor 30 nappal azután, hogy törli a csoport adatait olyan alkalmazásokból, mint a Planner, a helyek vagy a csapatok véglegesen törölve lettek, de a jogi úton tartott csoportos postaláda megmarad, és nem véglegesen törlődik. A rendszergazda az Exchange-parancsmagok használatával visszaállíthatja a postaládát az adatlekérdezéshez.

## <a name="how-microsoft-365-group-expiration-works-with-retention-policy"></a>Hogyan működik Microsoft 365 csoport lejárata az adatmegőrzési házirenddel

Az adatmegőrzési szabályt a biztonsági és megfelelőségi központ konfigurálja. Ha adatmegőrzési szabályzatot állított be Microsoft 365 csoportokhoz, a csoport lejárati idejét és törlését követően a csoport postaládájában lévő csoportok és a csoport helyének fájljai megmaradnak az adatmegőrzési szabályban meghatározott számú nap megőrzési tárolójában. A felhasználók a lejárat után nem látják a csoportot vagy annak tartalmát, de az e-Discovery segítségével helyreállíthatja a helyet és a postaláda-adatokat.

## <a name="powershell-examples"></a>PowerShell-példák

Íme néhány példa arra, hogyan használhatók a PowerShell-parancsmagok az Azure AD-szervezetben Microsoft 365 csoportok lejárati beállításainak konfigurálásához:

1. Telepítse a PowerShell 2.0 modult, és jelentkezzen be a PowerShell parancssorába:

   ``` PowerShell
   Install-Module -Name AzureAD
   Connect-AzureAD
   ```

1. A lejárati beállítások konfigurálása a New-AzureADMSGroupLifecyclePolicy parancsmaggal állítható be az Azure AD-szervezet összes Microsoft 365 csoportjának élettartama 365 napra. A tulajdonosokkal nem rendelkező Microsoft 365 csoportok megújítására vonatkozó értesítések küldése a következőnek: " emailaddress@contoso.com "
  
   ``` PowerShell
   New-AzureADMSGroupLifecyclePolicy -GroupLifetimeInDays 365 -ManagedGroupTypes All -AlternateNotificationEmails emailaddress@contoso.com
   ```

1. A Get-AzureADMSGroupLifecyclePolicy meglévő házirend beolvasása: Ez a parancsmag lekérdezi az aktuálisan konfigurált Microsoft 365 csoport lejárati beállításait. Ebben a példában a következőket láthatja:

   - A szabályzat azonosítója
   - Az Azure AD-szervezet összes Microsoft 365 csoportjának élettartama 365 napra van állítva
   - A tulajdonosokkal nem rendelkező Microsoft 365 csoportokra vonatkozó megújítási értesítések küldése a következőnek: " emailaddress@contoso.com ."
  
   ```powershell
   Get-AzureADMSGroupLifecyclePolicy
  
   ID                                    GroupLifetimeInDays ManagedGroupTypes AlternateNotificationEmails
   --                                    ------------------- ----------------- ---------------------------
   26fcc232-d1c3-4375-b68d-15c296f1f077  365                 All               emailaddress@contoso.com
   ```

1. A meglévő szabályzat frissítése – AzureADMSGroupLifecyclePolicy: Ez a parancsmag egy meglévő szabályzat frissítésére szolgál. Az alábbi példában a csoport élettartama a meglévő szabályzatban 365 nap és 180 nap között változik.
  
   ```powershell
   Set-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -GroupLifetimeInDays 180 -AlternateNotificationEmails "emailaddress@contoso.com"
   ```
  
1. Adott csoportok hozzáadása a Policy Add-AzureADMSLifecyclePolicyGroup: Ez a parancsmag hozzáad egy csoportot az életciklus-szabályzathoz. Lássunk erre egy példát:
  
   ```powershell
   Add-AzureADMSLifecyclePolicyGroup -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -groupId "cffd97bd-6b91-4c4e-b553-6918a320211c"
   ```
  
1. Távolítsa el a meglévő Policy Remove-AzureADMSGroupLifecyclePolicy: Ez a parancsmag törli a Microsoft 365 csoport lejárati beállításait, de megköveteli a házirend-azonosítót. Ez a parancsmag letiltja Microsoft 365 csoportok lejáratát.
  
   ```powershell
   Remove-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077"
   ```
  
A következő parancsmagok segítségével konfigurálhatja a szabályzatot részletesebben. További információ: PowerShell- [dokumentáció](/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#groups).

- Get-AzureADMSGroupLifecyclePolicy
- New-AzureADMSGroupLifecyclePolicy
- Set-AzureADMSGroupLifecyclePolicy
- Remove-AzureADMSGroupLifecyclePolicy
- Add-AzureADMSLifecyclePolicyGroup
- Remove-AzureADMSLifecyclePolicyGroup
- Reset-AzureADMSLifeCycleGroup
- Get-AzureADMSLifecyclePolicyGroup

## <a name="next-steps"></a>Következő lépések

Ezek a cikkek további információkat nyújtanak az Azure AD-csoportokról.

- [Meglévő csoportok megtekintése](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Csoportbeállítások kezelése](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Csoporttagok kezelése](../fundamentals/active-directory-groups-members-azure-portal.md)
- [Csoporttagságok kezelése](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [A csoportban lévő felhasználók dinamikus szabályainak kezelése](groups-dynamic-membership.md)
