---
title: A Microsoft-ügyfélszerződéshez tartozó számlázási szerepkörök – Azure
description: Megismerheti a Microsoft-ügyfélszerződéshez tartozó számlázási szerepköröket az Azure-beli számlázási fiókok esetében.
author: amberbhargava
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 02/05/2021
ms.author: banders
ms.openlocfilehash: 00ac61567502984759c5db9837060c86aaee378d
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99593586"
---
# <a name="understand-microsoft-customer-agreement-administrative-roles-in-azure"></a>A Microsoft-ügyfélszerződéshez tartozó felügyeleti szerepkörök ismertetése az Azure-ban

A Microsoft-ügyfélszerződéshez tartozó számlázási fiók kezeléséhez az alábbi szakaszokban ismertetett szerepköröket használhatja. Az Azure beépített szerepkörei mellett ezekkel a szerepkörökkel szabályozhatja az erőforrásokhoz történő hozzáférést. További információ: [Beépített Azure-szerepkörök](../../role-based-access-control/built-in-roles.md).

Ez a cikk a Microsoft-ügyfélszerződéshez tartozó számlázási fiókokra vonatkozik. Ellenőrizze, hogy rendelkezik-e hozzáféréssel a Microsoft-ügyfélszerződéshez.

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
|Számlakezelő|A számlázási profilhoz tartozó számlák megtekintése|
|Számlázási szakasz tulajdonosa|A számlázási szakasz tartalmát kezeli.|
|Számlázási szakasz közreműködője|A számlázási szakasz tartalmát kezeli az engedélyeket kivéve.|
|Számlázási szakasz olvasója|A számlaszakasz tartalmának írásvédett nézete|
|Azure-előfizetés létrehozója|Létrehozhat Azure-előfizetéseket.|

## <a name="billing-account-roles-and-tasks"></a>A számlázási fiók szerepkörei és azok feladatai

A számlázási fiókkal kezelheti a szervezet számlázását. A számlázási fiókkal rendszerezheti a költségeket, monitorozhatja a díjakat és a számlákat, valamint szabályozhatja a számlázáshoz való hozzáférést a szervezetben. További információkért tekintse meg [a számlázási fiók bemutatását](../understand/mca-overview.md#your-billing-account).

Az alábbi táblázat azt mutatja, hogy melyik szerepkörre van szüksége bizonyos feladatok elvégzéséhez a számlázási fiókra vonatkozóan.

### <a name="manage-billing-account-permissions-and-properties"></a>A számlázási fiók engedélyeinek és tulajdonságainak kezelése

|Tevékenység|Számlázásifiók-tulajdonos|Számlázási fiók közreműködője|Számlázásifiók-olvasó|
|---|---|---|---|
|A számlázási fiók meglévő engedélyeinek megtekintése|✔|✔|✔|
|Engedély megadása másoknak a számlázási fiók megtekintésére és kezelésére|✔|✘|✘|
|A számlázási fiók tulajdonságainak megtekintése, például a vállalat neve, címe és egyebek|✔|✔|✔|

### <a name="manage-billing-profiles-for-billing-account"></a>A számlázási fiókhoz tartozó számlázási profilok kezelése

|Tevékenység|Számlázásifiók-tulajdonos|Számlázási fiók közreműködője|Számlázásifiók-olvasó|
|---|---|---|---|
|A fiókhoz tartozó összes számlázási profil megtekintése|✔|✔|✔|

### <a name="manage-invoices-for-billing-account"></a>A számlázási fiókhoz tartozó számlák kezelése

|Tevékenység|Számlázásifiók-tulajdonos|Számlázási fiók közreműködője|Számlázásifiók-olvasó|
|---|---|---|---|
|A fiókhoz tartozó összes számla megtekintése|✔|✔|✔|
|A fiókban található számlák, Azure-beli használati adatok és díjak fájljai, árlisták és adózási dokumentumok letöltése|✔|✔|✔|

### <a name="manage-invoice-sections-for-billing-account"></a>A számlázási fiók számlázási szakaszainak kezelése

|Tevékenység|Számlázásifiók-tulajdonos|Számlázási fiók közreműködője|Számlázásifiók-olvasó|
|---|---|---|---|
|A fiók számlázási szakaszainak megtekintése|✔|✔|✔|

### <a name="manage-transactions-for-billing-account"></a>A számlázási fiókhoz tartozó tranzakciók kezelése

|Tevékenység|Számlázásifiók-tulajdonos|Számlázási fiók közreműködője|Számlázásifiók-olvasó|
|---|---|---|---|
|A fiókhoz tartozó összes számlázási tranzakció megtekintése|✔|✔|✔|
|A fiókhoz tartozó összes megvásárolt termék megtekintése|✔|✔|✔|

### <a name="manage-subscriptions-for-billing-account"></a>A számlázási fiókhoz tartozó előfizetések kezelése

|Tevékenység|Számlázásifiók-tulajdonos|Számlázási fiók közreműködője|Számlázásifiók-olvasó|
|---|---|---|---|
|A számlázási fiókhoz tartozó összes Azure-előfizetés megtekintése|✔|✔|✔|

## <a name="billing-profile-roles-and-tasks"></a>A számlázási profil szerepkörei és azok feladatai

A számlázási profilok segítségével kezelheti a számlákat és a fizetési módokat. A rendszer havonta létrehoz egy számlát az Azure-előfizetésekhez és a számlázási profillal vásárolt egyéb termékekhez. A számlát a fizetési módok használatával egyenlítheti ki. További információkért tekintse meg [a számlázási profilok bemutatását](../understand/mca-overview.md#billing-profiles).

Az alábbi táblázat azt mutatja, hogy melyik szerepkörre van szüksége bizonyos feladatok elvégzéséhez a számlázási profilra vonatkozóan.

### <a name="manage-billing-profile-permissions-properties-and-policies"></a>A számlázási profil engedélyeinek, tulajdonságainak és szabályzatainak kezelése

|Tevékenység|Számlázásiprofil-tulajdonos|Számlázási profil közreműködője|Számlázásiprofil-olvasó|Számlakezelő|Számlázásifiók-tulajdonos|Számlázási fiók közreműködője|Számlázásifiók-olvasó
|---|---|---|---|---|---|---|---|
|A számlázási profil meglévő engedélyeinek megtekintése|✔|✔|✔|✔|✔|✔|✔|
|Engedély megadása másoknak a számlázási profil megtekintésére és kezelésére|✔|✘|✘|✘|✘|✘|✘|
|A számlázási profil tulajdonságainak, például a rendelési számnak, az e-mailben küldött számlák beállításainak és egyéb információknak a megtekintése|✔|✔|✔|✔|✔|✔|✔|
|A számlázási profil tulajdonságainak frissítése |✔|✔|✘|✘|✘|✘|✘|
|A számlázási profilra alkalmazott szabályzatok, például az Azure-foglalások vásárlásának engedélyezése, az Azure Marketplace-en történő vásárlások engedélyezése és egyebek megtekintése|✔|✔|✔|✔|✔|✔|✔|
|Szabályzatok alkalmazása a számlázási profilban |✔|✔|✘|✘|✘|✘|✘|
|Foglalási megrendelések kezelése |✔|✔|✘|✘|✘|✘|✘|
|Foglalási megrendelések megtekintése |✔|✔|✔|✘|✘|✘|✘|

### <a name="manage-invoices-for-billing-profile"></a>A számlázási profilhoz tartozó számlák kezelése

|Tevékenység|Számlázásiprofil-tulajdonos|Számlázási profil közreműködője|Számlázásiprofil-olvasó|Számlakezelő|Számlázásifiók-tulajdonos|Számlázási fiók közreműködője|Számlázásifiók-olvasó
|---|---|---|---|---|---|---|---|
|A számlázási profilhoz tartozó összes számla megtekintése|✔|✔|✔|✔|✔|✔|✔|
|A számlázási profilhoz tartozó számlák, Azure-beli használati adatok és díjak fájljai, árlisták és adózási dokumentumok letöltése|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-invoice-sections-for-billing-profile"></a>A számlázási profil számlázási szakaszainak kezelése

|Tevékenység|Számlázásiprofil-tulajdonos|Számlázási profil közreműködője|Számlázásiprofil-olvasó|Számlakezelő|Számlázásifiók-tulajdonos|Számlázási fiók közreműködője|Számlázásifiók-olvasó
|---|---|---|---|---|---|---|---|
|A számlázási profilhoz tartozó összes számlázási szakasz megtekintése|✔|✔|✔|✔|✔|✔|✔|
|Új számlázási szakasz létrehozása a számlázási profilban|✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-transactions-for-billing-profile"></a>A számlázási profilhoz tartozó tranzakciók kezelése

|Tevékenység|Számlázásiprofil-tulajdonos|Számlázási profil közreműködője|Számlázásiprofil-olvasó|Számlakezelő|Számlázásifiók-tulajdonos|Számlázási fiók közreműködője|Számlázásifiók-olvasó
|---|---|---|---|---|---|---|---|
|A számlázási profilhoz tartozó összes számlázási tranzakció megtekintése|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-payment-methods-for-billing-profile"></a>A számlázási profilhoz tartozó fizetési módok kezelése

|Tevékenység|Számlázásiprofil-tulajdonos|Számlázási profil közreműködője|Számlázásiprofil-olvasó|Számlakezelő|Számlázásifiók-tulajdonos|Számlázási fiók közreműködője|Számlázásifiók-olvasó
|---|---|---|---|---|---|---|---|
|A számlázási profilhoz tartozó fizetési módok megtekintése|✔|✔|✔|✔|✔|✔|✔|
|A számlázási profilhoz tartozó Azure-kreditek egyenlegének nyomon követése|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-subscriptions-for-billing-profile"></a>A számlázási profilhoz tartozó előfizetések kezelése

|Tevékenység|Számlázásiprofil-tulajdonos|Számlázási profil közreműködője|Számlázásiprofil-olvasó|Számlakezelő|Számlázásifiók-tulajdonos|Számlázási fiók közreműködője|Számlázásifiók-olvasó
|---|---|---|---|---|---|---|---|
|A számlázási profilhoz tartozó összes Azure-előfizetés megtekintése|✔|✔|✔|✔|✔|✔|✔|

## <a name="invoice-section-roles-and-tasks"></a>A számlázási szakaszok szerepkörei és azok feladatai

A számlázási szakaszok segítségével rendszerezheti a számláján szereplő költségeket. Szakaszokat hozhat a költségek részlegek, fejlesztési környezetek vagy a szervezet igényei alapján történő rendszerezéséhez. Engedélyt biztosíthat másoknak Azure-előfizetések létrehozására az adott szakaszban. Az előfizetések használati díjai és vásárlásai megjelennek az adott számlázási szakaszban. További információért lásd a [számlaszakaszokat ismertető részt](../understand/mca-overview.md#invoice-sections).

Az alábbi táblázat azt mutatja be, hogy melyik szerepkörre van szüksége bizonyos feladatok elvégzéséhez a számlázási szakaszokra vonatkozóan.

### <a name="manage-invoice-section-permissions-and-properties"></a>A számlázási szakaszok engedélyeinek és tulajdonságainak kezelése

|Feladatok|Számlázási szakasz tulajdonosa|Számlázási szakasz közreműködője|Számlázási szakasz olvasója|Azure-előfizetés létrehozója|Számlázásifiók-tulajdonos|Számlázási fiók közreműködője|Számlázásifiók-olvasó |
|---|---|---|---|---|---|---|---|
|A számlázási szakaszhoz tartozó összes engedély megtekintése|✔|✔|✔|✔|✔|✔|✔|
|Engedély biztosítása másoknak a számlázási szakasz megtekintésére és kezelésére|✔|✘|✘|✘|✘|✘|✘|
|A számlázási szakasz tulajdonságainak megtekintése|✔|✔|✔|✔|✔|✔|✔|
|A számlázási szakasz tulajdonságainak frissítése|✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-products-for-invoice-section"></a>A számlázási szakaszokhoz tartozó termékek kezelése

|Feladatok|Számlázási szakasz tulajdonosa|Számlázási szakasz közreműködője|Számlázási szakasz olvasója|Azure-előfizetés létrehozója|Számlázásifiók-tulajdonos|Számlázási fiók közreműködője|Számlázásifiók-olvasó
|---|---|---|---|---|---|---|---|
|A számlázási szakaszokhoz tartozó összes megvásárolt termék megtekintése|✔|✔|✔|✘|✔|✔|✔|
|A számlázási szakaszokhoz tartozó termékek számlázásának kezelése, például a lemondás, az automatikus megújítás kikapcsolása és egyebek|✔|✔|✘|✘|✘|✘|✘|
|A termékekhez tartozó számlázási szakaszok módosítása|✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-subscriptions-for-invoice-section"></a>A számlázási szakaszokhoz tartozó előfizetések kezelése

|Feladatok|Számlázási szakasz tulajdonosa|Számlázási szakasz közreműködője|Számlázási szakasz olvasója|Azure-előfizetés létrehozója|Számlázásifiók-tulajdonos|Számlázási fiók közreműködője|Számlázásifiók-olvasó
|---|---|---|---|---|---|---|---|
|A számlázási szakaszokhoz tartozó összes Azure-előfizetés megtekintése|✔|✔|✔|✘|✔|✔|✔|
|Az előfizetéshez tartozó számlázási szakaszok módosítása|✔|✔|✘|✘|✘|✘|✘|
|Számlázási tulajdonjog igénylése az előfizetésekhez más számlázási fiókok felhasználóitól|✔|✔|✘|✘|✘|✘|✘|

## <a name="subscription-billing-roles-and-tasks"></a>Az előfizetés számlázási szerepkörei és azok feladatai

Az alábbi táblázat azt mutatja be, hogy melyik szerepkörre van szüksége bizonyos feladatok elvégzéséhez az előfizetésre vonatkozóan.

|Feladatok|Számlázási szakasz tulajdonosa|Számlázási szakasz közreműködője|Számlázási szakasz olvasója|Azure-előfizetés létrehozója|
|---|---|---|---|---|
|Létrehozhat Azure-előfizetéseket.|✔|✔|✘|✔|
|Az előfizetés költséghelyének frissítése|✔|✔|✘|✘|
|Az előfizetéshez tartozó számlázási szakaszok módosítása|✔|✔|✘|✘|

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
