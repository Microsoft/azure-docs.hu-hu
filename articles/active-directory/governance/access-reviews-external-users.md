---
title: A Azure AD Identity Governance az erőforrás-hozzáféréssel már nem rendelkezik külső felhasználók áttekintésére és eltávolítására
description: Hozzáférési felülvizsgálatok használata a partnerszervezetek tagjai hozzáférésének eltávolítására
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
ms.openlocfilehash: c976562224d4a0caca8921e46d8f8566800027ee
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532238"
---
# <a name="use-azure-active-directory-azure-ad-identity-governance-to-review-and-remove-external-users-who-no-longer-have-resource-access"></a>A Azure Active Directory (Azure AD) Identity Governance használatával áttekinti és eltávolítja azokat a külső felhasználókat, akik már nem férnek hozzá az erőforrásokhoz

Ez a cikk olyan szolgáltatásokat és metódusokat ismertet, amelyek lehetővé teszik a külső identitások kitűzését és kiválasztását, így áttekintheti és eltávolíthatja őket az Azure AD-ból, ha már nincs rájuk szükség. A felhő minden másnál egyszerűbbé teszi a belső vagy külső felhasználókkal való együttműködést. Az Office 365 használatával a szervezetek láthatják a külső identitások (beleértve a vendégeket is) elterjedését, miközben a felhasználók adatokon, dokumentumokon vagy digitális munkaterületeken, például a Teamsben dolgoznak együtt. A szervezeteknek egyensúlyban kell lenniük, lehetővé kell téve az együttműködést, valamint a biztonsági és irányítási követelményeknek való megfelelést. Ezen erőfeszítések egy részének ki kell értékelnie és ki kell távolítania azokat a külső felhasználókat, akik a bérlővel való együttműködésre meg vannak hívva, a partnerszervezetektől származnak, és el kell távolítani őket az Azure AD-ból, amikor már nincs rájuk szükség.

>[!NOTE]
>Az Azure AD prémium szintű Azure AD-hozzáférési felülvizsgálatok Enterprise Mobility + Security P2, Enterprise Mobility + Security E5 vagy próbaverziós licencre van szükség. További információ: Azure Active Directory [kiadása.](../fundamentals/active-directory-whatis.md)

## <a name="why-review-users-from-external-organizations-in-your-tenant"></a>Miért érdemes külső szervezetek felhasználóit felülvizsgálni a bérlőben?

A legtöbb szervezetben a végfelhasználók kezdeményezik az üzleti partnerek és szállítók együttműködésre való meghívásának folyamatát. Az együttműködésre való szükség arra használja a szervezeteket, hogy az erőforrás-tulajdonosok és a végfelhasználók számára rendszeres lehetőséget biztosítson a külső felhasználók értékelésére és értékelésére. Az új együttműködési partnerek beléptetése gyakran tervbe van véve és figyelembe van véve, de sok együttműködésnél nem egyértelmű a záró dátum, nem mindig nyilvánvaló, ha egy felhasználónak már nincs szüksége hozzáférésre. Emellett az identitás életciklus-kezelése arra használja a nagyvállalatokat, hogy az Azure AD-t tisztán tartják, és eltávolítsák azokat a felhasználókat, akiknek már nincs szükségük a szervezet erőforrásaihoz való hozzáférésre. Ha csak a partnerek és szállítók számára releváns identitásreferenciákat tartja meg a címtárban, az csökkenti az alkalmazottak kockázatát, akaratlanul kiválasztja és megadja a hozzáférést olyan külső felhasználók számára, akik el kellett volna távolítani őket. Ez a dokumentum számos olyan lehetőségen végigvezeti, amelyek a javasolt proaktív javaslatoktól a reaktív és tisztítási tevékenységekig a külső identitások szabályozásaig számos lehetőséget tartalmaznak.

## <a name="use-entitlement-management-to-grant-and-revoke-access"></a>Hozzáférés megadása és visszavonása jogosultságkezelés használatával

A jogosultságkezelési funkciók [lehetővé](entitlement-management-external-users.md#manage-the-lifecycle-of-external-users) teszik az erőforrásokhoz hozzáférő külső identitások automatizált életciklusát. A jogosultságkezelésen keresztüli hozzáférés kezelésére szolgáló folyamatok és eljárások létrehozásával, valamint az erőforrások hozzáférési csomagokkal való közzétételével a külső felhasználók erőforrásokhoz való hozzáférésének nyomon követése sokkal kevésbé bonyolult megoldott problémává válik. Ha jogosultságkezelési [](entitlement-management-overview.md) hozzáférési csomagokkal kezeli a hozzáférést az Azure AD-ban, a szervezet központilag meghatározhatja és kezelheti a felhasználók, valamint a partnerszervezetek felhasználóinak hozzáférését. A jogosultságkezelés hozzáférési csomagok jóváhagyását és hozzárendelését használja annak nyomon követésére, hogy külső felhasználók hol kérelmeztek és rendeltek hozzá hozzáférést. Ha egy külső felhasználó elveszíti az összes hozzárendelését, a Jogosultságkezelés automatikusan eltávolíthatja ezeket a külső felhasználókat a bérlőből. 

## <a name="find-guests-not-invited-through-entitlement-management"></a>A jogosultságkezelésen keresztül nem meghívott vendégek megkeresi

Ha az alkalmazottak jogosultak külső felhasználókkal való együttműködésre, a szervezeten kívüli felhasználók bármelyikét meghívhatja. Előfordulhat, hogy külső partnereket keres és csoportosít vállalathoz igazított dinamikus csoportokba, és nem tudja felülvizsgálni őket, mert előfordulhat, hogy túl sok különálló vállalatot kell áttekintenünk, vagy nincs tulajdonos vagy szponzor a szervezetben. A Microsoft biztosít egy PowerShell-példaszkprogramot, amely segít elemezni a külső identitások használatát a bérlőkben. A szkript számba veszi és kategorizálja a külső identitásokat. A szkript segíthet azonosítani és megtisztítani azokat a külső identitásokat, amelyekre már nincs szükség. A szkript kimenetének részeként a szkriptminta támogatja az azonosított csoportokat nem tartalmazó külső partnereket tartalmazó biztonsági csoportok automatikus létrehozását további elemzésekhez és az Azure AD hozzáférési felülvizsgálatokkal való használathoz.
A szkript elérhető a [GitHubon.](https://github.com/microsoft/access-reviews-samples/tree/master/ExternalIdentityUse) A szkript futtatása után létrehoz egy HTML kimeneti fájlt, amely felvázolja a külső identitásokat:

- Már nincs csoporttagság a bérlőben
- Jogosultsági szintű szerepkör hozzárendelése a bérlőben
- Hozzárendelése van egy alkalmazáshoz a bérlőben

A kimenet az egyes külső identitások egyéni tartományát is tartalmazza. 

>[!NOTE]
>A fent hivatkozott szkript egy példaszk szkript, amely csoporttagságot, szerepkör-hozzárendeléseket és alkalmazás-hozzárendeléseket keres az Azure AD-ban. Lehetnek más hozzárendelések is az alkalmazásokban, amelyekhez külső felhasználók az Azure AD-n kívül kaptak hozzáférést, például SharePoint (közvetlen tagság-hozzárendelés), Azure RBAC vagy Azure DevOps.

## <a name="review-resources-used-by-external-identities"></a>Külső identitások által használt erőforrások áttekintése

Ha olyan külső identitásokkal rendelkezik, amelyek olyan erőforrásokat használnak, mint a Teams vagy más, jogosultságkezelés által még nem szabályozott alkalmazások, akkor ezeket az erőforrásokhoz való hozzáférést is rendszeresen áttekintheti. Az Azure AD [hozzáférési](create-access-review.md) felülvizsgálatok lehetővé teszi a külső identitások hozzáférésének áttekintését azáltal, hogy hagyja, hogy az erőforrás tulajdonosa, maguk a külső identitások vagy egy másik megbízható meghatalmazott személy igazolja, hogy szükség van-e a folyamatos hozzáférésre. A hozzáférési felülvizsgálatok egy erőforrást céloznak meg, és létrehoznak egy felülvizsgálati tevékenységet, amely vagy mindenkire terjed ki, aki hozzáféréssel rendelkezik az erőforráshoz, vagy csak vendégfelhasználók. A felülvizsgáló ekkor látni fogja az áttekintni szükséges felhasználók listáját – vagy az összes felhasználót, beleértve a szervezet alkalmazottait vagy csak a külső identitásokat.

![csoport használata a hozzáférés áttekintéshez](media/access-reviews-external-users/group-members.png)

Az erőforrás-tulajdonosi felülvizsgálati kultúra létrehozása segít a külső identitások hozzáférésének szabályozásában. Az erőforrás-tulajdonosok, akik a hozzáférésért, a rendelkezésre állásért és a tulajdonában álló információk biztonságért hetők el, a legtöbb esetben a legjobb célközönsége, hogy döntéseket hozzanak az erőforrásaikhoz való hozzáféréssel kapcsolatban, és közelebb állnak a hozzájuk hozzáférő felhasználókhoz, mint a központi IT-hez vagy a számos külsőt kezelő szponzorhoz.

## <a name="create-access-reviews-for-external-identities"></a>Hozzáférési felülvizsgálatok létrehozása külső identitásokhoz

Azok a felhasználók, akik már nem férnek hozzá a bérlő erőforrásaihoz, eltávolíthatók, ha már nem működnek együtt a szervezettel. Mielőtt letiltja és törli ezeket a külső identitásokat, előfordulhat, hogy szeretné elérni ezeket a külső felhasználókat, és győződjön meg arról, hogy nem maradt ki egy projektből vagy állandó hozzáférésből, amelyekre még szükségük van. Amikor olyan tagként hoz létre egy csoportot, amely az összes külső identitást tartalmazza, és nem fér hozzá a bérlőben található erőforrásokhoz, a Hozzáférési felülvizsgálatok segítségével az összes külső személy saját maga tanúsíthatja, hogy továbbra is szükségük van-e a hozzáférésre, vagy hogy a jövőben is szüksége lesz-e hozzáférésre. A felülvizsgálat részeként a hozzáférési felülvizsgálatok felülvizsgálója  az Ok megkövetelése a jóváhagyáshoz funkció használatával megkövetelheti a külső felhasználóktól a folyamatos hozzáférés indoklását, amelyből megtudhatja, hol és hogyan kell még hozzáférniük a bérlőhöz. Emellett engedélyezheti a További  tartalom a felülvizsgáló e-mail-funkcióhoz beállítást, hogy a felhasználók tudják, hogy elveszítik a hozzáférést, ha nem válaszolnak, és ha továbbra is hozzáférésre van szükségük, meg kell indokolni őket. Ha azt szeretné, hogy a  Hozzáférési felülvizsgálatok letiltják és töröljön külső identitásokat, ha nem válaszolnak, vagy érvényes okot adnak a folyamatos hozzáférésre, használhatja a Letiltás és törlés lehetőséget a következő szakaszban leírtak szerint.

![a felülvizsgálat hatókörének korlátozása a vendégfelhasználókra](media/access-reviews-external-users/guest-users-only.png)

A felülvizsgálat befejezése után az **Eredmények** lap áttekintést nyújt az összes külső identitás által adott válaszról. Dönthet úgy, hogy automatikusan alkalmazza az eredményeket, és engedélyezi a hozzáférési felülvizsgálatok letiltását és törlését. Másik lehetőségként át is nézheti a kapott válaszokat, és eldöntheti, hogy el szeretné-e távolítani egy felhasználó hozzáférését, vagy nyomon szeretné követni őket, és további információkat szeretne kapni a döntés meghozatala előtt. Ha egyes felhasználók továbbra is hozzáférnek az Ön által még nem áttekintett erőforrásokhoz, a felderítés részeként használhatja a felülvizsgálatot, és bővítheti a következő felülvizsgálati és igazolási ciklust.

## <a name="disable-and-delete-external-identities-with-azure-ad-access-reviews"></a>Külső identitások letiltása és törlése az Azure AD hozzáférési felülvizsgálatokkal

Amellett, hogy eltávolítja a nemkívánatos külső identitásokat az erőforrásokból, például csoportokból vagy alkalmazásokból, az Azure AD hozzáférési felülvizsgálatok blokkolhatják a külső identitások bejelentkezését a bérlőbe, és 30 nap után törölhetik a külső identitásokat a bérlőből. Ha a **Felhasználó bejelentkezésének 30 napig 30** napig lehetőséget választja, majd eltávolítja a felhasználót a bérlőből, a felülvizsgálat 30 napig "alkalmazás" állapotban marad. Ebben az időszakban az aktuális felülvizsgálat alatt lévő beállítások, eredmények, felülvizsgálók vagy auditnaplók nem hetők meg és nem konfigurálhatók. 

![a befejezési beállítások megadásakor](media/access-reviews-external-users/upon-completion-settings.png)

Új hozzáférési felülvizsgálat létrehozásakor a "Befejezési beállítások"  szakaszban az Elutasított felhasználókra alkalmazandó művelet beállításnál megadhatja a Felhasználók bejelentkezésének **30** napra való letiltását, majd a felhasználó bérlőből való eltávolítását.

Ezzel a beállítással azonosíthatja, letilthatja és törölheti a külső identitásokat az Azure AD-bérlőből. A felülvizsgáló által felülvizsgált és megtagadott külső identitásokat a rendszer letiltja és törli, függetlenül attól, hogy milyen erőforrás-hozzáféréssel vagy csoporttagságokkal rendelkezik. Ezt a beállítást akkor használja a legjobban utolsó lépésként, ha ellenőrzi, hogy az ellenőrzésen áteső külső felhasználók már nem férnek hozzá az erőforrásokhoz, és biztonságosan eltávolíthatók a bérlőből, vagy ha szeretné, hogy eltávolítsa őket, attól függetlenül, hogy állandó hozzáféréssel rendelkezik-e. A "Letiltás és törlés" funkció először blokkolja a külső felhasználót, így nem lesz lehetősége bejelentkezni a bérlőbe és hozzáférni az erőforrásokhoz. Ebben a szakaszban az erőforrás-hozzáférés nincs visszavonva, és ha újra meg akarja újítani a külső felhasználót, újrakonfigurálható a bejelentkezési képességük. További művelet nélkül a letiltott külső identitás 30 nap után törlődik a címtárból, így a fiók és a hozzáférése is törlődik.

## <a name="next-steps"></a>Következő lépések

- [Hozzáférési felülvizsgálatok – Graph API](/graph/api/resources/accessreviewsv2-root?view=graph-rest-beta&preserve-view=true)
- [Jogosultságkezelés – Graph API](/graph/api/resources/entitlementmanagement-root)
