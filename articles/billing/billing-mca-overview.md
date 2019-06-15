---
title: Ismerkedés az Ön számlázási fiókjához egy Microsoft Ügyfélszerződéséhez – Azure |} A Microsoft Docs
description: A Microsoft vevői szerződés számlázási fiók ismertetése
services: billing
documentationcenter: ''
author: amberbhargava
manager: amberbhargava
editor: banders
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/28/2019
ms.author: banders
ms.openlocfilehash: ea625a61ed600dbaa22fef85987e9570a6fb7dbc
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60371452"
---
# <a name="get-started-with-your-billing-account-for-a-microsoft-customer-agreement"></a>Ismerkedés az Ön számlázási fiókjához a Microsoft ügyfél-szerződés

Minden egyezmény jelentkezik a Microsoft az Azure számlázási fiók jön létre. A számlázási fiók használatával kezelése, számlázás és nyomon követni a költségeket. Több számlázási fiók rendelkezhet. Például előfordulhat, hogy feliratkozott az Azure-hoz a személyes projektekhez. Hozzáférés az Azure-ban a szervezet nagyvállalati szerződéssel vagy Microsoft Ügyfélszerződéséhez keresztül is lehet. Az egyes forgatókönyvek esetében, a számlázási fiókra kell.

Ez a cikk a Microsoft vevői szerződés számlázási fiók vonatkozik. [Ellenőrizze, hogy egy Microsoft Ügyfélszerződéséhez hozzáférést](#check-access-to-a-microsoft-customer-agreement).

## <a name="understand-billing-account"></a>Számlázási fiók ismertetése

A Microsoft ügyfél-szerződés az Ön számlázási fiókjához tartalmaz egy vagy több számlázási profilok –, amelyek lehetővé teszik a számlákat és a fizetési módok kezeléséhez. Minden egyes számlázási profil egy vagy több, amelyekkel a számlázási profilja számlán költségek rendszerezése számla szakasz tartalmazza.

Az alábbi ábrán a számlázási fiók, a számlázási profilok és a számlaszakaszok közötti kapcsolat látható.

![Bemutató diagram, amelyek a Microsoft vevői szerződés számlázási hierarchia](./media/billing-mca-overview/mca-billing-hierarchy.png)

A számlázási fiók szerepkörök rendelkeznek a legmagasabb szintű engedélyeket. Alapértelmezés szerint a szervezet Azure Active Directory csak a globális rendszergazdák hozzáférhetnek a számlázási fiók. Ezek a szerepkörök számlák megtekintése, valamint nyomon követheti a teljes cég számára, mint például a pénzügyi vagy az informatikai vezetők költségek felhasználóknak hozzá kell rendelni. További információkért lásd: [számlázási fiók szerepkörök és feladatok](billing-understand-mca-roles.md#billing-account-roles-and-tasks).

## <a name="understand-billing-profiles"></a>Számlázási a profilok megismerése

A számlázási profil használatával a számlázási és fizetési módok kezelése. A havi számla jön létre az Azure-előfizetések és az egyéb termékek, a számlázási profillal vásárolt. Fizetési módokat kell fizetnem a számla kell használnia.

A számlázási fiók automatikusan létrejön egy számlázási profilja. További számlákat beállításához új számlázási profilokat hozhat létre. Például érdemes különböző számlák minden részleg vagy projekt a szervezetben.

Egy számlázási profilja számlán költségek rendszerezéséhez számla szakaszok is létrehozhat. Az Azure-előfizetések és a számla szakasz vásárolt termékek jelennek meg a szakaszban. A számlázási profilja számla díjak minden számlán szereplő szakaszokat tartalmaz.

A számlázási profilban szerepkörök engedélye számlákat és a fizetési módok megtekintése és kezelése. Ezek a szerepkörök hozzárendelése a felhasználókhoz, például a számviteli csoport tagjai számlákat a szervezet fizeti ki. További információkért lásd: [számlázási profil szerepkörök és feladatok](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

### <a name="monthly-invoice-generated-for-each-billing-profile"></a>Havi számla hoz létre minden egyes számlázási profilja

A havi számla jön létre az egyes számlázási profilok a számla dátuma. A számlán minden költséget az előző hónap tartalmazza.

A számla megtekintése, töltse le a dokumentumokat, és beszerezni a jövőbeli számlákat e-mailek, az Azure Portalon a beállítás módosításához. További információkért lásd: [számlákat letölteni a Microsoft vevői szerződés](billing-download-azure-invoice-daily-usage-date.md#download-invoices-for-a-microsoft-customer-agreement).

### <a name="invoices-paid-through-payment-methods"></a>Fizetési módok keresztül fizetett számlák

Minden egyes számlázási profilja rendelkezik a saját fizetési módok, amely segítségével a számlákat kell fizetnie. A következő fizetési módokat támogatja:

| Típus             | Meghatározás  |
|------------------|-------------|
|Az Azure-kreditek    |  Kreditek a rendszer automatikusan alkalmazza, a teljes számlázott összeg a számla számításához, akkor meg kell fizetni. További információkért lásd: [nyomon követheti a számlázási profilja Azure kreditegyenlegét](billing-mca-check-azure-credits-balance.md). |
|Ellenőrizze vagy vezetéket használtak átvitel | A fizetendő összeg fizethet a szolgáltatásért számláit ellenőrzés vagy átviteli keresztül átvitele. A fizetési utasításokat kapnak a számlán |

### <a name="control-azure-marketplace-and-reservation-purchases-by-applying-policies"></a>Vezérlő Azure Marketplace-en és a fenntartás vásárlások házirendek alkalmazásával

A alkalmazni szabályzatokat, amelyekkel szabályozható a számlázási profilt használó vásárlásokra. Beállíthatja a házirendek letiltása az Azure-foglalásokat és Piactéri termékek vásárlására. A szabályzatok érvénybe lépnek, ha a számlázási profilja számla részeiben létrehozott előfizetések nem használható az Azure-fenntartásokat és a Piactéri termékek megvásárlására.

### <a name="allow-users-to-create-azure-subscriptions-by-enabling-azure-plans"></a>Engedélyezése a felhasználók számára az Azure-csomagok engedélyezésével az Azure-előfizetések létrehozása

Az Azure-csomagok automatikusan engedélyezve van egy számlázási profilt hoz létre. A számlázási profilban szereplő összes számla szakasz az alábbi díjcsomagok hozzáférést kap. A számla szakasz hozzáféréssel rendelkező felhasználók a tervek használata az Azure-előfizetéseket hozhasson létre. Nem hozhatnak létre az Azure-előfizetések, kivéve, ha a számlázási profilja engedélyezve van az Azure-csomag. A következő Azure-csomagok támogatottak a számlázási fiókok a Microsoft ügyfél-szerződés:

| Felkészülés             | Meghatározás  |
|------------------|-------------|
|A Microsoft Azure-csomagot   | Engedélyezi a felhasználók hozhatnak létre előfizetéseket, amelyek futtathatók a számítási feladatokat. További információkért lásd: [a Microsoft Azure-csomag](https://azure.microsoft.com/offers/ms-azr-0017g/) |
|Microsoft Azure-csomag fejlesztéshez és teszteléshez | Lehetővé teszi a Visual Studio-előfizetők hozhatnak létre előfizetéseket, amelyek korlátozott fejlesztési vagy tesztelési feladatok. Ezeket az előfizetéseket kaphat további előnyöket, például az alacsonyabb díjszabás, valamint az exkluzív virtuálisgép-lemezképek elérését az Azure Portalon. További információkért lásd: [a Microsoft a Azure DevTest tervezése](https://azure.microsoft.com/offers/ms-azr-0148g/)|

## <a name="understand-invoice-sections"></a>Számla szakaszok ismertetése

Hozzon létre számla szakaszok a költségek egy számlázási profilja számlán rendszerezéséhez. Ha például szükség lehet egyetlen számlán a szervezet, de szeretne osztály, csoport vagy projekt költségeket rendszerezéséhez. Ebben a forgatókönyvben egy számlázási profillal rendelkezik, amellyel létre minden egyes osztály, csoport vagy projekt egy számla című szakaszt.

Egy számla szakasz létrehozásakor a engedélyt adhat a többi szakasz az Azure-előfizetéseket hozhasson létre. A használati díjak és a vásárlások az előfizetések majd jelennek meg a számlán a megfelelő szakaszában.

A számla szakaszban lévő szerepkörök engedélye szabályozza, ki Azure-előfizetést hoz létre. Ezek a szerepkörök hozzárendelése a felhasználók, akik az Azure környezetet az például a mérnöki érdeklődőket és a műszaki architects a szervezeten belül. További információkért lásd: [szakasz szerepkörök és feladatok számla](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).

## <a name="check-access-to-a-microsoft-customer-agreement"></a>A Microsoft Ügyfélszerződéséhez való hozzáférés ellenőrzése
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Segítség Forduljon a támogatási szolgálathoz.

Ha segítségre van szüksége, kell [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.

## <a name="next-steps"></a>További lépések

További információ az Ön számlázási fiókjához a következő cikkekben talál:

- [Megismerheti a Microsoft Ügyfélszerződéséhez rendszergazdai szerepkörök az Azure-ban](billing-understand-mca-roles.md)
- [További Azure-előfizetés létrehozása a Microsoft ügyfél-szerződés](billing-mca-create-subscription.md)
- [A számla a költségek rendszerezéséhez szakaszok létrehozása](billing-mca-section-invoice.md)