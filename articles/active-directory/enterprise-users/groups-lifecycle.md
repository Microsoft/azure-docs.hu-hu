---
title: Lejárat beállítása a Microsoft 365 számára – Azure Active Directory | Microsoft Docs
description: Lejárat beállítása a Microsoft 365 csoportok Azure Active Directory
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
ms.openlocfilehash: 8af1a5e73592dc1c3392f0bc1fecfe6139a54710
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529829"
---
# <a name="configure-the-expiration-policy-for-microsoft-365-groups"></a>Az elévülési szabályzat konfigurálása Microsoft 365 csoportokhoz

Ez a cikk bemutatja, hogyan kezelheti a csoportcsoportok Microsoft 365 elévülési szabályzat beállításával. Az elévülési szabályzatot csak a Microsoft 365 (Azure AD Azure Active Directory állíthatja be.

Miután beállította, hogy a csoport lejárjon:

- A felhasználói tevékenységekkel rendelkező csoportok automatikusan megújulnak, amint a lejárat közeledik.
- A csoport tulajdonosai értesítést kapnak a csoport megújítására, ha a csoport nem újul meg automatikusan.
- A nem megújított csoportokat a rendszer törli.
- A Microsoft 365 csoport tulajdonosai vagy a rendszergazda 30 napon belül visszaállíthatja a törölt csoportokat.

Jelenleg csak egy elévülési szabályzat konfigurálható egy Azure AD Microsoft 365 összes csoporthoz.

> [!NOTE]
> A Microsoft 365-csoportok elévülési szabályzatának konfigurálásához és használatához prémium szintű Azure AD-licenceket nem feltétlenül kell hozzárendelni minden olyan csoport tagjaihoz, amelyekre érvényes a lejárati szabályzat.

További információ az Azure AD PowerShell-parancsmagok letöltésével és telepítésével kapcsolatban: [Azure Active Directory PowerShell for Graph 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137).

## <a name="activity-based-automatic-renewal"></a>Tevékenységalapú automatikus megújítás

Az Azure AD-intelligencia segítségével a csoportok automatikusan megújulnak attól függően, hogy a közelmúltban használták-e őket. Ez a funkció szükségtelenné teszi a csoporttulajdonosok manuális beavatkozását, mivel az olyan csoportokban végzett felhasználói tevékenységeken alapul, mint az Outlook, a SharePoint vagy a Teams Microsoft 365 csoportokban. Ha például egy tulajdonos vagy csoporttag feltölt egy dokumentumot a SharePointba, Teams-csatornát keres fel, vagy e-mailt küld a csoportnak az Outlookban, a csoport körülbelül 35 nappal a csoport lejárata előtt automatikusan megújul, és a tulajdonos nem kap megújítási értesítéseket.

### <a name="activities-that-automatically-renew-group-expiration"></a>A csoport lejáratát automatikusan megújító tevékenységek

Az alábbi felhasználói műveletek a csoport automatikus megújítását okják:

- SharePoint: Fájlok megtekintése, szerkesztése, letöltése, áthelyezése, megosztása vagy feltöltése
- Outlook: Csoporthoz való csatlakozás, csoportüzenet olvasása/írása csoporttérből, Mint egy üzenetben (az Outlook Web Accessben)
- Teams: Teams-csatorna felkeresve

### <a name="auditing-and-reporting"></a>Naplózás és jelentéskészítés

A rendszergazdák az Azure AD tevékenységnaplóiból kaphatják meg az automatikusan megújított csoportok listáját.

![Csoportok automatikus megújítása tevékenység alapján](./media/groups-lifecycle/audit-logs-autorenew-group.png)

## <a name="roles-and-permissions"></a>Szerepkörök és engedélyek

Az alábbiakban olyan szerepköröket ismertetünk, amelyek az Azure AD-beli Microsoft 365 konfigurálhatók és használhatók elévülésre.

Szerepkör | Engedélyek
-------- | --------
globális rendszergazda, csoport-rendszergazda vagy felhasználói rendszergazda | Létrehozhatja, olvashatja, frissítheti vagy törölheti a Microsoft 365 elévülési szabályzat beállításait<br>Bármely új Microsoft 365 megújítható
Felhasználó | Megújíthat egy Microsoft 365, amely a tulajdonában van<br>Visszaállíthat egy Microsoft 365, amely a tulajdonában van<br>Olvashatja az elévülési szabályzat beállításait

A törölt csoportok visszaállítására vonatkozó engedélyekkel kapcsolatos további információkért lásd: Törölt csoport Microsoft 365 visszaállítása [a Azure Active Directory.](groups-restore-deleted.md)

## <a name="set-group-expiration"></a>Csoportok elévülésének beállítása

1. Nyissa meg [az Azure AD felügyeleti központot](https://aad.portal.azure.com) egy olyan fiókkal, amely az Azure AD-szervezet globális rendszergazdája.

2. Válassza **a Csoportok** lehetőséget, majd a Lejárat **lehetőséget** az elévülési beállítások megnyitásához.
  
   ![Csoportok elévülési beállításai](./media/groups-lifecycle/expiration-settings.png)

3. A Lejárat **lapon** a következőt használhatja:

    - Állítsa be a csoport élettartamát napokban. Kiválaszthat egy előre beállított értéket vagy egy egyéni értéket (30 vagy több napnak kell lennie).
    - Adjon meg egy e-mail-címet, ahová a megújítási és lejárati értesítéseket küldeni kell, ha egy csoportnak nincs tulajdonosa.
    - Válassza ki, Microsoft 365 mely csoportok járnak le. A lejárati időt a következőre állíthatja be:
      - **All (Összes)** Microsoft 365 csoportok
      - A kijelölt **csoportokat Microsoft 365** listája
      - **Nincs** az összes csoport lejáratának korlátozására
    - Ha végzett, mentse a beállításokat a Mentés **lehetőség kiválasztásával.**

> [!NOTE]
> - A lejárat első beállításakor a lejárati időnél régebbi csoportok lejárata 35 napra lesz beállítva, kivéve, ha a csoport automatikusan megújul, vagy a tulajdonos megújítja azt.
> - Egy dinamikus csoport törlésekor és visszaállításakor az új csoportként lesz látható, és újra ki lesz töltve a szabálynak megfelelően. Ez a folyamat akár 24 órát is igénybe vehet.
> - A Teamsben használt csoportok lejárati értesítései a Teams-tulajdonosok hírcsatornában jelennek meg.
> - Ha engedélyezi a lejárati időt a kiválasztott csoportok számára, legfeljebb 500 csoportot adhat hozzá a listához. Ha 500-asnál több csoportot kell hozzáadnia, minden csoport esetében engedélyezheti a lejárati időt. Ebben a forgatókönyvben az 500 csoportra vonatkozó korlátozás nem érvényes.

## <a name="email-notifications"></a>E-mail-értesítések

Ha a csoportok nem újulnak meg automatikusan, az ehhez hasonló e-mail-értesítéseket a rendszer a Microsoft 365-csoport tulajdonosainak a csoport lejárta előtt 30 nappal, 15 nappal és 1 nappal küldi el. Az e-mail nyelvét a csoportok tulajdonosának előnyben részesített nyelve vagy az Azure AD nyelvi beállítása határozza meg. Ha a csoport tulajdonosa meghatározta az előnyben részesített nyelvet, vagy ha több tulajdonos is ugyanazt a nyelvet használja, akkor a felhasználó ezt a nyelvet használja. Minden más esetben az Azure AD nyelvi beállítását használja a rendszer.

![Elévülési e-mail-értesítések](./media/groups-lifecycle/expiration-notification.png)

A Csoport **megújítása értesítési** e-mailben a csoporttulajdonosok közvetlenül hozzáférhetnek a csoportadatok oldalhoz a [hozzáférési panel.](https://account.activedirectory.windowsazure.com/r#/applications) Itt a felhasználók további információkat kaphatnak a csoportról, például annak leírását, legutóbbi megújítását, lejárati időét, valamint a csoport megújításának képességét. A csoport részleteit tartalmazó oldal mostantól hivatkozásokat is tartalmaz Microsoft 365 csoporterőforrásokra, hogy a csoport tulajdonosa kényelmesen megtekint tudja a csoport tartalmát és tevékenységét.

Amikor egy csoport lejár, a csoport a lejárati dátum után egy nappal törlődik. Az ilyen e-mailes értesítést a rendszer elküldi a Microsoft 365 csoport tulajdonosainak, amely tájékoztatja őket a csoport lejáratával és későbbi Microsoft 365 törlésével kapcsolatban.

![Csoporttörlési e-mail-értesítések](./media/groups-lifecycle/deletion-notification.png)

A csoport a törlést követő 30 napon  belül visszaállítható a Csoport visszaállítása lehetőség kiválasztásával vagy PowerShell-parancsmagok használatával a törölt Microsoft 365 csoport visszaállítása a [Azure Active Directory.](groups-restore-deleted.md) Vegye figyelembe, hogy a 30 napos csoport-visszaállítási időszak nem szabható testre.

Ha a visszaállítandó csoport dokumentumokat, SharePoint-webhelyeket vagy más állandó objektumokat tartalmaz, akár 24 órába is szükség lehet a csoport és a tartalma teljes visszaállítására.

## <a name="how-to-retrieve-microsoft-365-group-expiration-date"></a>Csoport lejárati Microsoft 365 lekérése

Amellett, hozzáférési panel a felhasználók megtekinthetik a csoport részleteit, például a lejárati és a legutóbbi megújítás dátumát, a Microsoft 365-csoport lejárati dátuma a bétaverzióból Microsoft Graph REST API le. expirationDateTime, mivel a bétaverzióban engedélyezve van egy Microsoft Graph tulajdonság. GET kéréssel kérhető le. További részletekért tekintse meg ezt [a példát.](/graph/api/group-get?view=graph-rest-beta#example&preserve-view=true)

> [!NOTE]
> A csoporttagság kezeléséhez hozzáférési panel "A csoportokhoz való hozzáférés korlátozása a hozzáférési panel-ban" beállítást "Nem" Azure Active Directory csoportok általános beállításában.

## <a name="how-microsoft-365-group-expiration-works-with-a-mailbox-on-legal-hold"></a>A Microsoft 365 elévülésének működése a jogi hold-postaládával

Amikor egy csoport lejár és törlődik, a törlés után 30 nappal törli a csoport adatait az olyan alkalmazásokból, mint a Planner, a Sites vagy a Teams, véglegesen törölve lesz, de a csoport jogi megőrzésre használt postaláda megmarad, és nem törlődik véglegesen. A rendszergazda Exchange-parancsmagokkal visszaállíthatja a postaládát az adatok lekéréséhez.

## <a name="how-microsoft-365-group-expiration-works-with-retention-policy"></a>A Microsoft 365 elévülésének működése az adatmegőrzési szabályzattal

Az adatmegőrzési szabályzatot a Biztonsági és megfelelőségi központ konfigurálja. Ha megőrzési szabályzatot adott meg Microsoft 365-csoportokhoz, a csoport lejártakor és törlésekor a csoport postaládájában és a csoporthely fájljaiban lévő csoportbeszélgetések a megőrzési házirendben meghatározott számú napig maradnak meg a megőrzési tárolóban. A felhasználók a lejárat után nem látják a csoportot vagy annak tartalmát, de e-felderítéssel helyreállítják a hely és a postaláda adatait.

## <a name="powershell-examples"></a>PowerShell-példák

Íme néhány példa arra, hogyan használhatja a PowerShell-parancsmagokat az Azure AD Microsoft 365 csoport elévülési beállításainak konfigurálhoz:

1. Telepítse a PowerShell 2.0-s modult, és jelentkezzen be a PowerShell parancssorában:

   ``` PowerShell
   Install-Module -Name AzureAD
   Connect-AzureAD
   ```

1. Az elévülési beállítások konfigurálása A New-AzureADMSGroupLifecyclePolicy parancsmag használatával állítsa az Azure AD-szervezetben Microsoft 365 összes csoport élettartamát 365 napra. A tulajdonosok nélküli Microsoft 365 megújítási értesítéseket a rendszer a"-nak emailaddress@contoso.com küldi el.
  
   ``` PowerShell
   New-AzureADMSGroupLifecyclePolicy -GroupLifetimeInDays 365 -ManagedGroupTypes All -AlternateNotificationEmails emailaddress@contoso.com
   ```

1. A meglévő Get-AzureADMSGroupLifecyclePolicy szabályzat lekérése: Ez Microsoft 365 parancsmag lekéri az aktuálisan konfigurált Microsoft 365 csoportleévülési beállításokat. Ebben a példában a következőt láthatja:

   - A szabályzat azonosítója
   - Az Azure AD Microsoft 365 összes csoport élettartama 365 napra van beállítva
   - A tulajdonosok nélküli Microsoft 365 megújítási értesítéseket a rendszer a " emailaddress@contoso.com .
  
   ```powershell
   Get-AzureADMSGroupLifecyclePolicy
  
   ID                                    GroupLifetimeInDays ManagedGroupTypes AlternateNotificationEmails
   --                                    ------------------- ----------------- ---------------------------
   26fcc232-d1c3-4375-b68d-15c296f1f077  365                 All               emailaddress@contoso.com
   ```

1. Frissítse a meglévő Set-AzureADMSGroupLifecyclePolicy szabályzatot: Ez a parancsmag egy meglévő szabályzat frissítésére szolgál. Az alábbi példában a csoport élettartama a meglévő szabályzatban 365 napról 180 napra változik.
  
   ```powershell
   Set-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -GroupLifetimeInDays 180 -AlternateNotificationEmails "emailaddress@contoso.com"
   ```
  
1. Adjon hozzá konkrét csoportokat az Add-AzureADMSLifecyclePolicyGroup szabályzathoz: Ez a parancsmag egy csoportot ad hozzá az életciklus-szabályzathoz. Lássunk erre egy példát:
  
   ```powershell
   Add-AzureADMSLifecyclePolicyGroup -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -groupId "cffd97bd-6b91-4c4e-b553-6918a320211c"
   ```
  
1. Távolítsa el a meglévő Remove-AzureADMSGroupLifecyclePolicy szabályzatot: Ez a parancsmag törli Microsoft 365 csoport lejárati beállításait, de a szabályzat azonosítóját igényli. Ez a parancsmag letiltja a Microsoft 365 elévülését.
  
   ```powershell
   Remove-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077"
   ```
  
Az alábbi parancsmagokkal részletesebben konfigurálhatja a szabályzatot. További információt a [PowerShell dokumentációjában talál.](/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#groups)

- Get-AzureADMSGroupLifecyclePolicy
- New-AzureADMSGroupLifecyclePolicy
- Set-AzureADMSGroupLifecyclePolicy
- Remove-AzureADMSGroupLifecyclePolicy
- Add-AzureADMSLifecyclePolicyGroup
- Remove-AzureADMSLifecyclePolicyGroup
- Reset-AzureADMSLifeCycleGroup
- Get-AzureADMSLifecyclePolicyGroup

## <a name="next-steps"></a>Következő lépések

Ezek a cikkek további információt nyújtanak az Azure AD-csoportokról.

- [Meglévő csoportok megtekintése](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Csoportbeállítások kezelése](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Csoporttagok kezelése](../fundamentals/active-directory-groups-members-azure-portal.md)
- [Csoporttagságok kezelése](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [A csoportban lévő felhasználók dinamikus szabályainak kezelése](groups-dynamic-membership.md)
