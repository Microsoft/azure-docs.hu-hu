---
title: Tudnivalók az Azure-előfizetés számlázási tulajdonjogának átadásáról
description: Ez a cikk ismerteti azokat a tudnivalókat, amelyekkel tisztában kell lennie, mielőtt átadja egy Azure-előfizetés számlázási tulajdonjogát egy másik fióknak.
keywords: azure-előfizetés átadása, átadás azure-előfizetés, azure-előfizetés áthelyezése másik fiókba, azure-előfizetés tulajdonosának módosítása, azure-előfizetés átvitele másik fiókba, azure számlázás átadása
author: bandersmsft
ms.reviewer: amberb
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: banders
ms.custom: contperf-fy21q1
ms.openlocfilehash: 746845004a7a1baf1cafb872aae9322177879b6b
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "103011102"
---
# <a name="about-transferring-billing-ownership-for-an-azure-subscription"></a>Tudnivalók az Azure-előfizetés számlázási tulajdonjogának átadásáról

Ez a cikk segít megismerni azokat a tudnivalókat, amelyekkel tisztában kell lennie, mielőtt átadja egy Azure-előfizetés számlázási tulajdonjogát egy másik fióknak. 

Az Azure-előfizetés számlázási tulajdonjogának átadására akkor lehet szükség, ha elhagyja a szervezetet, vagy egy másik fióknak kell küldeni az előfizetésre vonatkozó számlákat. A számlázási tulajdonjog másik fióknak való átadása lehetővé teszi az új fiók rendszergazdái számára a számlázási feladatok végrehajtását. Ilyenek például a fizetési mód megváltoztatása, a díjak megtekintése és az előfizetés lemondása.

Ha meg szeretné tartani a számlázási tulajdonjogot, de módosítani kívánja az előfizetés típusát, tekintse meg [az Azure-előfizetés másik ajánlatra váltását](../manage/switch-azure-offer.md) bemutató cikket. Ha szeretné szabályozni, hogy ki férhet hozzá az előfizetés erőforrásaihoz, tekintse meg [az Azure beépített szerepköreit](../../role-based-access-control/built-in-roles.md).

A Nagyvállalati Szerződéssel (EA) rendelkező ügyfelek esetében a vállalati rendszergazdák adhatják át az előfizetések számlázási tulajdonjogát az egyes fiókok között.

Az előfizetés tulajdonjogát csak egy fiók számlázási rendszergazdája adhatja át.

## <a name="determine-if-you-are-a-billing-administrator"></a>Annak megállapítása, hogy számlázási rendszergazda-e

<a name="whoisaa"></a>

Az átvitel elvégzése érdekében keresse meg azt a személyt, aki hozzáfér a fiók számlázásának kezeléséhez. Ő férhet hozzá a számlákhoz az [Azure Portalon](https://portal.azure.com), és ő végzi el a különböző számlázási feladatokat, mint az előfizetések létrehozása, a számlák áttekintése és kifizetése, valamint a fizetési módok frissítése.

### <a name="check-if-you-have-billing-access"></a>Ellenőrizze, hogy van-e számlázási hozzáférése

1. A számlázási hozzáféréssel rendelkező fiókok azonosításához látogasson el [Azure Portal Cost Management + számlázási oldalára](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview).

2. A bal oldali menüben válassza a **Számlázási fiókok** elemet.

3. A **Számlázási hatókör** listázása lap megjeleníti az összes olyan előfizetést, amelyhez hozzáférése van a számlázási adatokhoz.

### <a name="check-by-subscription"></a>Ellenőrzés előfizetés szerint

1. Ha nem biztos benne, hogy egy adott előfizetésnek ki a fiókadminisztrátora, lépjen [az Azure Portal Előfizetések oldalára](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). 

2. Válassza ki az előfizetést, amelyet szeretne megtekinteni.

3. A **Beállítások** fejléc alatt válassza a **Tulajdonságok** lehetőséget. A **fiók** rendszergazdája mezőben megtudhatja, hogy ki az előfizetés rendszergazdai fiókja.

   > [!NOTE]
   > Nem minden előfizetési típus jeleníti meg a tulajdonságokat.

## <a name="supported-subscription-types"></a>Támogatott előfizetési típusok

Az előfizetések Azure Portalon történő átadása az alábbi előfizetési típusok esetében érhető el. Az átadás lehetősége jelenleg nem támogatott az [ingyenes próba-előfizetések](https://azure.microsoft.com/offers/ms-azr-0044p/) és az [Azure in Open- (AIO-)](https://azure.microsoft.com/offers/ms-azr-0111p/) előfizetések esetében. Áthidaló megoldásért tekintse meg az [erőforrások új erőforráscsoportba vagy előfizetésbe való áthelyezését](../../azure-resource-manager/management/move-resource-group-and-subscription.md) ismertető témakört. Egyéb előfizetések, például támogatási csomagok átviteléhez [forduljon az Azure ügyfélszolgálatához](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

- [Nagyvállalati Szerződés (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)<sup>1</sup>
- [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p/)  
- [Visual Studio Enterprise- (MPN-) előfizetők](https://azure.microsoft.com/offers/ms-azr-0029p/)
- [MSDN-platformok](https://azure.microsoft.com/offers/ms-azr-0062p/)  
- [Használatalapú fizetés](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Pay-As-You-Go Dev/Test](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)
- [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p/)
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)
- [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)
- [Microsoft Azure-csomag](https://azure.microsoft.com/offers/ms-azr-0017g/)<sup>2</sup>

<sup>1</sup>Az EA Portalon.

<sup>2</sup>Csak az Azure webhelyén történő regisztráció során létrehozott fiókok esetében támogatott.

## <a name="resources-transferred-with-subscriptions"></a>Előfizetésekkel együtt átadott erőforrások

Az összes erőforrás, például virtuális gép, lemez és webhely át lesz helyezve az új fiókba. Ugyanakkor ha az előfizetést egy másik Azure AD-bérlőben található fiókba helyezi át, az előfizetés [rendszergazdai szerepkörei](../manage/add-change-subscription-administrator.md) és [Azure-beli szerepkör-hozzárendelései](../../role-based-access-control/role-assignments-portal.md) nem lesznek áthelyezve. Ezenkívül az [alkalmazásregisztrációkat](../../active-directory/develop/quickstart-register-app.md) és más, bérlőspecifikus szolgáltatásokat sem adja át az előfizetéssel.

## <a name="transfer-account-ownership-to-another-countryregion"></a>Fiók tulajdonjogának átvitele másik országba/régióba

Sajnos az előfizetések nem vihetők át másik országba vagy régióba az Azure Portal használatával. Azure-támogatáskérés megnyitásával azonban átvihetők. Támogatási kérés létrehozásához [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="transfer-a-subscription-from-one-account-to-another"></a>Előfizetés átvitele az egyik fiókból a másikba

Ha Ön két fiók adminisztrátora, átadhat előfizetéseket a fiókjai között. A fiókjai technikailag két különálló felhasználó fiókjainak számítanak, így átadhat előfizetéseket a fiókjai között.
Az előfizetések átvitelének lépéseit lásd: [Azure-előfizetés számlázási tulajdonjogának átadása](../manage/billing-subscription-transfer.md).

## <a name="transferring-a-subscription-shouldnt-create-downtime"></a>Az előfizetések átvitele nem jár leállással

Egy előfizetésnek az ugyanazon Azure AD-bérlőn lévő fiókba való átadása nincs hatással az előfizetésen futó erőforrásokra. A PowerShellben mentett környezeti információk azonban nem frissülnek, ezért előfordulhat, hogy ezeket törölnie kell, vagy módosítania kell a beállításokat. Ha az előfizetést egy másik bérlő fiókjába helyezi át, és úgy dönt, hogy áthelyezi az előfizetést a bérlőre, akkor az összes korábbi, [Azure-beli szerepkör-hozzárendeléssel](../../role-based-access-control/role-assignments-portal.md) rendelkező felhasználó, csoport és szolgáltatásnév elveszíti az előfizetésben lévő erőforrások eléréséhez való hozzáférést. Emiatt szolgáltatáskiesés fordulhat elő.

## <a name="new-account-usage-and-billing-history"></a>Új fiók használati és számlázási előzményei

Az új fiók felhasználói számára elérhető egyetlen információ az előfizetése utolsó havi díja. A használati és számlázási előzmények közül más nem kerül át az előfizetéssel együtt.

## <a name="manually-migrate-data-and-services"></a>Adatok és szolgáltatások manuális migrálása

Előfizetés áthelyezésekor a hozzá tartozó erőforrások is át lesznek helyezve. Ha nem tudja átadni az előfizetés tulajdonjogát, manuálisan is elvégezheti a hozzá tartozó erőforrások migrálását. További információ: [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](../../azure-resource-manager/management/move-resource-group-and-subscription.md).

## <a name="remaining-subscription-credits"></a>Előfizetés fennmaradó kreditjei 

Ha Visual Studio- vagy Microsoft Partner Network-előfizetéssel rendelkezik, havi krediteket kap. Ezek a kreditek nem kerülnek át előfizetéssel együtt az új fiókba. Az átadási kérelmet elfogadó felhasználónak rendelkeznie kell Visual Studio-licenccel a kérelem elfogadásához. Az előfizetés a felhasználó fiókjában rendelkezésre álló Visual Studio-krediteket használja. További információ: [A Visual Studio- és a Microsoft Partner Network-előfizetések átadása](../manage/billing-subscription-transfer.md#transfer-visual-studio-and-partner-network-subscriptions).

## <a name="users-keep-access-to-transferred-resources"></a>A felhasználók továbbra is hozzáférhetnek az áthelyezett erőforrásokhoz

Ne feledje, hogy ha egy felhasználó hozzáféréssel rendelkezik az előfizetésben lévő erőforrásokhoz, akkor a hozzáférés a tulajdonjog átadását követően is megmarad. Azonban előfordulhat, hogy a [rendszergazdai szerepkörök](../manage/add-change-subscription-administrator.md) és az [Azure-beli szerepkör-hozzárendelések](../../role-based-access-control/role-assignments-portal.md) el lesznek távolítva. A hozzáférés akkor veszik el, ha a fiók az előfizetés bérlőjétől eltérő Azure AD-bérlőn található, és az átadási kérelmet küldő felhasználó áthelyezi az előfizetést az Ön fiókjának bérlőjére. 

Az Azure Portalon megtekintheti, hogy mely felhasználók rendelkeznek olyan Azure-beli szerepkör-hozzárendeléssel, amely hozzáférést biztosít az előfizetés erőforrásaihoz. Nyissa meg [az Azure Portal Előfizetés lapját](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Válassza ki a megtekinteni kívánt előfizetést, majd a bal oldali panelen válassza a **Hozzáférés-vezérlés (IAM)** elemet. Ezt követően válassza a lap tetején található **Szerepkör-hozzárendelések** elemet. A Szerepkör-hozzárendelések oldal felsorolja az összes felhasználót, aki hozzáféréssel rendelkezik az előfizetéshez.

Az [Azure-beli szerepkör-hozzárendeléseknek](../../role-based-access-control/role-assignments-portal.md) az átadás során történő eltávolítása esetén is előfordulhat, hogy az eredeti tulajdonosi fiók felhasználói továbbra is hozzáféréssel rendelkeznek az előfizetéshez más biztonsági mechanizmusokon keresztül, például:

* Felügyeleti tanúsítványok, amelyek rendszergazdai jogosultságokat biztosítanak a felhasználónak az előfizetés erőforrásaihoz. További információért lásd: [Felügyeleti tanúsítvány létrehozása és feltöltése az Azure szolgáltatáshoz](../../cloud-services/cloud-services-certs-create.md).
* A Storage-hoz hasonló szolgáltatások hozzáférési kulcsai. További információkat az [Azure Storage-fiókokkal kapcsolatos tudnivalókat](../../storage/common/storage-account-create.md) ismertető témakörben olvashat.
* Az olyan szolgáltatások távelérési hitelesítő adatai, mint az Azure-beli virtuális gépek.

Ha a címzettnek korlátoznia kell a hozzáférését az erőforrásokhoz, érdemes megfontolnia a szolgáltatáshoz társított titkos kódok frissítését. A legtöbb erőforrás frissíthető. Jelentkezzen be az [Azure Portalra](https://portal.azure.com), majd a központi menüben válassza a **Minden erőforrás** elemet. Ezután válassza ki az erőforrást. Az erőforrás oldalán válassza a **Beállítások** elemet. Itt tekintheti meg és frissítheti a meglévő titkos kódokat.

## <a name="you-pay-for-usage-when-you-receive-ownership"></a>A tulajdonjog átvétele után fizetni kell a használatért

Az Ön fiókja felelős az átadás időpontjától kezdve jelentkező használati díjak megfizetéséért. Előfordulhat, hogy bizonyos használatra az átadás előtt került sor, de csak az átadást követően lett jelentve. Ez is az Ön fiókjának számlájában fog szerepelni.

## <a name="use-a-different-payment-method"></a>Másik fizetési mód használata

Az átadási kérelem elfogadásakor kiválaszthatja a fiókja meglévő fizetési módját, vagy hozzáadhat egy új fizetési módot.

## <a name="transfer-enterprise-agreement-subscription-ownership"></a>Nagyvállalati szerződéses előfizetések tulajdonjogának átadása

A vállalati rendszergazda bármilyen fiók tulajdonjogát frissítheti, még azután is, hogy az eredeti fióktulajdonos már kilépett a szervezetből. További információ az Azure nagyvállalati szerződéses fiókok átadásáról: [Azure Enterprise-átvitelek](../manage/ea-transfers.md).

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Vegye fel velünk a kapcsolatot.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések

- [Azure-előfizetés számlázási tulajdonjogának átadása](../manage/billing-subscription-transfer.md)
