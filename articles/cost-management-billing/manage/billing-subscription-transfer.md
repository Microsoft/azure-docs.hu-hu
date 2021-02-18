---
title: Azure-előfizetés számlázási tulajdonjogának átadása
description: Ismerteti, hogyan lehet átadni egy Azure-előfizetés számlázási tulajdonjogát egy másik fióknak.
keywords: azure-előfizetés átadása, átadás azure-előfizetés, azure-előfizetés áthelyezése másik fiókba, azure-előfizetés tulajdonosának módosítása, azure-előfizetés átvitele másik fiókba, azure számlázás átadása
author: bandersmsft
ms.reviewer: amberb
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 02/05/2021
ms.author: banders
ms.custom: contperf-fy21q1
ms.openlocfilehash: c3142fd41830487453a3cc980a87cdca72cf7213
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "101094132"
---
# <a name="transfer-billing-ownership-of-an-azure-subscription-to-another-account"></a>Azure-előfizetés számlázási tulajdonjogának átadása másik fióknak

Ez a cikk lépésenként ismerteti, hogyan lehet átadni egy Azure-előfizetés számlázási tulajdonjogát egy másik fióknak. Az előfizetés számlázási tulajdonjogának átadása előtt olvassa el a [Tudnivalók az Azure-előfizetés számlázási tulajdonjogának átadásáról](../understand/subscription-transfer.md) cikket.

Ha meg szeretné tartani a számlázási tulajdonjogot, de módosítani kívánja az előfizetés típusát, tekintse meg [az Azure-előfizetés másik ajánlatra váltását](switch-azure-offer.md) bemutató cikket. Ha szeretné szabályozni, hogy ki férhet hozzá az előfizetés erőforrásaihoz, tekintse meg [az Azure beépített szerepköreit](../../role-based-access-control/built-in-roles.md).

A Nagyvállalati Szerződéssel (EA) rendelkező ügyfelek esetében a vállalati rendszergazda adhatja át az előfizetések számlázási tulajdonjogát az egyes fiókok között. További információ: [Az Azure-előfizetés vagy a fiók tulajdonjogának módosítása](ea-portal-administration.md#change-azure-subscription-or-account-ownership).

Az előfizetés tulajdonjogát csak egy fiók számlázási rendszergazdája adhatja át.

## <a name="transfer-billing-ownership-of-an-azure-subscription"></a>Azure-előfizetés számlázási tulajdonjogának átadása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az átadni kívánt előfizetést tartalmazó számlázási fiók rendszergazdájaként. Ha nem biztos abban, hogy Ön a rendszergazda, vagy meg szeretné állapítani, hogy ki a rendszergazda, tekintse meg a [Fiók számlázási rendszergazdájának megállapítása](../understand/subscription-transfer.md#whoisaa) cikket.
1. Keressen rá a **Költségkezelés + számlázás** kifejezésre.  
   ![Képernyőkép az Azure Portal keresőmezőjéről](./media/billing-subscription-transfer/billing-search-cost-management-billing.png)
1. A bal oldali panelen válassza az **Előfizetések** elemet. A hozzáférésétől függően előfordulhat, hogy ki kell választania egy számlázási hatókört, majd az **Előfizetések** vagy az **Azure-előfizetések** lehetőséget.
1. Az átadni kívánt előfizetésnél válassza a **Számlázási tulajdonjog átadása** lehetőséget.  
   ![Válassza ki az átadni kívánt előfizetést.](./media/billing-subscription-transfer/billing-select-subscription-to-transfer.png)
1. Adja meg egy olyan felhasználó e-mail-címét, aki annak a fióknak a számlázási adminisztrátora, amely az előfizetés új tulajdonosa lesz.
1. Ha az előfizetését egy másik Azure AD-bérlőn lévő fiókba helyezi át, válassza ki, hogy át kívánja-e helyezni az előfizetést az új fiók bérlőjére. További információ: [Előfizetés átadása egy másik Azure AD-bérlőn található fióknak](#transfer-a-subscription-to-another-azure-ad-tenant-account).
    > [!IMPORTANT]
    > Ha úgy dönt, hogy áthelyezi az előfizetést az új fiók Azure AD-bérlőjére, azzal véglegesen eltávolítja az előfizetés erőforrásainak eléréséhez szükséges összes [Azure-beli szerepkör-hozzárendelést](../../role-based-access-control/role-assignments-portal.md). Csak az új fiók átadási kérelmet elfogadó felhasználójának lesz hozzáférése az előfizetés erőforrásainak kezeléséhez. Másik lehetőségként törölheti az **Előfizetés Azure AD-bérlője** jelölőnégyzet jelölését, így anélkül adhatja át a számlázási tulajdonjogot az új fiók bérlőjének, hogy áthelyezné az előfizetést. Ha ezt a módszert választja, megtarthatja az Azure-erőforrások eléréséhez szükséges meglévő Azure-beli szerepkör-hozzárendeléseket.  
    ![Átadási kérelem küldése lap](./media/billing-subscription-transfer/billing-send-transfer-request.png)
1. Válassza az **Átadási kérelem küldése** lehetőséget.
1. A felhasználó egy e-mailt kap, amely felszólítja az átadási kérelem áttekintéséra.  
   ![Az előfizetés átadásáról szóló e-mail, amely el lett küldve a címzettnek](./media/billing-subscription-transfer/billing-receiver-email.png)
1. Az átadási kérelem jóváhagyásához a felhasználó kiválasztja az e-mailben szereplő hivatkozást, és követi az útmutatást. A felhasználó ezután kiválasztja az előfizetés díjának fizetésére használt fizetési módot. Ha a felhasználó még nem rendelkezik Azure-fiókkal, regisztrálnia kell egyet.  
   ![Előfizetés-átadás: első weblap](./media/billing-subscription-transfer/billing-accept-ownership-step1.png)
   ![Előfizetés-átadás: második weblap](./media/billing-subscription-transfer/billing-accept-ownership-step2.png)
   ![Előfizetés-átadás: harmadik weblap](./media/billing-subscription-transfer/billing-accept-ownership-step3.png)
1. Sikerült! Az előfizetés átadása ezzel megtörtént.

## <a name="transfer-a-subscription-to-another-azure-ad-tenant-account"></a>Előfizetés átadása másik Azure AD-bérlői fiók számára

Amikor regisztrál az Azure-ba, a rendszer létrehoz Önnek egy Azure Active Directory- (AD)-bérlőt. A bérlő képviseli az Ön fiókját. A bérlő segítségével szabályozhatja az előfizetéseihez és az erőforrásaihoz való hozzáférést.

Amikor új előfizetést hoz létre, az a fiókja Azure AD-bérlőjén lesz tárolva. Ha másoknak kíván hozzáférést biztosítani az előfizetéshez vagy annak erőforrásaihoz, meg kell őket hívnia, hogy csatlakozzanak a bérlőjéhez. Így szabályozhatja az előfizetésekhez és az erőforrásokhoz való hozzáférést.

Amikor az előfizetése számlázási tulajdonjogát egy másik Azure AD-bérlőn lévő fióknak adja át, az előfizetést is áthelyezheti az új fiók bérlőjére. Ha így tesz, akkor az összes korábbi [Azure-beli szerepkör-hozzárendeléssel](../../role-based-access-control/role-assignments-portal.md) rendelkező felhasználó, csoport és szolgáltatásnév elveszíti az előfizetés és az előfizetés erőforrásainak kezeléséhez szükséges hozzáférést. Csak az új fiók átadást elfogadó felhasználójának lesz hozzáférése az erőforrások kezeléséhez. Az új tulajdonosnak manuálisan kell hozzáadnia ezeket a felhasználókat az előfizetéshez, ha hozzáférést kíván biztosítani az azt elveszítő felhasználónak. További információ: [Azure-előfizetés átadása egy másik Azure AD-címtárba](../../role-based-access-control/transfer-subscription.md).

## <a name="transfer-visual-studio-and-partner-network-subscriptions"></a>Visual Studio- és a Microsoft Partner Network-előfizetések átadása

A Visual Studio- és a Microsoft Partner Network-előfizetésekhez havonta ismétlődő Azure-kredit tartozik. Az előfizetések átadásakor ez a kredit nem válik elérhetővé a cél számlázási fiókban. Az előfizetés a cél számlázási fiókban rendelkezésre álló kreditet használja fel. Például: Bob átad egy Visual Studio Enterprise-előfizetést Jane fiókjába szeptember 9-én, és Jane elfogadja a tranzakciót. Az átadás befejezését követően az előfizetés Jane fiókjának kreditjét kezdi használni. A kreditek mennyisége minden hónap 9. napján visszaáll az alapértékre.

## <a name="next-steps-after-accepting-billing-ownership"></a>A számlázási tulajdonjog elfogadását követő lépések

Ha elfogadta egy Azure-előfizetés számlázási tulajdonjogát, javasoljuk, hogy tekintse át a következő lépéseket:

1. Tekintse át és frissítse a szolgáltatásadminisztrátori, a társadminisztrátori és az Azure-beli szerepkör-hozzárendeléseket. További információ: Azure- [előfizetések rendszergazdáinak hozzáadása vagy módosítása](add-change-subscription-administrator.md) és [Azure-szerepkörök kiosztása az Azure Portal használatával](../../role-based-access-control/role-assignments-portal.md).
1. Frissítse az előfizetés szolgáltatásaihoz tartozó hitelesítő adatokat, például:
   1. Felügyeleti tanúsítványok, amelyek rendszergazdai jogosultságokat biztosítanak a felhasználónak az előfizetés erőforrásaihoz. További információk: [Felügyeleti tanúsítvány létrehozása és feltöltése az Azure szolgáltatáshoz](../../cloud-services/cloud-services-certs-create.md)
   1. A Storage-hoz hasonló szolgáltatások hozzáférési kulcsai. További információkat az [Azure Storage-fiókokkal kapcsolatos tudnivalókat](../../storage/common/storage-account-create.md) ismertető témakörben olvashat.
   1. Az olyan szolgáltatások távelérési hitelesítő adatai, mint az Azure-beli virtuális gépek.
1. Ha egy partnerrel közösen dolgozik, fontolja meg a partnerazonosító frissítését az előfizetésen. A partnerazonosítót az [Azure Portalon](https://portal.azure.com) frissítheti. További információért lásd a [partnerazonosítók az Azure-fiókokhoz csatolását](link-partner-id.md) ismertető cikket.

## <a name="cancel-a-transfer-request"></a>Átadási kérelem megszakítása

Egyszerre csak egy átadási kérelem aktív. Az átadási kérelem 15 napig érvényes. 15 nap után az átadási kérelem lejár.

Átadási kérelem törlése:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Navigáljon az **Előfizetések** > válassza ki azt az előfizetést, amelyhez átadási kérelmet küld, majd válassza az **átvitel számlázási tulajdonos** lehetőséget.
1. Az oldal alján válassza az **Átadási kérelem törlése** elemet.

:::image type="content" source="./media/billing-subscription-transfer/transfer-billing-owership-cancel-request.png" alt-text="A Számlázási tulajdonjog átadása ablakot bemutató példa az Átadási kérelem törlése lehetőséggel" lightbox="./media/billing-subscription-transfer/transfer-billing-owership-cancel-request.png" :::

## <a name="troubleshooting"></a>Hibaelhárítás

Ha problémába ütközik az előfizetések átadása során, az alábbi hibaelhárítási információk segítséget nyújthatnak.

### <a name="original-azure-subscription-billing-owner-leaves-your-organization"></a>Az eredeti Azure-előfizetés számlázási tulajdonosa elhagyja a szervezetét

> [!Note]
> Ez a szakasz kifejezetten egy Microsoft-ügyfél szerződés számlázási fiókjára vonatkozik. Ellenőrizze, hogy van-e hozzáférése [Microsoft-ügyfél szerződéshez](mca-request-billing-ownership.md#check-for-access).

Lehetséges, hogy az eredeti számlázási fiók tulajdonosa, aki létrehozta az Azure-fiókot és az Azure-előfizetést, elhagyja a szervezetét. Ha ez a helyzet történik, akkor a felhasználói identitásuk már nem szerepel a szervezet Azure Active Directoryján. Az Azure-előfizetéshez nem tartozik számlázási tulajdonos. Ez a helyzet megakadályozza, hogy bárki elvégezzen számlázási műveleteket a fiókban, beleértve a megtekintést és a számlák kifizetését. Az előfizetés korábban esedékes állapotba kerülhet. Végül az előfizetés nem fizetés miatt letiltható. Végső soron az előfizetés törölhető, és az előfizetésen futó összes szolgáltatásra hatással lenne.

Ha egy előfizetés már nem rendelkezik érvényes számlázási fiók tulajdonosával, az Azure e-mailt küld a számlázási fiókok más tulajdonosainak, a szolgáltatás rendszergazdáinak (ha vannak ilyenek), Co-Administrators (ha van), és az előfizetés tulajdonosai tájékoztatják őket a helyzetről, és egy hivatkozást biztosítanak az előfizetés számlázási tulajdonjogának elfogadására. Bármelyik felhasználó kiválaszthatja a számlázási tulajdonjog elfogadására szolgáló hivatkozást. A számlázási szerepkörökkel kapcsolatos további információkért tekintse meg a [Számlázási szerepkörök](understand-mca-roles.md) és a [klasszikus szerepkörök és az Azure RBAC szerepkörei](../../role-based-access-control/rbac-and-directory-admin-roles.md)című témakört.

Íme egy példa arra, hogy az e-mail hogyan néz ki.

:::image type="content" source="./media/billing-subscription-transfer/orphaned-subscription-email.png" alt-text="Képernyőfelvétel: a számlázási tulajdonjog elfogadására szolgáló e-mail-cím." lightbox="./media/billing-subscription-transfer/orphaned-subscription-email.png" :::

Emellett az Azure egy szalagcímet jelenít meg az előfizetés részletek ablakában a Azure Portal a számlázási tulajdonosok, a szolgáltatás-rendszergazdák, a társ-rendszergazdák és az előfizetések tulajdonosai számára. A számlázási tulajdonjog elfogadásához kattintson a szalagcímben található hivatkozásra.

:::image type="content" source="./media/billing-subscription-transfer/orphaned-subscription-example.png" alt-text="Képernyőkép: egy érvényes számlázási tulajdonos nélküli előfizetésre mutató példa." lightbox="./media/billing-subscription-transfer/orphaned-subscription-example.png" :::

### <a name="the-transfer-subscription-option-is-unavailable"></a>„Az előfizetés átadása” lehetőség nem érhető el

<a name="no-button"></a> 

Az önkiszolgáló előfizetés-átadási funkció nem érhető el a számlázási fiókjában. Jelenleg a nagyvállalati szerződéses (EA-) fiókok esetében nem támogatott az előfizetések számlázási tulajdonjogának átadása az Azure Portalon. Ezenkívül a Microsoft-képviselővel közösen létrehozott Microsoft-ügyfélszerződési fiókok nem támogatják a számlázási tulajdonjog átadását.

###  <a name="not-all-subscription-types-can-transfer"></a>Nem minden előfizetés-típus adható át

Nem minden előfizetés-típus támogatja a számlázási tulajdonjog átadását. Az átadást támogató előfizetési típusok teljes listáját a [támogatott előfizetési típusokat](../understand/subscription-transfer.md#supported-subscription-types) felsoroló témakörben tekintheti át.

###  <a name="access-denied-error-shown-when-trying-to-transfer-subscription-billing-ownership"></a>Hozzáférés megtagadva hibaüzenet jelenik meg az előfizetés számlázási tulajdonjogának átadásakor

Akkor kapja ezt a hibaüzenetet, ha úgy próbálja átadni egy Microsoft Azure-csomag előfizetését, hogy nem rendelkezik a szükséges engedéllyel. A Microsoft Azure-csomag előfizetésének átadásához tulajdonosnak vagy közreműködőnek kell lennie abban a számlázási szakaszban, ahol az előfizetés számlái megjelennek. További információk: [A számlázási szakaszokhoz tartozó előfizetések kezelése](../manage/understand-mca-roles.md#manage-subscriptions-for-invoice-section).

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Vegye fel velünk a kapcsolatot.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések

- Tekintse át és frissítse a szolgáltatásadminisztrátori, a társadminisztrátori és az Azure-beli szerepkör-hozzárendeléseket. További információ: Azure- [előfizetések rendszergazdáinak hozzáadása vagy módosítása](add-change-subscription-administrator.md) és [Azure-szerepkörök kiosztása az Azure Portal használatával](../../role-based-access-control/role-assignments-portal.md).