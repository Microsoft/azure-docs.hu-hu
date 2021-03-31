---
title: Katalógus engedélyei (előzetes verzió)
description: Ez a cikk áttekintést nyújt a Role-Based Access Control (RBAC) Azure-beli hatáskörébe való konfigurálásáról
author: yarong
ms.author: yarong
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 10/20/2020
ms.openlocfilehash: b351be1e7212dc9923f701599dd951a73254afe0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98610369"
---
# <a name="role-based-access-control-in-azure-purviews-data-plane"></a>Szerepköralapú hozzáférés-vezérlés az Azure-beli hatáskörébe adatsíkon

Ez a cikk azt ismerteti, hogyan valósul meg a Role-Based Access Control (RBAC) az Azure hatáskörébe tartozó [adatsíkon](../azure-resource-manager/management/control-plane-and-data-plane.md#data-plane).

> [!IMPORTANT]
> A felügyelői fiókot létrehozó rendszerbiztonsági tag automatikusan megkapja az összes adatsík-engedélyt, függetlenül attól, hogy milyen adatsíkok-szerepkörök lehetnek vagy sem. Ahhoz, hogy bármely más felhasználó bármit elvégezzen az Azure-ban, az előre meghatározott adatsík-szerepkörök közül legalább az egyiknek kell lennie.

## <a name="azure-purviews-pre-defined-data-plane-roles"></a>Az Azure hatáskörébe tartozó előre definiált adatsíkok szerepkörei

Az Azure hatáskörébe olyan előre definiált adatsíkok-szerepköröket határozhat meg, amelyek segítségével szabályozhatja, hogy ki férhet hozzá az Azure hatáskörébe. Ezek a szerepkörök az alábbiak:

* A **hatáskörébe tartozó Adatolvasói szerepkör** – hozzáfér a hatáskörébe-portálhoz, és az összes tartalmat beolvashatja az Azure hatáskörébe, kivéve a vizsgálati kötéseket
* A **hatáskörébe tartozó Adatkurátori szerepkör** – hozzáféréssel rendelkezik a hatáskörébe-portálhoz, és az összes tartalmat beolvashatja az Azure hatáskörébe, kivéve a vizsgálat kötéseit, szerkesztheti az eszközökre vonatkozó információkat, szerkesztheti a besorolási definíciókat és a szószedetet, és a besorolásokat és a szószedetet is alkalmazhatja az eszközökre.
* A **hatáskörébe tartozó adatforrás-rendszergazdai szerepkör** – nem fér hozzá a hatáskörébe-portálhoz (a felhasználónak az Adatolvasó-vagy adatkezelői szerepkörökben is szerepelnie kell), és képes kezelni az adatokat az Azure hatáskörébe, de nem rendelkezik olvasási vagy írási hozzáféréssel a tartalomhoz az Azure-ban a vizsgálathoz kapcsolódóan.

## <a name="understanding-how-to-use-azure-purviews-data-plane-roles"></a>Az Azure hatáskörébe tartozó adatsík-szerepkörök használatának ismertetése

Egy Azure-beli hatáskörébe tartozó fiók létrehozásakor a rendszer úgy kezeli a létrehozót, mintha a hatáskörébe tartozó adatkezelőben és a hatáskörébe tartozó adatforrások rendszergazdai szerepköreiben vannak. A rendszer azonban nem rendeli hozzá a fióklétrehozót ezekkel a szerepkörökkel a szerepkörtárolóhoz. Az Azure Purview felismeri, hogy a rendszerbiztonsági tag a fiók létrehozója, és az identitása alapján kiterjeszti rá ezeket a képességeket.

Az összes többi felhasználó csak akkor használhatja az Azure Purview-fiókot, ha legalább e szerepkörök egyikét hozzájuk rendelik. Ez azt jelenti, hogy amikor létrejön egy Azure-beli hatáskörébe tartozó fiók, egyetlen, de a létrehozó hozzáférhet a fiókhoz, vagy használhatja az API-kat, amíg azok egy vagy több előző meghatározott szerepkörbe bekerülnek.

Vegye figyelembe, hogy a hatáskörébe tartozó adatforrás-rendszergazdai szerepkör két támogatott forgatókönyvet tartalmaz. Az első forgatókönyv olyan felhasználók számára készült, akik már a hatáskörébe tartozó adatolvasók vagy a hatáskörébe tartozó adatkezelők számára is lehetővé kell tenni a vizsgálatok létrehozását. Ezeknek a felhasználóknak két szerepkörrel kell rendelkezniük, amelyek közül legalább az egyik a hatáskörébe tartozó Adatolvasó vagy a hatáskörébe tartozó adatkezelő, valamint a hatáskörébe tartozó adatforrás-rendszergazdai szerepkörbe kerül.

A hatáskörébe tartozó adatforrások rendszergazdájának más forgatókönyve olyan programozott folyamatokhoz, mint például a szolgáltatásokhoz, amelyeknek képesnek kell lenniük a vizsgálatok beállítására és figyelésére, de nem férnek hozzá a katalógus összes adatforrásához.

Ez a forgatókönyv úgy valósítható meg, ha az egyszerű szolgáltatásnevet a hatáskörébe tartozó adatforrás-rendszergazdai szerepkörbe helyezi, anélkül, hogy a másik két szerepkörbe kellene helyeznie őket. A rendszerbiztonsági tag nem fér hozzá a hatáskörébe-portálhoz, de rendben van mivel ez egy programozott rendszerbiztonsági tag, és csak API-kon keresztül kommunikál.

## <a name="putting-users-into-roles"></a>Felhasználók szerepkörökbe helyezése

Így az Azure-beli hatáskörébe tartozó fiók létrehozása után az első teendő az, hogy személyeket rendeljen ezekhez a szerepkörökhöz.

A szerepkör-hozzárendelés az [Azure RBAC](../role-based-access-control/overview.md)keresztül felügyelhető.

Az Azure-ban csak két beépített vezérlési sík szerepkör rendelhető hozzá a felhasználói szerepkörökhöz, ezek lehetnek tulajdonosok vagy felhasználói hozzáférési rendszergazdák. Tehát ahhoz, hogy a felhasználók az Azure hatáskörébe tartozó szerepkörökbe kerüljenek, meg kell találni valakit, aki tulajdonosi vagy felhasználói hozzáférési rendszergazda, vagy önmaga is lehet.

### <a name="an-example-of-assigning-someone-to-a-role"></a>Példa valaki szerepkörhöz való hozzárendelésére

1. Nyissa meg az Azure-beli https://portal.azure.com hatáskörébe tartozó fiókját, és navigáljon hozzá
1. A bal oldalon kattintson a "hozzáférés-vezérlés (IAM)" elemre.
1. Ezután kövesse az [itt](../role-based-access-control/quickstart-assign-role-user-portal.md#create-a-resource-group) megadott általános utasításokat

## <a name="role-definitions-and-actions"></a>Szerepkör-definíciók és műveletek

A szerepkörök műveletek gyűjteménye vannak meghatározva. A szerepkörök definiálásával kapcsolatos további [információkért tekintse meg a](../role-based-access-control/role-definitions.md) következőt:. [Itt](../role-based-access-control/built-in-roles.md) megtekintheti az Azure-beli hatáskörébe tartozó szerepkörökhöz tartozó szerepkör-definíciókat.

## <a name="getting-added-to-a-data-plane-role-in-an-azure-purview-account"></a>Adatközponti szerepkör hozzáadása egy Azure-beli hatáskörébe tartozó fiókban

Ha hozzáférést szeretne biztosítani egy Azure-beli hatáskörébe tartozó fiókhoz, használja a stúdióját, vagy hívja meg az API-kat, hogy hozzá kell adnia egy Azure-beli hatáskörébe tartozó adatközpont-szerepkört. Az egyetlen olyan személy, aki az Azure hatáskörébe tartozó fiók tulajdonosai vagy felhasználói hozzáférés-rendszergazdája. A legtöbb felhasználó számára a következő lépés egy helyi rendszergazda megkeresése, aki segíthet megtalálni azokat a megfelelő személyeket, akik hozzáférést biztosíthatnak.

Azok a felhasználók, akik hozzáférnek a vállalata [Azure Portalhoz](https://portal.azure.com) , megkereshetik azt az adott Azure-fiókot, amelyhez csatlakozni szeretne, kattintson a "hozzáférés-vezérlés (iam)" fülre, és tekintse meg, kik a tulajdonosok vagy a felhasználói hozzáférés rendszergazdái (UAAs). Vegye figyelembe azonban, hogy egyes esetekben Azure Active Directory csoportokat vagy egyszerű szolgáltatásokat tulajdonosként vagy UAAs lehet használni, ebben az esetben előfordulhat, hogy nem lehet közvetlenül kapcsolatba lépni velük. Ehelyett egy rendszergazdának kell megtalálnia a segítségét.

## <a name="who-should-be-assigned-to-what-role"></a>Kinek kell hozzárendelni a szerepkörhöz?

|Felhasználói forgatókönyv|Megfelelő szerepkör (ek)|
|-------------|-----------------|
|Csak meg kell találni az eszközöket, nem szeretnék szerkeszteni semmit|A hatáskörébe tartozó Adatolvasó szerepkör|
|Szerkeszteni kell az eszközökre vonatkozó információkat, besorolásokat kell elhelyezni rájuk, hozzájuk rendelni őket a Szószedet bejegyzéseihez stb.|A hatáskörébe tartozó adatkurátori szerepkör|
|Szerkeszteni kell a szószedetet, vagy új besorolási definíciókat kell beállítani|A hatáskörébe tartozó adatkurátori szerepkör|
|Az alkalmazás egyszerű szolgáltatásának az Azure hatáskörébe kell küldenie az adatküldés|A hatáskörébe tartozó adatkurátori szerepkör|
|Be kell állítania a vizsgálatokat a hatáskörébe studión keresztül|A hatáskörébe tartozó adatforrás-rendszergazdai szerepkör és a hatáskörébe tartozó adatolvasói szerepkör vagy a hatáskörébe tartozó adatkezelői szerepkör legalább egyike|
|Engedélyezni kell egy egyszerű szolgáltatásnevet vagy más programozási identitást az Azure-beli vizsgálatok beállításához és figyeléséhez anélkül, hogy a programozott identitás hozzáférhessen a katalógus adataihoz |A hatáskörébe tartozó adatforrás rendszergazdai szerepköre|
|Szerepkörökbe kell helyezni a felhasználókat az Azure hatáskörébe | Tulajdonos vagy felhasználói hozzáférés rendszergazdája |

A rendszerbiztonsági tag szerepkörhöz való hozzáadásával kapcsolatos további információkért tekintse meg a rövid útmutató [: Azure hatáskörébe tartozó fiók létrehozása](create-catalog-portal.md) című témakört.

## <a name="next-steps"></a>Következő lépések

* [Adatokkal kapcsolatos megállapítások](concept-insights.md)
