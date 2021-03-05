---
title: A Azure AD Identity Governance használatával áttekintheti és eltávolíthatja azokat a külső felhasználókat, akik már nem rendelkeznek erőforrás-hozzáféréssel
description: Hozzáférési felülvizsgálatok használata a partnerszervezetek tagjaiból való hozzáférés megszüntetéséhez
services: active-directory
documentationcenter: ''
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 09/06/2020
ms.author: ajburnle
ms.openlocfilehash: fe68ec498d17ec20778c8f34fc6ffa1f0964c44e
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102176960"
---
# <a name="use-azure-active-directory-azure-ad-identity-governance-to-review-and-remove-external-users-who-no-longer-have-resource-access"></a>Az Azure Active Directory (Azure AD) identitás-szabályozás használatával áttekintheti és eltávolíthatja azokat a külső felhasználókat, akik már nem rendelkeznek erőforrás-hozzáféréssel

Ez a cikk azokat a funkciókat és metódusokat ismerteti, amelyek lehetővé teszik a külső identitások kijelölését és kiválasztását, így áttekintheti őket, és eltávolíthatja őket az Azure AD-ből, ha már nincs rájuk szükség. A felhő minden eddiginél könnyebben együttműködik a belső vagy külső felhasználókkal. Az Office 365 bevonásával a szervezetek elkezdik megtekinteni a külső identitások (beleértve a vendégeket is) elterjedését, mivel a felhasználók az adat, dokumentum-vagy digitális munkaterületeken, például a csapatokon dolgoznak együtt. A szervezeteknek egyensúlyt kell biztosítaniuk, engedélyezniük kell az együttműködést és a biztonsági és irányítási követelmények teljesítését. Ezeknek az erőfeszítéseknek a részeként be kell vonni a külső felhasználók kiértékelését és tisztítását, akik a partner szervezeteitől származó együttműködésre kérték a bérlők számára, és azokat az Azure AD-ből távolítják el, ha már nincs rájuk szükség.

>[!NOTE]
>Az Azure AD hozzáférési felülvizsgálatok használatához érvényes prémium szintű Azure AD P2, Enterprise Mobility + Security E5 fizetett vagy próbaverziós licenc szükséges. További információ: [Azure Active Directory kiadások](../fundamentals/active-directory-whatis.md).

## <a name="why-review-users-from-external-organizations-in-your-tenant"></a>Miért érdemes áttekinteni a felhasználókat a bérlő külső szervezeteitől?

A legtöbb szervezetben a végfelhasználók kezdeményezik az üzleti partnerek és szállítók meghívásának folyamatát az együttműködéshez. A meghajtókat használó szervezeteknek együtt kell működniük az erőforrás-tulajdonosok és a végfelhasználók számára, hogy rendszeresen értékeljék és tanúsítsák a külső felhasználókat. Az új együttműködési partnerek bevezetésének folyamata gyakran megtervezve, de sok olyan együttműködéssel, amely nem rendelkezik egyértelmű befejezési dátummal, nem mindig nyilvánvaló, ha a felhasználónak már nincs szüksége a hozzáférésre. Emellett az identitás életciklus-kezelési szolgáltatásával a vállalatok megtarthatják az Azure AD tisztítását, és eltávolíthatják azokat a felhasználókat, akiknek már nincs szükségük a szervezet erőforrásaihoz való hozzáférésre. A címtárban lévő partnerek és szállítók számára csak a releváns identitási referenciák segítenek csökkenteni az alkalmazottak kockázatát, és a külső felhasználók számára a hozzáférésük véletlen kiválasztását és engedélyezését, amelyeket el kell távolítani. Ez a dokumentum számos olyan lehetőséget mutat be, amely a javasolt proaktív javaslatoktól kezdve a külső identitások szabályozása érdekében a reaktív és a tisztítási tevékenységekre terjed ki.

## <a name="use-entitlement-management-to-grant-and-revoke-access"></a>Jogosultság-kezelés használata a hozzáférés engedélyezéséhez és visszavonásához

A jogosultság-kezelési funkciók lehetővé teszik a [külső identitások automatizált életciklusát](entitlement-management-external-users.md#manage-the-lifecycle-of-external-users) az erőforrásokhoz való hozzáféréssel. Ha olyan folyamatokat és eljárásokat hoz létre, amelyek a jogosultságok felügyeletén keresztül kezelik a hozzáférést, és az erőforrásokat hozzáférési csomagokon keresztül teszi közzé, az erőforrásokhoz való külső felhasználói hozzáférés nyomon követése pedig sokkal kevésbé bonyolult megoldás. Ha az Azure AD-ben a [jogosultságok felügyeleti hozzáférési csomagjain](entitlement-management-overview.md) keresztül kezeli a hozzáférést, a szervezet központilag definiálhatja és kezelheti a felhasználók hozzáférését, valamint a partnerek szervezeteinek felhasználói számára. A jogosultsági felügyelet a hozzáférési csomagok jóváhagyásait és hozzárendeléseit használja annak nyomon követéséhez, hogy a külső felhasználók Mikor igényeltek hozzáférést. Ha egy külső felhasználó elveszíti az összes hozzárendelést, akkor a jogosultságok kezelése automatikusan eltávolítja ezeket a külső felhasználókat a bérlőtől. 

## <a name="find-guests-not-invited-through-entitlement-management"></a>Jogosultság-kezeléssel nem meghívott vendégek keresése

Ha az alkalmazottak jogosultak a külső felhasználókkal való együttműködésre, akkor a szervezeten kívül tetszőleges számú felhasználót hívhatnak meg. Ha külső partnereket keres és csoportosít a vállalat által igazított dinamikus csoportokba, és a felülvizsgálatuk nem valósítható meg, előfordulhat, hogy előfordulhat, hogy túl sok különböző egyéni vállalat tekinti át, vagy nincs tulajdonos vagy szponzor a szervezet számára. A Microsoft egy PowerShell-parancsfájlt biztosít, amely segítségével elemezheti a külső identitások használatát a bérlőben. A parancsfájl enumerálja a külső identitásokat, és kategorizálja őket. A parancsfájl segítségével azonosíthatja és megtisztíthatja azokat a külső identitásokat, amelyekre már nincs szükség. A szkript kimenetének részeként a parancsfájl-minta támogatja az azonosított, csoport nélküli külső partnereket tartalmazó biztonsági csoportok automatikus létrehozását, az Azure AD-hozzáférési felülvizsgálatok további elemzéséhez és használatához.
A szkript elérhető a [githubon](https://github.com/microsoft/access-reviews-samples/tree/master/ExternalIdentityUse). Miután a parancsfájl futása befejeződött, létrehoz egy HTML-kimeneti fájlt, amely a következőhöz tartozó külső identitásokat ismerteti:

- Már nem rendelkezik csoporttagság a bérlőben
- Jogosultsági szintű szerepkör hozzárendelése a bérlőhöz
- Hozzárendelés egy alkalmazáshoz a bérlőben

A kimenet ezen külső identitások egyes tartományait is tartalmazza. 

>[!NOTE]
>A fent hivatkozott parancsfájl egy példa a csoporttagság, a szerepkör-hozzárendelések és az alkalmazás-hozzárendelések ellenőrzéséhez az Azure AD-ben. Előfordulhat, hogy a külső felhasználók az Azure AD-n kívül kaptak más hozzárendeléseket, például a SharePoint (közvetlen tagsági hozzárendelés) vagy az Azure RBAC vagy az Azure DevOps.

## <a name="review-resources-used-by-external-identities"></a>Külső identitások által használt erőforrások áttekintése

Ha külső identitásokkal rendelkezik olyan erőforrásokkal, mint a csapatok vagy más olyan alkalmazások, amelyek még nem szabályozzák a jogosultságok felügyeletét, érdemes lehet rendszeresen megtekinteni a hozzáférést ezekhez az erőforrásokhoz. Az Azure AD [hozzáférési](create-access-review.md) felülvizsgálatai lehetővé teszik a külső identitások hozzáférésének áttekintését azáltal, hogy az erőforrás tulajdonosát, a külső identitásokat vagy egy másik delegált személyt tanúsítanak, hogy továbbra is szükség van-e a hozzáférésre. A hozzáférési felülvizsgálatok egy erőforrást céloznak meg, és olyan felülvizsgálati tevékenységet is létrehoznak, amelynek hatóköre csak az erőforráshoz vagy a vendég felhasználóhoz fér hozzá. A felülvizsgáló Ekkor megtekintheti az azokat a felhasználókat, akik számára szükséges a felülvizsgálat – vagy minden felhasználó, beleértve a szervezet alkalmazottai vagy a külső identitások listáját.

![hozzáférés áttekintése csoport használatával](media/access-reviews-external-users/group-members.png)

Az erőforrás-tulajdonos által vezérelt felülvizsgálati kulturális környezet létrehozásával szabályozható a külső identitásokhoz való hozzáférés. Az erőforrás-tulajdonosok, amelyek a hozzáféréshez, a rendelkezésre álláshoz és a saját információk biztonságához felelősek, a legtöbb esetben a legjobb célközönség, hogy az erőforrásokhoz való hozzáférésre vonatkozó döntéseket megközelítsék, és közelebb kerüljenek azokhoz a felhasználókhoz, akik a központi informatikai szolgáltatáshoz, illetve a sok külsőt kezelő szponzorhoz férnek hozzá.

## <a name="create-access-reviews-for-external-identities"></a>Külső identitások hozzáférési felülvizsgálatának létrehozása

Azok a felhasználók, akik már nem férnek hozzá a bérlő összes erőforrásához, eltávolíthatók, ha már nem működnek együtt a szervezettel. A külső identitások blokkolásának és törlésének megkezdése előtt érdemes lehet ezeket a külső felhasználókat használni, és győződjön meg arról, hogy nem adott meg olyan projektet vagy állandó hozzáférést, amelyhez továbbra is szüksége van. Ha olyan csoportot hoz létre, amely az összes olyan külső identitást tartalmazza, amelynek a megtalált tagja nem fér hozzá a bérlő összes erőforrásához, a hozzáférési felülvizsgálatok segítségével saját maga is tanúsíthatja, hogy továbbra is szükség van-e vagy rendelkezik hozzáféréssel – vagy továbbra is elérhetőnek kell lennie a jövőben. A felülvizsgálat részeként a hozzáférési felülvizsgálatok felülvizsgálati folyamata a jóváhagyás szükségességének megkövetelése a **jóváhagyási** függvénnyel megkövetelheti a külső felhasználók számára a folyamatos hozzáférés indoklását, amelyen keresztül megtudhatja, hogy hol és hogyan férhet hozzá a bérlőhöz. Emellett engedélyezheti a további tartalom megadását a **felülvizsgáló e-mail** -szolgáltatáshoz, hogy a felhasználók tudják, hogy elvesztik a hozzáférést, ha nem válaszolnak, és ha továbbra is hozzáférésre van szükségük, indoklásra van szükség. Ha azt szeretné, hogy a hozzáférési felülvizsgálatok ne tudják **letiltani és törölni** a külső identitásokat, akkor a következő szakaszban leírtak szerint használhatja a Letiltás és Törlés lehetőséget.

![a felülvizsgálat hatókörének korlátozása csak vendég felhasználók számára](media/access-reviews-external-users/guest-users-only.png)

A felülvizsgálat befejeződése után a **Results (eredmények** ) oldalon látható az egyes külső identitások által adott válasz áttekintése. Dönthet úgy, hogy automatikusan alkalmazza az eredményeket, és lehetővé teszi a hozzáférési felülvizsgálatok letiltását és törlését. Azt is megteheti, hogy áttekinti a megadott válaszokat, és eldönti, hogy szeretné-e eltávolítani a felhasználó hozzáférését, vagy ha további információkat szeretne kapni a döntés meghozatala előtt. Ha egyes felhasználók továbbra is hozzáférhetnek az olyan erőforrásokhoz, amelyeket még nem vizsgált meg, használhatja a felülvizsgálatot a felderítés részeként, és gazdagíthatja a következő felülvizsgálati és igazolási ciklust.

## <a name="disable-and-delete-external-identities-with-azure-ad-access-reviews"></a>Külső identitások letiltása és törlése az Azure AD hozzáférési felülvizsgálatokkal

A nem kívánt külső identitások más erőforrásokból, például csoportokból vagy alkalmazásokból való eltávolításának lehetősége mellett az Azure AD hozzáférési felülvizsgálatai letilthatják a külső identitásokat a bérlőbe való bejelentkezéstől, és 30 nap elteltével törölhetik a bérlő külső identitásait. Miután kiválasztotta a **felhasználó letiltását 30 napig, majd eltávolítja a felhasználót a bérlőből**, a felülvizsgálat 30 napig a "alkalmazás" állapotban marad. Ezen időszak alatt a beállítások, eredmények, felülvizsgálók vagy naplók az aktuális felülvizsgálat alatt nem tekinthetők meg és nem konfigurálhatók. 

![befejezési beállítások](media/access-reviews-external-users/upon-completion-settings.png)

Új hozzáférési felülvizsgálat létrehozásakor a "befejezési beállítások" szakaszban a **megtagadott felhasználókra vonatkozó művelet** megadásával megadható, hogy a **felhasználók 30 napig jelentkezzenek be, majd a bérlőből távolítsák el a felhasználót**.

Ezzel a beállítással azonosíthatja, letilthatja és törölheti az Azure AD-bérlő külső identitásait. A felülvizsgáló által áttekintett és megtagadott külső identitások le lesznek tiltva és törlődnek, az erőforrás-hozzáféréstől vagy a csoporttagságtól függetlenül. Ez a beállítás a legutóbb Utolsó lépésként használatos, miután ellenőrizte, hogy a külső felhasználók – áttekintés többé nem hordozzák az erőforrás-hozzáférést, és biztonságosan eltávolíthatók a bérlőtől, vagy ha meg szeretné győződni arról, hogy azok el lesznek távolítva, függetlenül attól, hogy azok állandó hozzáféréssel rendelkeznek. A "letiltás és törlés" funkció először blokkolja a külső felhasználót, így elkerülheti a bérlőbe való bejelentkezés és az erőforrások elérésének lehetőségét. Ebben a szakaszban nem vonja vissza az erőforrás-hozzáférést, és ha újra szeretné létrehozni a külső felhasználót, a bejelentkezést újra lehet konfigurálni. Ha további műveletre nincs szükség, a rendszer 30 nap elteltével törli a letiltott külső identitást, eltávolítja a fiókot és a hozzáférését.

## <a name="next-steps"></a>Következő lépések

- [Hozzáférési felülvizsgálatok – Graph API](/graph/api/resources/accessreviews-root?view=graph-rest-beta)
- [Jogosultságkezelés – Graph API](/graph/api/resources/entitlementmanagement-root?view=graph-rest-beta)
