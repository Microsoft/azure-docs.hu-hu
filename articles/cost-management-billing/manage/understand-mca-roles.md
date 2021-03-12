---
title: A Microsoft-ügyfélszerződéshez tartozó számlázási szerepkörök – Azure
description: Megismerheti a Microsoft-ügyfélszerződéshez tartozó számlázási szerepköröket az Azure-beli számlázási fiókok esetében.
author: amberbhargava
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 03/10/2021
ms.author: banders
ms.openlocfilehash: e334a423fd11aa3a357d52099a792dcc905aedeb
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "103011663"
---
# <a name="understand-microsoft-customer-agreement-administrative-roles-in-azure"></a>A Microsoft-ügyfélszerződéshez tartozó felügyeleti szerepkörök ismertetése az Azure-ban

A Microsoft-ügyfélszerződéshez tartozó számlázási fiók kezeléséhez az alábbi szakaszokban ismertetett szerepköröket használhatja. Az Azure beépített szerepkörei mellett ezekkel a szerepkörökkel szabályozhatja az erőforrásokhoz történő hozzáférést. További információ: [Beépített Azure-szerepkörök](../../role-based-access-control/built-in-roles.md).

Ez a cikk a Microsoft-ügyfélszerződéshez tartozó számlázási fiókokra vonatkozik. [Ellenőrizze, hogy rendelkezik-e hozzáféréssel egy Microsoft-ügyfélszerződéshez](#check-access-to-a-microsoft-customer-agreement).

Tekintse meg az [MCA számlázási fiókjához való hozzáférés kezelése](https://www.youtube.com/watch?v=9sqglBlKkho) című videót, amelyből megtudhatja, hogyan szabályozhatja a Microsoft Customer Agreement (MCA) számlázási fiókjához való hozzáférést.

>[!VIDEO https://www.youtube.com/embed/9sqglBlKkho]

## <a name="billing-role-definitions"></a>A számlázási szerepkörök definíciói

Az alábbi táblázat a számlázási fiók, a számlázás profilok és a számlaszakaszok kezeléséhez használható számlázási szerepköröket ismerteti.

|Szerepkör|Leírás|
|---|---|
|Számlázásifiók-tulajdonos|A teljes számlázási fiókot kezeli.|
|Számlázási fiók közreműködője|A teljes számlázási fiókot kezeli, kivéve az engedélyeket.|
|Számlázásifiók-olvasó|A számlázási fiók egészének írásvédett megtekintése.|
|Számlázásiprofil-tulajdonos|A teljes számlázási profilt kezeli.|
|Számlázási profil közreműködője|A teljes számlázási profilt kezeli, kivéve az engedélyeket.|
|Számlázásiprofil-olvasó|A számlázási profil egészének írásvédett megtekintése.|
|Számlakezelő|Megtekinti és kifizeti a számlázási profilhoz tartozó számlákat.|
|Számlázási szakasz tulajdonosa|A számlázási szakasz tartalmát kezeli.|
|Számlázási szakasz közreműködője|A számlázási szakasz tartalmát kezeli az engedélyeket kivéve.|
|Számlázási szakasz olvasója|A számlaszakasz tartalmának írásvédett nézete|
|Azure-előfizetés létrehozója|Létrehozhat Azure-előfizetéseket.|

## <a name="billing-account-roles-and-tasks"></a>A számlázási fiók szerepkörei és azok feladatai

A számlázási fiók az Azure-ba való regisztráció során jön létre. A számlázási fiók használatával kezelheti a számlákat és a kifizetéseket, valamint nyomon követheti a költségeket. A Számlázási fiók szerepkörei rendelkeznek a legmagasabb szintű engedélyekkel, és a szerepkörök felhasználói betekintést kapnak a teljes fiókra vonatkozó díjszabási és számlázási adatokba. Ezeket a szerepköröket csak azokhoz a felhasználókhoz rendelje, akiknek meg kell tekinteniük a számlákat, és követniük kell a teljes fiók költségeit, például a Pénzügy és a könyvelési csapatok tagjait. További információkért tekintse meg [a számlázási fiók bemutatását](../understand/mca-overview.md#your-billing-account).

Az alábbi táblázat azt mutatja, hogy melyik szerepkörre van szüksége bizonyos feladatok elvégzéséhez a számlázási fiókra vonatkozóan.

### <a name="manage-billing-account-permissions-and-properties"></a>A számlázási fiók engedélyeinek és tulajdonságainak kezelése

|Tevékenység|Számlázásifiók-tulajdonos|Számlázási fiók közreműködője|Számlázásifiók-olvasó|
|---|---|---|---|
|Számlázási fiók szerepkör-hozzárendeléseinek megtekintése|✔|✔|✔|
|Engedély megadása másoknak a számlázási fiók megtekintésére és kezelésére|✔|✘|✘|
|Számlázási fiók tulajdonságainak (például cím, szerződések stb.) megtekintése|✔|✔|✔|
|Számlázási fiók tulajdonságainak frissítése, például az eladott, a megjelenítendő név és egyebek|✔|✔|✘|

### <a name="manage-billing-profiles-for-billing-account"></a>A számlázási fiókhoz tartozó számlázási profilok kezelése

|Tevékenység|Számlázásifiók-tulajdonos|Számlázási fiók közreműködője|Számlázásifiók-olvasó|
|---|---|---|---|
|A fiókhoz tartozó összes számlázási Profil megtekintése|✔|✔|✔|
|Új számlázási profilok létrehozása|✔|✔|✘|

### <a name="manage-invoices-for-billing-account"></a>A számlázási fiókhoz tartozó számlák kezelése

|Tevékenység|Számlázásifiók-tulajdonos|Számlázási fiók közreműködője|Számlázásifiók-olvasó|
|---|---|---|---|
|A fiókhoz tartozó összes számla megtekintése|✔|✔|✔|
|Számlák fizetése bankkártyával|✔|✔|✘|
|Számlák letöltése, Azure-használati fájlok, árlista és adóügyi dokumentumok|✔|✔|✔|

### <a name="manage-products-for-billing-account"></a>Számlázási fiókhoz tartozó termékek kezelése

|Tevékenység|Számlázásifiók-tulajdonos|Számlázási fiók közreműködője|Számlázásifiók-olvasó|
|---|---|---|---|
|A fiókhoz megvásárolt összes termék megtekintése|✔|✔|✔|
|Olyan termékek számlázásának kezelése, mint például a Mégse, az automatikus megújítás kikapcsolása és egyebek|✔|✔|✘|

### <a name="manage-subscriptions-for-billing-account"></a>A számlázási fiókhoz tartozó előfizetések kezelése

|Tevékenység|Számlázásifiók-tulajdonos|Számlázási fiók közreműködője|Számlázásifiók-olvasó|
|---|---|---|---|
|A számlázási fiókhoz létrehozott összes Azure-előfizetés megtekintése|✔|✔|✔|
|Új Azure-előfizetések létrehozása|✔|✔|✘|
|Azure-előfizetések megszakítása|✘|✘|✘|

## <a name="billing-profile-roles-and-tasks"></a>A számlázási profil szerepkörei és azok feladatai

Minden számlázási fiókhoz legalább egy számlázási profil tartozik. Az első számlázási profil beállítása az Azure használatára való regisztráláskor történik. A számlázási profilhoz havi számla jön létre, amely az előző hónap összes kapcsolódó díját tartalmazza. Az igények alapján több számlázási profilt is beállíthat. A számlázási profil szerepköreivel rendelkező felhasználók megtekinthetik a költségeket, megadhatják a költségvetést, és kezelhetik és fizethetik a számláit. Rendelje hozzá ezeket a szerepköröket azokhoz a felhasználókhoz, akik felelősek a költségvetés kezeléséért, valamint a számlázási profilért, például a szervezete üzleti felügyeleti csapatának tagjaihoz tartozó számlák kifizetésével. További információkért tekintse meg [a számlázási profilok bemutatását](../understand/mca-overview.md#billing-profiles).

Az alábbi táblázat azt mutatja, hogy melyik szerepkörre van szüksége bizonyos feladatok elvégzéséhez a számlázási profilra vonatkozóan.

### <a name="manage-billing-profile-permissions-properties-and-policies"></a>A számlázási profil engedélyeinek, tulajdonságainak és szabályzatainak kezelése

|Tevékenység|Számlázásiprofil-tulajdonos|Számlázási profil közreműködője|Számlázásiprofil-olvasó|Számlakezelő|Számlázásifiók-tulajdonos|Számlázási fiók közreműködője|Számlázásifiók-olvasó
|---|---|---|---|---|---|---|---|
|A számlázási profilhoz tartozó szerepkör-hozzárendelések megtekintése|✔|✔|✔|✔|✔|✔|✔|
|Engedély megadása másoknak a számlázási profil megtekintésére és kezelésére|✔|✘|✘|✘|✔|✘|✘|
|Megtekintheti a számlázási profil tulajdonságait, például a PO-számot, a számlázást és egyebeket.|✔|✔|✔|✔|✔|✔|✔|
|A számlázási profil tulajdonságainak frissítése |✔|✔|✘|✘|✔|✔|✘|
|Megtekintheti a számlázási profilon alkalmazott házirendeket, például az Azure foglalási vásárlásokat, az Azure Marketplace-beli vásárlásokat és egyebeket|✔|✔|✔|✔|✔|✔|✔|
|Szabályzatok alkalmazása a számlázási profilban |✔|✔|✘|✘|✔|✔|✘|

### <a name="manage-invoices-for-billing-profile"></a>A számlázási profilhoz tartozó számlák kezelése

|Tevékenység|Számlázásiprofil-tulajdonos|Számlázási profil közreműködője|Számlázásiprofil-olvasó|Számlakezelő|Számlázásifiók-tulajdonos|Számlázási fiók közreműködője|Számlázásifiók-olvasó
|---|---|---|---|---|---|---|---|
|A számlázási profilhoz tartozó összes számla megtekintése|✔|✔|✔|✔|✔|✔|✔|
|Számlák fizetése bankkártyával|✔|✔|✘|✔|✔|✘|✘|
|A számlázási profilhoz tartozó számlák, Azure-beli használati adatok és díjak fájljai, árlisták és adózási dokumentumok letöltése|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-invoice-sections-for-billing-profile"></a>A számlázási profil számlázási szakaszainak kezelése

|Tevékenység|Számlázásiprofil-tulajdonos|Számlázási profil közreműködője|Számlázásiprofil-olvasó|Számlakezelő|Számlázásifiók-tulajdonos|Számlázási fiók közreműködője|Számlázásifiók-olvasó
|---|---|---|---|---|---|---|---|
|A számlázási profilhoz tartozó összes számlázási szakasz megtekintése|✔|✔|✔|✔|✔|✔|✔|
|Új számlázási szakasz létrehozása a számlázási profilban|✔|✔|✘|✘|✔|✔|✘|

### <a name="manage-products-for-billing-profile"></a>A számlázási profilhoz tartozó termékek kezelése

|Tevékenység|Számlázásiprofil-tulajdonos|Számlázási profil közreműködője|Számlázásiprofil-olvasó|Számlakezelő|Számlázásifiók-tulajdonos|Számlázási fiók közreműködője|Számlázásifiók-olvasó
|---|---|---|---|---|---|---|---|
|A számlázási profilhoz megvásárolt összes termék megtekintése|✔|✔|✔|✔|✔|✔|✔|
|Olyan termékek számlázásának kezelése, mint például a Mégse, az automatikus megújítás kikapcsolása és egyebek|✔|✔|✘|✘|✔|✔|✘|
|A termékek számlázási profiljának módosítása|✔|✔|✘|✘|✔|✔|✘|

### <a name="manage-payment-methods-for-billing-profile"></a>A számlázási profilhoz tartozó fizetési módok kezelése

|Tevékenység|Számlázásiprofil-tulajdonos|Számlázási profil közreműködője|Számlázásiprofil-olvasó|Számlakezelő|Számlázásifiók-tulajdonos|Számlázási fiók közreműködője|Számlázásifiók-olvasó
|---|---|---|---|---|---|---|---|
|A számlázási profilhoz tartozó fizetési módok megtekintése|✔|✔|✔|✔|✔|✔|✔|
|Olyan fizetési módszerek kezelése, mint például a hitelkártya cseréje, a bankkártya leválasztása és egyebek|✔|✔|✘|✘|✔|✔|✘|
|A számlázási profilhoz tartozó Azure-kreditek egyenlegének nyomon követése|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-subscriptions-for-billing-profile"></a>A számlázási profilhoz tartozó előfizetések kezelése

|Tevékenység|Számlázásiprofil-tulajdonos|Számlázási profil közreműködője|Számlázásiprofil-olvasó|Számlakezelő|Számlázásifiók-tulajdonos|Számlázási fiók közreműködője|Számlázásifiók-olvasó
|---|---|---|---|---|---|---|---|
|A számlázási profilhoz tartozó összes Azure-előfizetés megtekintése|✔|✔|✔|✔|✔|✔|✔|
|Új Azure-előfizetések létrehozása|✔|✔|✘|✘|✔|✔|✘|
|Azure-előfizetések megszakítása|✘|✘|✘|✘|✘|✘|✘|
|Az Azure-előfizetések számlázási profiljának módosítása|✔|✔|✘|✘|✔|✔|✘|

## <a name="invoice-section-roles-and-tasks"></a>A számlázási szakaszok szerepkörei és azok feladatai

Alapértelmezés szerint minden számlázási profil egy számla szakaszt tartalmaz. További számlázási szakaszt is létrehozhat a számlázási profil számláján lévő díjak csoportosításához.  A számla szakasz szerepköreivel rendelkező felhasználók vezérelhetik, hogy ki hoz létre Azure-előfizetéseket, és hogy más vásárlásokat is. Ezeket a szerepköröket hozzárendelheti azokhoz a felhasználókhoz, akik Azure-környezeteket hoznak létre a szervezet csapatai számára, például a mérnöki vezetőkhöz és a rendszertervezőkhöz. További információért lásd a [számlaszakaszokat ismertető részt](../understand/mca-overview.md#invoice-sections).

Az alábbi táblázat azt mutatja be, hogy melyik szerepkörre van szüksége bizonyos feladatok elvégzéséhez a számlázási szakaszokra vonatkozóan.

### <a name="manage-invoice-section-permissions-and-properties"></a>A számlázási szakaszok engedélyeinek és tulajdonságainak kezelése

|Feladatok|Számlázási szakasz tulajdonosa|Számlázási szakasz közreműködője|Számlázási szakasz olvasója|Azure-előfizetés létrehozója|Számlázásiprofil-tulajdonos|Számlázási profil közreműködője|Számlázásiprofil-olvasó |Számlakezelő|Számlázásifiók-tulajdonos|Számlázási fiók közreműködője|Számlázásifiók-olvasó 
|---|---|---|---|---|---|---|---|---|---|---|---|
|Szerepkör-hozzárendelések megtekintése a számla szakaszhoz|✔|✔|✔|✘|✔|✔|✔|✔|✔|✔|✔|
|Engedély biztosítása másoknak a számlázási szakasz megtekintésére és kezelésére|✔|✘|✘|✘|✔|✘|✘|✘|✔|✘|✘|
|A számlázási szakasz tulajdonságainak megtekintése|✔|✔|✔|✘|✔|✔|✔|✔|✔|✔|✔|
|A számlázási szakasz tulajdonságainak frissítése|✔|✔|✘|✘|✔|✔|✘|✘|✔|✔|✘|

### <a name="manage-products-for-invoice-section"></a>A számlázási szakaszokhoz tartozó termékek kezelése

|Feladatok|Számlázási szakasz tulajdonosa|Számlázási szakasz közreműködője|Számlázási szakasz olvasója|Azure-előfizetés létrehozója|Számlázásiprofil-tulajdonos|Számlázási profil közreműködője|Számlázásiprofil-olvasó |Számlakezelő|Számlázásifiók-tulajdonos|Számlázási fiók közreműködője|Számlázásifiók-olvasó 
|---|---|---|---|---|---|---|---|---|---|---|---|
|A számla szakaszhoz megvásárolt összes termék megtekintése|✔|✔|✔|✘|✔|✔|✔|✔|✔|✔|✔|
|Olyan termékek számlázásának kezelése, mint például a Mégse, az automatikus megújítás kikapcsolása és egyebek|✔|✔|✘|✘|✔|✔|✘|✘|✔|✔|✘|
|A termékekhez tartozó számlázási szakaszok módosítása|✔|✔|✘|✘|✔|✔|✘|✘|✔|✔|✘|

### <a name="manage-subscriptions-for-invoice-section"></a>A számlázási szakaszokhoz tartozó előfizetések kezelése

|Feladatok|Számlázási szakasz tulajdonosa|Számlázási szakasz közreműködője|Számlázási szakasz olvasója|Azure-előfizetés létrehozója|Számlázásiprofil-tulajdonos|Számlázási profil közreműködője|Számlázásiprofil-olvasó |Számlakezelő|Számlázásifiók-tulajdonos|Számlázási fiók közreműködője|Számlázásifiók-olvasó 
|---|---|---|---|---|---|---|---|---|---|---|---|
|A számlázási szakaszokhoz tartozó összes Azure-előfizetés megtekintése|✔|✔|✔|✘|✔|✔|✔|✔|✔|✔|✔|
|Létrehozhat Azure-előfizetéseket.|✔|✔|✘|✔|✔|✔|✘|✘|✔|✔|✘|
|Azure-előfizetések megszakítása|✘|✘|✘|✘|✘|✘|✘|✘|✘|✘|✘|
|Az Azure-előfizetés számla módosítása szakasza|✔|✔|✘|✘|✔|✔|✘|✘|✔|✔|✘|
|Számlázási tulajdonjog igénylése az előfizetésekhez más számlázási fiókok felhasználóitól|✔|✔|✘|✘|✔|✔|✘|✘|✔|✔|✘|

## <a name="subscription-billing-roles-and-tasks"></a>Az előfizetés számlázási szerepkörei és azok feladatai

Az alábbi táblázat azt mutatja be, hogy melyik szerepkörre van szüksége bizonyos feladatok elvégzéséhez az előfizetésre vonatkozóan.

|Feladatok|Számlázási szakasz tulajdonosa|Számlázási szakasz közreműködője|Számlázási szakasz olvasója|Azure-előfizetés létrehozója|Számlázásiprofil-tulajdonos|Számlázási profil közreműködője|Számlázásiprofil-olvasó |Számlakezelő|Számlázásifiók-tulajdonos|Számlázási fiók közreműködője|Számlázásifiók-olvasó 
|---|---|---|---|---|---|---|---|---|---|---|---|
|Előfizetések létrehozása|✔|✔|✘|✔|✔|✔|✘|✘|✔|✔|✘|
|Az előfizetés költséghelyének frissítése|✔|✔|✘|✘|✔|✔|✘|✘|✔|✔|✘|
|Az előfizetéshez tartozó számlázási szakaszok módosítása|✔|✔|✘|✘|✔|✔|✘|✘|✔|✔|✘|
|Az előfizetés számlázási profiljának módosítása|✘|✘|✘|✘|✔|✔|✘|✘|✔|✔|✘|
|Azure-előfizetések megszakítása|✘|✘|✘|✘|✘|✘|✘|✘|✘|✘|✘|

## <a name="manage-billing-roles-in-the-azure-portal"></a>Számlázási szerepkörök kezelése az Azure Portalon

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Keressen rá a **Költségkezelés + számlázás** kifejezésre.

   ![Képernyőkép az Azure Portal keresőmezőjéről](./media/understand-mca-roles/billing-search-cost-management-billing.png)

3. A **Hozzáférés-vezérlés (IAM)** lehetőségnél válassza ki azt a hatókört, amelyhez hozzáférést szeretne biztosítani, például a számlázási fiókot, a számlázási profilt vagy a számlázási szakaszt.

4. A Hozzáférés-vezérlés (IAM) lap a hatókör szerepköreihez rendelt felhasználókat és csoportokat listázza.

   ![Képernyőkép a számlázási fiók rendszergazdáinak listájáról](./media/understand-mca-roles/billing-list-admins.png)

5. Ha szeretne hozzáférést biztosítani egy felhasználó számára, a lap tetején válassza a **Hozzáadás** lehetőséget. A Szerepkör legördülő listában válasszon ki egy szerepkört. Adja meg annak a felhasználónak az e-mail-címét, aki számára hozzáférést kíván biztosítani. A szerepkör hozzárendeléséhez válassza a **Mentés** lehetőséget.

   ![Képernyőkép egy rendszergazda számlázási fiókhoz történő hozzáadásáról](./media/understand-mca-roles/billing-add-admin.png)

6. Egy felhasználó hozzáférésének eltávolításához válassza ki az eltávolítani kívánt szerepkör-hozzárendeléssel rendelkező felhasználót. Válassza az Eltávolítás lehetőséget.

   ![Képernyőkép egy rendszergazda számlázási fiókból történő eltávolításáról](./media/understand-mca-roles/billing-remove-admin.png)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft-ügyfélszerződéshez való hozzáférés ellenőrzése
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Segítségre van szüksége? Kapcsolatfelvétel a támogatási szolgáltatással
Ha segítségre van szüksége, [vegye fel a kapcsolatot az ügyfélszolgálattal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.

## <a name="next-steps"></a>Következő lépések

A számlázási fiókkal kapcsolatos tudnivalókért tekintse meg az alábbi cikkeket:

- [Ismerkedés a Microsoft-ügyfélszerződéshez tartozó számlázási fiókkal](../understand/mca-overview.md)
- [Azure-előfizetés létrehozása a Microsoft-ügyfélszerződéshez tartozó számlázási fiókban](create-subscription.md)
