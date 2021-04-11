---
title: Számlázás beállítása a Microsoft-ügyfélszerződéshez – Azure
description: Megtudhatja, hogyan állíthatja be számlázási fiókját egy Microsoft-ügyfélszerződéshez. Tekintse meg a telepítés előfeltételeit, és tekintse meg a többi elérhető erőforrást.
author: amberbhargava
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 03/19/2021
ms.author: banders
ms.openlocfilehash: 15aa3acab9fe98a4c2f5103ba211dde34220c54e
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2021
ms.locfileid: "107255684"
---
# <a name="set-up-your-billing-account-for-a-microsoft-customer-agreement"></a>Számlázási fiók beállítása Microsoft-ügyfélszerződéshez

Ha a közvetlen Nagyvállalati Szerződéséhez kapcsolódó regisztrációja lejárt vagy hamarosan lejár, egy Microsoft Ügyfélszerződéssel megújíthatja a regisztrációját. Ez a cikk ismerteti, hogyan változik a meglévő számlázása a beállítás elvégzése után, és végigvezeti az új számlázási fiókja beállítási lépésein. A lejáró közvetett Nagyvállalati Szerződések jelenleg nem újíthatók meg Microsoft Ügyfélszerződéssel.

A megújítás a következő lépéseket tartalmazza:

1. Fogadja el az új Microsoft-ügyfélszerződést. A Microsoft helyszíni munkatársa segítségével ismerje meg az új szerződés részleteit, majd fogadja el őket.
2. Állítsa be az új számlázási fiókot, amely az új Microsoft-ügyfélszerződéshez lett létrehozva.

A számlázási fiók beállításához át kell irányítania az Azure-előfizetései számlázását a Nagyvállalati Szerződés fiókjából az új fiókjába. A telepítő nem érinti az előfizetésében futó Azure-szolgáltatásokat. Módosítja azonban az előfizetés számlázási módját.

- Az [EA Portal](https://ea.azure.com) helyett Azure-beli szolgáltatásait és számlázását az [Azure Portalon](https://portal.azure.com) fogja kezelni.
- A díjakról havonta digitális számlát fog kapni. A számlát az Azure Költségkezelés + számlázás oldalon tekintheti meg és elemezheti.
- Az Nagyvállalati Szerződés regisztrációjában szereplő részlegek és fiók helyett az új fiók számlázási struktúráját és hatóköreit fogja használni a számlázás kezeléséhez és rendszerezéséhez.

A beállítás megkezdése előtt érdemes elvégeznie az alábbi műveleteket:

- **Az új számlázási fiók megismerése**
  - Az új fiókja leegyszerűsíti a szervezet számlázását. [Az új számlázási fiókjának rövid áttekintése](../understand/mca-overview.md)
- **A beállítás végrehajtásához szükséges hozzáférés ellenőrzése**
  - Csak bizonyos rendszergazdai jogosultságokkal rendelkező felhasználók hajthatják végre a beállítást. Ellenőrizze, hogy rendelkezik-e a [beállítás végrehajtásához szükséges hozzáféréssel](#access-required-to-complete-the-setup).
- **A számlázási hierarchia változásainak megismerése**
  - Az új fiók számlázási rendszere eltér a Nagyvállalati Szerződés regisztrációjához kapcsolódó rendszertől. [Ismerje meg az új fiók számlázási hierarchiáját](#understand-changes-to-your-billing-hierarchy).
- **A számlázási rendszergazda hozzáférési jogosultságait érintő változások megismerése**
  - A Nagyvállalati Szerződés regisztrációjának rendszergazdái hozzáférhetnek az új fiók számlázási hatóköreihez.[Ismerje meg a hozzáférésüket érintő változásokat](#changes-to-billing-administrator-access).
- **Azon nagyvállalati szerződéses szolgáltatások megtekintése, amelyeket az új fiók más szolgáltatásokra cserél**
  - Tekintse meg a Nagyvállalati Szerződés regisztrációjának azon szolgáltatásait, amelyeket az új fiók más szolgáltatásokra cserél.
- **A gyakori kérdésekre adott válaszok megtekintése**
  - Tudjon meg [további információkat](#additional-information), hogy még közelebbről megismerkedhessen a beállítási folyamattal.

## <a name="access-required-to-complete-the-setup"></a>A beállítás végrehajtásához szükséges hozzáférés

A beállítás befejezéséhez a következő hozzáférésre van szükség:

- Annak a számlázási profilnak a tulajdonosa, amely a Microsoft-ügyfélszerződés aláírásakor jött létre. A számlázási profilokkal kapcsolatos további információkért lásd [a számlázási profilok ismertetését](../understand/mca-overview.md#billing-profiles).  
&mdash; És &mdash;
- A megújított regisztráció vállalati rendszergazdája.

### <a name="start-migration-and-get-permission-needed-to-complete-setup"></a>Az áttelepítés megkezdése és a telepítés befejezéséhez szükséges engedély beszerzése

Az alábbi beállításokkal elindíthatja az EA-regisztrációjának áttelepítési élményét a Microsoft-ügyfél szerződésében.


- Jelentkezzen be az Azure Portalra annak a hivatkozásnak a használatával, amelyet a Microsoft-ügyfélszerződés aláírásakor kapott.

- Ha nincs a birtokában az e-mail-üzenet, jelentkezzen be a következő hivatkozás használatával. Cserélje le az `enrollmentNumber` értéket a megújított Nagyvállalati Szerződés regisztrációs számára.

  `https://portal.azure.com/#blade/Microsoft_Azure_EA/EATransitionToMCA/enrollmentId/<enrollmentNumber>`

Ha a vállalati rendszergazda és a Számlázási fiók tulajdonosi szerepköre vagy a számlázási profil szerepkör egyaránt szerepel, a Azure Portal a következő oldal jelenik meg. A váltáshoz továbbra is beállíthatja az EA-regisztrációkat és a Microsoft ügyfél-szerződés számlázási fiókját.

:::image type="content" source="./media/mca-setup-account/setup-billing-account-page.png" alt-text="A Számlázási fiók beállítása oldalon látható képernyőkép" lightbox="./media/mca-setup-account/setup-billing-account-page.png" :::

Ha nem rendelkezik vállalati rendszergazdai szerepkörrel a nagyvállalati szerződéshez vagy a számlázási profil tulajdonosi szerepköréhez a Microsoft ügyfél-szerződéshez, akkor a következő információk alapján kérheti le a szükséges hozzáférést a telepítés befejezéséhez.

### <a name="if-youre-not-an-enterprise-administrator-on-the-enrollment"></a>Amennyiben Ön nem vállalati rendszergazda a regisztráció tekintetében

Ha a Számlázási fiók vagy a számlázási profil tulajdonosi szerepköre van, de Ön nem vállalati rendszergazda, akkor a Azure Portal következő lapja jelenik meg.

:::image type="content" source="./media/mca-setup-account/setup-billing-account-page-not-ea-administrator.png" alt-text="Képernyőfelvétel a Számlázási fiók beállítása oldalon – a Nagyvállalati Szerződés-regisztrációk előkészítése a váltáshoz." lightbox="./media/mca-setup-account/setup-billing-account-page-not-ea-administrator.png" :::

Erre két lehetősége van:

- Kérje meg a vállalati rendszergazdát, hogy adja meg a vállalati rendszergazda szerepkört. További információ: [hozzon létre egy másik vállalati rendszergazdát](ea-portal-administration.md#create-another-enterprise-administrator).
-  Megadhatja a vállalati rendszergazdának a Számlázási fiók tulajdonosi vagy számlázási profiljának tulajdonosi szerepkörét. További információ: [a Azure Portal számlázási szerepköreinek kezelése](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

Ha a vállalati rendszergazda szerepkört kapja, másolja a hivatkozást a Számlázási fiók beállítása oldalon. Nyissa meg a webböngészőjében, és folytassa a Microsoft-ügyfél szerződésének beállítását. Ellenkező esetben küldje el a vállalati rendszergazdának.

### <a name="if-youre-not-an-owner-of-the-billing-profile"></a>Amennyiben Ön nem a számlázási profil tulajdonosa

Ha Ön vállalati rendszergazda, de nem rendelkezik számlázási fiókkal vagy számlázási profil tulajdonosi szerepkörrel a Microsoft-ügyfél szerződéséhez, akkor a következő oldal jelenik meg a Azure Portal.

Ha úgy véli, hogy rendelkezik a számlázási profil tulajdonosának hozzáférési jogosultsággal a Microsoft megfelelő ügyfelének szerződéséhez, és a következő üzenet jelenik meg, győződjön meg arról, hogy a megfelelő bérlőt használja a szervezet számára. Előfordulhat, hogy módosítania kell a címtárakat.

:::image type="content" source="./media/mca-setup-account/setup-billing-account-page-not-billing-account-profile-owner.png" alt-text="Képernyőfelvétel: a Számlázási fiók beállítása oldal – Microsoft ügyfél-szerződés számlázási fiókja." lightbox="./media/mca-setup-account/setup-billing-account-page-not-billing-account-profile-owner.png" :::

Erre két lehetősége van:

- Kérje meg egy meglévő számlázási fiók tulajdonosát, hogy adja meg a Számlázási fiók tulajdonosi vagy számlázási profiljának tulajdonosi szerepkörét. További információ: [a számlázási szerepkörök kezelése a Azure Portalban](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)
- Adja meg a vállalati rendszergazda szerepkört egy meglévő számlázási fiók tulajdonosának. További információ: [hozzon létre egy másik vállalati rendszergazdát](ea-portal-administration.md#create-another-enterprise-administrator).

Ha megkapja a Számlázási fiók tulajdonosi vagy számlázási profiljának tulajdonosi szerepkörét, másolja a hivatkozást a Számlázási fiók beállítása oldalon. Nyissa meg a webböngészőjében, és folytassa a Microsoft-ügyfél szerződésének beállítását. Ellenkező esetben küldje el a Számlázási fiók tulajdonosának hivatkozását.

## <a name="understand-changes-to-your-billing-hierarchy"></a>A számlázási hierarchia változásainak megismerése

Az új számlázási fiókja leegyszerűsíti a szervezet számlázását, valamint továbbfejlesztett számlázási és költségkezelési képességeket nyújt. Az alábbi ábra az új számlázási fiók számlázási rendszerét mutatja be.

![Az ea-mca-post-transition-hierarchy képe](./media/mca-setup-account/mca-post-transition-hierarchy.png)

1. A Microsoft-ügyfélszerződés számláinak kezeléséhez a számlázási fiókot használhatja. A vállalati rendszergazdák a számlázási fiók tulajdonosai lesznek. A számlázási fiókkal kapcsolatos további információkért lásd [a számlázási fiókok ismertetését](../understand/mca-overview.md#your-billing-account).
2. A számlázási profillal a nagyvállalati szerződéses regisztrációhoz hasonlóan kezelheti a szervezet számlázását. A vállalati rendszergazdák a számlázási profil tulajdonosai lesznek. A számlázási profilokkal kapcsolatos további információkért lásd [a számlázási profilok ismertetését](../understand/mca-overview.md#billing-profiles).
3. A költségek igény szerinti rendszerezését a nagyvállalati szerződéses regisztráció részlegeihez hasonlóan a számlaszakaszokkal teheti meg. A részlegekből számlázási szakaszok lesznek, a részlegszintű rendszergazdákból pedig a megfelelő számlázási szakaszok tulajdonosai. A számlázási szakaszokkal kapcsolatos további információkért lásd [a számlázási szakaszok ismertetését](../understand/mca-overview.md#invoice-sections).
4. A Nagyvállalati Szerződésben létrehozott fiókok nem támogatottak az új számlázási fiókban. A fiók előfizetései a részlegek megfelelő számlázási szakaszához tartoznak. A fióktulajdonosok létrehozhatják és kezelhetik a számlázási szakaszaikhoz tartozó előfizetéseket.

## <a name="changes-to-billing-administrator-access"></a>Számlázási rendszergazda hozzáférését érintő változások

A hozzáférésüktől függően a nagyvállalati szerződéses regisztráció számlázási rendszergazdái hozzáférést kapnak az új fiók számlázási hatóköreihez. A következő táblázat ismerteti a hozzáférés változásait a beállítás után:

| Meglévő szerepkör | Váltás utáni szerepkör |
| --- | --- |
| **Vállalati rendszergazda (Csak olvasási engedély = Nem)** | **- Számlázásifiók-tulajdonos** </br> A számlázási fiók egészét felügyeli </br> **- Számlázásiprofil-tulajdonos** </br> A számlázási profil egészét felügyeli </br> **- Számlaszakasz tulajdonosa minden számlaszakaszon** </br> A számlaszakaszok tartalmát felügyeli |
| **Vállalati rendszergazda (Csak olvasási engedély = Igen)** | **- Számlázásifiók-olvasó** </br> A számlázási fiók egészének írásvédett megtekintése.</br> **- Számlázásiprofil-olvasó** </br> A számlázási profil egészének írásvédett megtekintése.</br>**- Számlaszakasz olvasója minden számlaszakaszon**</br> A számlaszakaszok tartalmának írásvédett nézete|
| **Részlegszintű rendszergazda (Csak olvasási engedély = Nem)** |**- Az adott részleghez létrehozott számlaszakasz tulajdonosa** </br>A számlaszakasz tartalmát felügyeli|
| **Részlegszintű rendszergazda (Csak olvasási engedély = Igen)**|**- Az adott részleghez létrehozott számlaszakasz olvasója**</br> A számlaszakasz tartalmának írásvédett nézete|
| **Fióktulajdonos** | **- Az adott részleghez létrehozott számlaszakaszhoz tartozó Azure-előfizetés létrehozója** </br>  Azure-előfizetések létrehozása a saját számlaszakaszokhoz|

A Microsoft-ügyfélszerződés elfogadásakor egy Azure Active Directory-bérlő (AD-bérlő) lesz kiválasztva az új számlázási fiókhoz. Ha szervezet még nem rendelkezik bérlőkkel, létrejön egy új bérlő. A bérlő képviseli a szervezetet az Azure Active Directoryban. A szervezet globális bérlői rendszergazdái a bérlővel kezelhetik az alkalmazások és az adatok hozzáférését a szervezetben.

Az új fiókja csak a Microsoft-ügyfélszerződés aláírása során kiválasztott bérlő felhasználóit támogatja. Ha a Nagyvállalati Szerződésében adminisztratív jogosultsággal rendelkező felhasználók részei a bérlőnek, hozzáférést kapnak az új számlázási fiókhoz a beállítás során. Ha nem részei a bérlőnek, csak akkor férhetnek hozzá az új számlázási fiókhoz, ha Ön meghívja őket.

A felhasználók meghívásával vendégként adja őket hozzá a bérlőhöz, így hozzáférést kapnak a számlázási fiókhoz. A felhasználók meghívásához a vendéghozzáférésnek bekapcsolva kell lennie a bérlőn. További információért tekintse meg az [Azure Active Directory vendéghozzáféréseinek kezelését](/microsoftteams/teams-dependencies#control-guest-access-in-azure-active-directory) ismertető részt. Ha a vendéghozzáférés ki van kapcsolva, forduljon a bérlő globális rendszergazdájához, és kérje meg, hogy kapcsolja be. <!-- Todo - How can they find their global administrator -->

## <a name="view-replaced-features"></a>Leváltott szolgáltatások megtekintése

A következő nagyvállalati szerződéses szolgáltatásoknak a Microsoft-ügyfélszerződéshez tartozó számlázási fiók új szolgáltatásai lépnek a helyébe.

### <a name="enterprise-agreement-accounts"></a>Nagyvállalati szerződéses fiókok

A nagyvállalati szerződéses regisztrációban létrehozott fiókok nem támogatottak az új számlázási fiókban. A fiók előfizetései a részlegek megfelelő számlázási szakaszához tartoznak. A fióktulajdonosok az Azure-előfizetések létrehozóivá válnak, és létrehozhatják, valamint kezelhetik a számlaszakaszukhoz tartozó előfizetéseket.

### <a name="notification-contacts"></a>Kapcsolattartók

A kapcsolattartók kapják meg az Azure Nagyvállalati Szerződésre vonatkozó tájékoztató e-maileket. Az új számlázási fiókban nem támogatottak. A számlázási fiókjában lévő számlázási profilokhoz hozzáférő felhasználók kapják meg az Azure-kreditekkel és -számlákkal kapcsolatos e-maileket.

### <a name="spending-quotas"></a>Költségkvóták

A nagyvállalati szerződéses regisztrációban a részlegekhez beállított költségkvótákat a költségkeretek váltják fel az új számlázási fiókban. Minden egyes, a regisztrációban a részlegekhez beállított költségkvótához létrejön egy költségkeret. A költségkeretekről a következő szakasz nyújt további információt: [Oktatóanyag: Azure-költségvetések létrehozása és kezelése](../costs/tutorial-acm-create-budgets.md).

### <a name="cost-centers"></a>Költséghelyek

A Nagyvállalati Szerződés regisztrációjának Azure-előfizetéseinél beállított költséghelyek továbbítódnak az új számlázási fiókba. A részlegek és a nagyvállalati szerződéses fiókok esetében azonban nem támogatottak a költséghelyek.

## <a name="additional-information"></a>További információ

A következő szakaszok további információval szolgálnak a számlázási fiók beállításáról.

### <a name="no-service-downtime"></a>Nincs szolgáltatáskiesés

Az előfizetésében lévő Azure-szolgáltatások megszakítás nélkül futnak tovább. Csak az Azure-előfizetései számlázási kapcsolatát állítjuk át. Meglévő erőforrásait, erőforráscsoportjait vagy felügyeleti csoportjait ez nem érinti.

### <a name="user-access-to-azure-resources"></a>Felhasználói hozzáférés az Azure-erőforrásokhoz

Az Azure-beli szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával beállított Azure-erőforrások hozzáférése érintetlen marad az átváltás során.

### <a name="azure-reservations"></a>Azure Reservations

A Nagyvállalati Szerződésben foglalt összes Azure Reserved VM Instances-példány is át lesz helyezve az új számlázási fiókba. Az átállás alatt az előfizetéseiben érvényesített foglalási kedvezmények nem módosulnak.

### <a name="azure-marketplace-products"></a>Azure Marketplace-termékek

A nagyvállalati szerződéses regisztráció összes Azure Marketplace-terméke az előfizetésekkel együtt lesz áthelyezve. A Marketplace-termékek szolgáltatásokhoz való hozzáférése az átváltás során nem változik.

### <a name="support-plan"></a>Támogatási csomag

A támogatási előnyök nem lesznek áthelyezve az átváltás során. Vásároljon új támogatási csomagot, ha az új számlázási fiókban is igénybe szeretné venni az előnyöket az Azure-előfizetésekhez.

### <a name="past-charges-and-balance"></a>Korábbi díjak és egyenleg

Az átváltás előtti kreditszám és egyenleg megtekinthető a Nagyvállalati Szerződés regisztrációjában az Azure Portalon keresztül. <!--Todo - Add a link for this-->

### <a name="when-should-the-setup-be-completed"></a>Mikor kell végrehajtani a beállítást?

A számlázási fiókja beállítását a nagyvállalati szerződés regisztrációja lejárta előtt hajtsa végre. Ha lejár a regisztrációja, az Azure-előfizetések szolgáltatásai továbbra is futni fognak megszakítás nélkül. A szolgáltatások használatáért azonban használatalapú árat kell fizetnie.

### <a name="changes-to-the-enterprise-agreement-enrollment-after-the-setup"></a>A nagyvállalati szerződés regisztráció beállítás utáni változásai

Az átváltás után a nagyvállalati szerződéses regisztrációhoz létrehozott Azure-előfizetések manuálisan helyezhetők át az új számlázási fiókba. További információért lásd az [Azure-előfizetések számlázási tulajdonjogának beszerzése más felhasználóktól](mca-request-billing-ownership.md) című részt. Az átváltás után megvásárolt Azure Reserved VM Instances-példányok áthelyezéséhez [forduljon az Azure ügyfélszolgálatához](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Az átváltás után is adhat hozzáférést számlázási fiókjához a felhasználóknak. További információért lásd a [számlázási szerepkörök az Azure Portalon való kezeléséről](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal) szóló részt.

### <a name="revert-the-transition"></a>Az átváltás visszafordítása

Az átváltás nem fordítható vissza. Ha Azure-előfizetései számlázását átállítja az új számlázási fiókra, többé nem állíthatja vissza a Nagyvállalati Szerződésére.

### <a name="closing-your-browser-during-setup"></a>Böngésző bezárása a beállítás során

Mielőtt az **átváltás indítására** szolgáló lehetőséget választja, bezárhatja a böngészőt. Az e-mailben kapott hivatkozás használatával visszatérhet a beállításhoz, és megkezdheti az átváltást. Ha bezárja a böngészőt az átváltás elindítása után, az átváltási folyamat továbbra is futni fog. Az átváltás állapotát az átváltás állapotát jelző oldalra visszatérve később is ellenőrizheti. E-mailt fog kapni arról, ha az átváltás befejeződött.

## <a name="complete-the-setup-in-the-azure-portal"></a>A beállítás befejezése az Azure Portalon

A beállítás befejezéséhez hozzá kell férnie az új számlázási fiókhoz és a nagyvállalati szerződés regisztrációjához is. További információért lásd a [számlázási fiók beállításának végrehajtásához szükséges hozzáférést](#access-required-to-complete-the-setup) ismertető részt.

1. Jelentkezzen be az Azure Portalra annak a hivatkozásnak a használatával, amelyet a Microsoft-ügyfélszerződés aláírásakor kapott.

2. Ha nincs a birtokában az e-mail-üzenet, jelentkezzen be a következő hivatkozás használatával. Cserélje le az `<enrollmentNumber>` értéket a megújított Nagyvállalati Szerződés regisztrációs számára.

   `https://portal.azure.com/#blade/Microsoft_Azure_EA/EATransitionToMCA/enrollmentId/<enrollmentNumber>`

3. Válassza az **átváltás indítására** szolgáló lehetőséget a beállítás utolsó lépésében. Miután kiválasztotta az átváltás elindítását:

    ![A beállítási varázslót megjelenítő képernyőkép](./media/mca-setup-account/ea-mca-set-up-wizard.png)

    - A Nagyvállalati Szerződés hierarchiának megfelelő számlázási hierarchia jön létre az új számlázási fiókban. További információért lásd: [A számlázási hierarchia változásainak megismerése](#understand-changes-to-your-billing-hierarchy).
    - A nagyvállalati szerződéses regisztráció rendszergazdái hozzáférést kapnak az új számlázási fiókhoz, így továbbra is kezelhetik a szervezet számlázását.
    - Az Azure-előfizetései számlázása az új fiókon történik. **A váltás folyamata nem lesz hatással az Azure-szolgáltatásaira. Továbbra is problémamentesen fognak futni**.
    - Ha rendelkezik Azure Reserved Virtual Machine Instances-példányokkal, a rendszer áthelyezi őket az új számlázási fiókba ugyanazzal a kedvezménnyel és időtartammal.

4. Az **átváltás állapotát** jelző lapon figyelheti az átváltás állapotát.

   ![Az átváltás állapotát megjelenítő képernyőkép](./media/mca-setup-account/ea-mca-set-up-status.png)

## <a name="validate-billing-account-setup"></a>Beállított számlázási fiók ellenőrzése

 Az alábbi lépésekkel ellenőrizze, hogy az új számlázási fiók megfelelően lett-e beállítva:

### <a name="azure-subscriptions"></a>Azure-előfizetések

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Keressen rá a **Költségkezelés + számlázás** kifejezésre.

   ![Képernyőkép az Azure Portal keresőmezőjéről](./media/mca-setup-account/search-cmb.png)

3. Válassza ki a számlázási fiókot. Az új számlázási fiók **Microsoft-ügyfélszerződés** típusú lesz.

4. A bal oldalon válassza az **Azure-előfizetések** elemet.

   ![Képernyőkép az előfizetések listájáról](./media/mca-setup-account/mca-subscriptions-post-transition.png)

Azok az Azure-előfizetések, amelyek a nagyvállalati szerződéses regisztrációról lettek átváltva az új számlázási fiókra, az Azure-előfizetések lapon jelennek meg. Ha úgy gondolja, hogy valamelyik előfizetés hiányzik, manuálisan váltsa át az előfizetés számlázását az Azure Portalon. További információért lásd az [Azure-előfizetések számlázási tulajdonjogának beszerzése más felhasználóktól](mca-request-billing-ownership.md) című részt.

### <a name="azure-reservations"></a>Azure Reserved Virtual Machine Instances

A Nagyvállalati Szerződés regisztrációjába foglalt Azure Reserved Virtual Machine Instances-példányok is át lesznek helyezve az új számlázási fiókba ugyanazzal a kedvezménnyel és időtartammal. A váltás előtt befejeződött tranzakciók nem fognak megjelenni az új számlázási fiókban. Ha felkeresi az [Azure Reserved Virtual Machine Instances oldalát](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade), ellenőrizheti, hogy a példányok kedvezményeit alkalmazza-e a rendszer az előfizetéseire.

### <a name="access-of-enterprise-administrators-on-the-billing-account"></a>A vállalati rendszergazdák hozzáférése a számlázási fiókban

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Keressen rá a **Költségkezelés + számlázás** kifejezésre.

   ![Képernyőkép az Azure Portal keresőmezőjéről](./media/mca-setup-account/search-cmb.png)

3. Válassza ki a **Microsoft-ügyfélszerződéshez** tartozó számlázási fiókot.

4. A bal oldalon válassza az **Access control (IAM)** lehetőséget.

   ![Képernyőkép a számlázásifiók-tulajdonosokként felsorolt vállalati rendszergazdák hozzáféréséről az átváltás után.](./media/mca-setup-account/mca-ea-admins-ba-access-post-transition.png)

A vállalati rendszergazdák számlázásifiók-tulajdonosokként vannak felsorolva, míg a csak olvasási jogosultsággal rendelkező vállalati rendszergazdák számlázásifiók-olvasókként jelennek meg. Ha úgy gondolja, hogy hiányzik valamelyik vállalati rendszergazda hozzáférése, hozzáférést adhat neki az Azure Portalon. További információért lásd [a számlázási szerepkörök az Azure Portalon történő kezelését](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

### <a name="access-of-enterprise-administrators-on-the-billing-profile"></a>A vállalati rendszergazdák hozzáférése a számlázási profilon

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Keressen rá a **Költségkezelés + számlázás** kifejezésre.

   ![Képernyőkép az Azure Portal keresőmezőjéről](./media/mca-setup-account/search-cmb.png)

3. Válassza ki a regisztrációjához létrehozott számlázási profilt. A hozzáférésétől függően előfordulhat, hogy ki kell választania egy számlázási fiókot. A számlázási fiókon belül válassza ki a Számlázási profilok lehetőséget, majd a számlázási profilt.

4. A bal oldalon válassza az **Access control (IAM)** lehetőséget.

   ![Képernyőkép a számlázásiprofil-tulajdonosokként felsorolt vállalati rendszergazdák hozzáféréséről az átváltás után.](./media/mca-setup-account/mca-ea-admins-bp-access-post-transition.png)

A vállalati rendszergazdák számlázásiprofil-tulajdonosokként vannak felsorolva, míg a csak olvasási jogosultsággal rendelkező vállalati rendszergazdák számlázásiprofil-olvasókként jelennek meg. Ha úgy gondolja, hogy hiányzik valamelyik vállalati rendszergazda hozzáférése, hozzáférést adhat neki az Azure Portalon. További információért lásd [a számlázási szerepkörök az Azure Portalon történő kezelését](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

### <a name="access-of-enterprise-administrators-department-administrators-and-account-owners-on-invoice-sections"></a>A vállalati rendszergazdák, a részlegszintű rendszergazdák és a fióktulajdonosok hozzáférése a számlaszakaszokhoz

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Keressen rá a **Költségkezelés + számlázás** kifejezésre.

   ![Képernyőkép az Azure Portal keresőmezőjéről](./media/mca-setup-account/search-cmb.png).

3. Válasszon ki egy számlázási szakaszt. A számlaszakaszoknak ugyanaz a neve, mint a hozzájuk tartozó nagyvállalati szerződéses regisztrációban lévő részlegeknek. A hozzáférésétől függően előfordulhat, hogy ki kell választania egy számlázási fiókot. A számlázási fiókon belül válassza ki a **Számlázási profilok**, majd a **Számlaszakaszok** lehetőséget. A számlaszakaszok listájából válasszon ki egy számlaszakaszt.

   ![Képernyőkép a számlaszakaszok listájáról az átváltás után](./media/mca-setup-account/mca-invoice-sections-post-transition.png)

4. A bal oldalon válassza az **Access control (IAM)** lehetőséget.

    ![A részlegszintű rendszergazdák és a fiókadminisztrátorok átváltás utáni hozzáférését megjelenítő képernyőkép](./media/mca-setup-account/mca-department-account-admins-access-post-transition.png)

A vállalati rendszergazdák és a részlegszintű rendszergazdák a számlaszakasz tulajdonosaiként vagy a számlaszakasz olvasóiként, míg a részlegben lévő fiókok tulajdonosai az Azure-előfizetések létrehozóiként vannak feltüntetve. Ismételje meg a lépéseket az összes számlaszakasz esetén, hogy leellenőrizze a nagyvállalati szerződéses regisztráció részlegeinek hozzáférését. Azok a fióktulajdonosok, akik egyetlen részlegnek sem voltak részei, az **Alapértelmezett számlaszakasz** nevű számlaszakaszhoz kapnak hozzáférést. Ha úgy gondolja, hogy hiányzik valamelyik rendszergazda hozzáférése, hozzáférést adhat neki az Azure Portalon. További információért lásd [a számlázási szerepkörök az Azure Portalon történő kezelését](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

## <a name="need-help-contact-support"></a>Segítségre van szüksége? Kapcsolatfelvétel a támogatási szolgáltatással

Ha segítségre van szüksége, [vegye fel a kapcsolatot az ügyfélszolgálattal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.

## <a name="next-steps"></a>További lépések

- [Új számlázási fiók – első lépések](../understand/mca-overview.md)

- [Nagyvállalati szerződéses feladatok elvégzése a Microsoft-ügyfélszerződés számlázási fiókjában](mca-enterprise-operations.md)

- [A számlázási fiókjához való hozzáférés kezelése](understand-mca-roles.md)