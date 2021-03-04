---
title: Azure Active Directory szolgáltatásfiókok szabályozása
description: Alapelvek és eljárások a szolgáltatási fiókok életciklusának kezeléséhez Azure Active Directoryban.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 3/1/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4311d0acc7c417bf31c71f46e6c25c65312b894d
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102034531"
---
# <a name="governing-azure-ad-service-accounts"></a>Az Azure AD-szolgáltatásfiókok szabályozása

Az Azure Active Directory (Azure AD) háromféle szolgáltatásfiókot támogat: [felügyelt identitásokat](service-accounts-managed-identities.md), [egyszerű szolgáltatásokat](service-accounts-principal.md)és felhasználói fiókokat. A szolgáltatásfiókok automatikus használatra való létrehozásakor engedélyeket kapnak az Azure és az Azure AD erőforrásainak eléréséhez. Az erőforrások közé tartozhatnak Microsoft 365 szolgáltatások, a szolgáltatott szoftverek (SaaS) alkalmazásai, az egyéni alkalmazások, az adatbázisok, a HR-rendszerek stb. Az Azure AD-szolgáltatásfiókok szabályozása azt jelenti, hogy a biztonság és a folytonosság biztosításához a létrehozás, az engedélyek és az életciklus kezelése szükséges.

> [!IMPORTANT] 
> A felhasználói fiókokat nem ajánlott szolgáltatásfiókként használni, mivel azok eleve kevésbé biztonságosak. Ide tartozik az Azure AD-vel szinkronizált helyszíni szolgáltatásfiókok, mivel azok nem az egyszerű szolgáltatásokra vannak konvertálva. Ehelyett a felügyelt identitások vagy egyszerű szolgáltatások használatát javasoljuk. Vegye figyelembe, hogy a feltételes hozzáférési szabályzatok használata jelenleg nem lehetséges az egyszerű szolgáltatásokkal, de a funkció elérhető.


## <a name="plan-your-service-account"></a>Szolgáltatásfiók megtervezése

A szolgáltatásfiók létrehozása vagy egy alkalmazás regisztrálása előtt dokumentálja a szolgáltatásfiók legfontosabb információit. Az információk dokumentálása megkönnyíti a fiók hatékony figyelését és szabályozását. Javasoljuk, hogy az alábbi adatok gyűjtését és nyomon követését a központosított Configuration Management-adatbázisban (CMDB).

| Adatok| Leírás| Részletek |
| - | - | - |
| Tulajdonos| A szolgáltatásfiók felügyeletéhez és figyeléséhez felelős felhasználó vagy csoport.| Hozza létre a tulajdonost a fiók figyeléséhez szükséges engedélyekkel, és hajtsa végre a problémák enyhítésének módját. A probléma enyhítését a tulajdonos vagy egy kérelem útján teheti meg. |
| Cél| A fiók használatának módja.| Rendelje hozzá a szolgáltatásfiókot egy adott szolgáltatáshoz, alkalmazáshoz vagy parancsfájlhoz. Kerülje a többszörös használatú szolgáltatásfiókok létrehozását. |
| Engedélyek (hatókörök)| Előre meghatározott engedélyek.| Dokumentálja a hozzájuk tartozó erőforrásokat és az ezekhez az erőforrásokhoz szükséges engedélyeket. |
| CMDB hivatkozása| Az elérni kívánt erőforrásokra és a szolgáltatásfiók használatára szolgáló parancsfájlokra mutató hivatkozás.| Győződjön meg arról, hogy dokumentálja az erőforrást és a parancsfájl tulajdonosait, hogy kommunikálni tudjon a szükséges felsőbb rétegbeli és alsóbb rétegbeli hatásokkal. |
| Kockázatfelmérés| Kockázat és üzleti hatás, ha a fiókot feltörték.| Ezekkel az információkkal szűkítheti az engedélyek hatókörét, és meghatározhatja, hogy ki férhet hozzá a fiók adataihoz. |
| Felülvizsgálati időszak| Az az ütemterv, amely alapján a szolgáltatásnak a tulajdonosnak kell áttekintenie a szolgáltatásfiókot.| Ezzel az áttekintéssel ellenőrizheti a kommunikációt és az értékeléseket. Dokumentálja, hogy mi történjen, ha a felülvizsgálatot az ütemezett felülvizsgálati időszakot követően megadott idő nem hajtja végre. |
| Élettartama| A fiók várható maximális élettartama.| Ezzel a paranccsal ütemezhet kommunikációt a tulajdonos számára, és végül letilthatja a fiókok törlését. Ahol lehetséges, állítson be egy lejárati dátumot a hitelesítő adatokhoz, ahol a hitelesítő adatok nem vihetők át automatikusan. |
| Name| A fiók szabványosított neve| Hozzon létre egy elnevezési sémát az összes szolgáltatásfiók számára, így könnyen megkeresheti, rendezheti és szűrheti a szolgáltatási fiókokat. |


## <a name="use-the-principle-of-least-privileges"></a>A legalacsonyabb jogosultságok elve
Csak a feladatai végrehajtásához szükséges engedélyeket adja meg a szolgáltatásfiók számára, és így tovább. Ha egy szolgáltatásfiók magas szintű engedélyekkel rendelkezik, például a globális rendszergazdai jogosultsági szinttel, értékelje ki, hogy miért és próbálja meg csökkenteni a szükséges engedélyeket.

A szolgáltatásfiók-jogosultságok kezeléséhez a következő eljárásokat javasoljuk.

**Engedélyek**

* Ne rendeljen beépített szerepköröket a szolgáltatásfiókokhöz. Ehelyett használja a [OAuth2 engedély engedélyezési modelljét Microsoft Graphhoz](/graph/api/resources/oauth2permissiongrant),

* Ha az egyszerű szolgáltatásnak Kiemelt szerepkörrel kell rendelkeznie, érdemes egy adott, a szükséges jogosultságokkal rendelkező [Egyéni szerepkört](https://docs.microsoft.com/azure/active-directory/roles/custom-create) hozzárendelni egy időben kötött módon.

* Az emelt szintű engedélyekkel rendelkező csoportok tagjaiként ne szerepeljenek a szolgáltatásfiókok. 

* A [PowerShell használatával sorolja fel a Kiemelt szerepkörök tagjait](/powershell/module/azuread/get-azureaddirectoryrolemember), például   
`Get-AzureADDirectoryRoleMember`, és szűrő a objektumtípus "egyszerű szolgáltatásnév".

   vagy használja  
`Get-AzureADServicePrincipal | % { Get-AzureADServiceAppRoleAssignment -ObjectId $_ }`

* A [OAuth 2,0 hatókörökkel](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent) korlátozhatja, hogy a szolgáltatásfiók milyen funkciókat érhet el egy erőforráson.
* Az egyszerű szolgáltatások és a felügyelt identitások a OAuth 2,0 hatóköröket használhatják olyan delegált környezetben, amely egy bejelentkezett felhasználót vagy szolgáltatásfiókot használ az alkalmazási környezetben. Az alkalmazás környezetében nincs bejelentkezve.

* Ellenőrizze, hogy megfelelőek-e a hatókörök szolgáltatásfiók-kérelme az erőforrásokhoz. Ha például egy fiók fájlokat kér. ReadWrite. all, értékelje ki, hogy valójában csak file. Read. All fájlra van szüksége. Az engedélyekkel kapcsolatos további információkért lásd: [Microsoft Graph engedélyek referenciája](https://docs.microsoft.com/graph/permissions-reference).

* Győződjön meg arról, hogy az alkalmazás vagy az API fejlesztője megbízhatónak tekinti az erőforrásokhoz való hozzáférést.

**Időtartam**

* A szolgáltatásfiók hitelesítő adatainak (ügyfél titkos kulcsa, tanúsítvány) korlátozása egy várható használati időszakra.

* A rendszeres időközönként áttekinti a szolgáltatásfiókok használatát és célját. Győződjön meg arról, hogy az ellenőrzések a fiók lejárta előtt zajlanak.

Ha a cél, a hatókör és a szükséges engedélyek egyértelmű megismerését választotta, hozzon létre egy szolgáltatásfiókot. 

[Felügyelt identitások létrehozása és használata](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet)

[Egyszerű szolgáltatás létrehozása és használata](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)

Ha lehetséges, használjon felügyelt identitást. Ha nem használhat felügyelt identitást, használjon egyszerű szolgáltatásnevet. Ha nem tud használni egy egyszerű szolgáltatásnevet, és csak ezután használ egy Azure AD-felhasználói fiókot.

 

## <a name="build-a-lifecycle-process"></a>Életciklus-folyamat létrehozása

A szolgáltatási fiók életciklusának kezelése a tervezéssel kezdődik, és a végleges törléssel végződik. 

Ez a cikk már tárgyalta a tervezési és létrehozási részt. Emellett figyelnie, ellenőriznie kell az engedélyeket, meg kell határoznia a fiók folyamatos használatát, és végül fel kell vennie a fiókot.

### <a name="monitor-service-accounts"></a>Szolgáltatásfiókok figyelése

Proaktív módon figyelheti a szolgáltatási fiókokat annak biztosítására, hogy a szolgáltatásfiók használati mintái a kívánt mintákat tükrözzék, és hogy a szolgáltatásfiók továbbra is aktívan használatban legyen.

**A szolgáltatásfiók-bejelentkezések összegyűjtése és figyelése az alábbi módszerek egyikével:**

* Az Azure ad Sign-In-naplók használata az Azure AD-portálon.

* Az Azure AD Sign-In naplók exportálása az [Azure Storage](https://docs.microsoft.com/azure/storage/)-ba, az [Azure Event Hubsba](https://docs.microsoft.com/azure/event-hubs/)vagy a [Azure monitorba](https://docs.microsoft.com/azure/azure-monitor/logs/data-platform-logs).


![A szolgáltatás egyszerű bejelentkezési képernyőjét bemutató képernyőfelvétel](./media/securing-service-accounts/service-accounts-govern-azure-1.png)

**A Sign-In-naplókban megjelenő intelligencia a következőket tartalmazza:**

* Vannak olyan szolgáltatásfiókok, amelyek már nem jelentkeznek be a bérlőbe?

* Változnak a szolgáltatási fiókok bejelentkezési mintái?

Javasoljuk, hogy exportálja az Azure AD bejelentkezési naplóit, és importálja azokat a meglévő biztonsági információk és események kezelése (SIEM) eszközeibe, például az Azure Sentinelbe. A SIEM használatával riasztásokat és irányítópultokat hozhat létre.

### <a name="review-service-account-permissions"></a>Szolgáltatásfiók engedélyeinek áttekintése

Rendszeresen tekintse át a szolgáltatásfiókok által biztosított engedélyeket és hatóköröket, és ellenőrizze, hogy a felhasználók csökkenthetők-e.

* A [PowerShell](/powershell/module/azuread/get-azureadserviceprincipaloauth2permissiongrant) használatával [automatizálhatja a](https://gist.github.com/psignoret/41793f8c6211d2df5051d77ca3728c09) szolgáltatási fiók belefoglalását, és dokumentálhatja azokat a hatóköröket, amelyekhez beleegyezik a hozzáférés.

* A PowerShell használatával [tekintse át a meglévő egyszerű szolgáltatásnév hitelesítő adatait](https://github.com/AzureAD/AzureADAssessment) , és ellenőrizze azok érvényességét.

* Ne állítson be egyszerű szolgáltatásnév hitelesítő adatait a "soha nem jár le" értékre.

* Ha lehetséges, használja az Azure kulcstartóban tárolt tanúsítványokat vagy hitelesítő adatokat.

A Microsoft ingyenes PowerShell-mintája az egyszerű szolgáltatásnév OAuth2-támogatásait és a hitelesítő adatokat gyűjti, a vesszővel tagolt adatfájlokban (CSV), valamint az adatok értelmezéséhez és használatához Power BI minta irányítópulton rögzíti azokat. További információ [: AzureAD/AzureADAssessment: az Azure ad-bérlő állapotának és konfigurációjának értékelésére szolgáló eszköz (GitHub.com)](https://github.com/AzureAD/AzureADAssessment)

### <a name="recertify-service-account-use"></a>Szolgáltatásfiók-használat újbóli hitelesítése

Hozzon létre egy felülvizsgálati folyamatot annak biztosítására, hogy a szolgáltatásfiókok rendszeres időközönként, a tulajdonosuk, illetve a biztonság vagy az IT-csoport rendszeresen felül legyenek értékelve. 

**A folyamatnak a következőket kell tartalmaznia:**

* Az egyes szolgáltatásfiókok felülvizsgálati ciklusának megállapítása (dokumentálni kell a CMDB).

* A tulajdonossal és a biztonsággal, vagy az informatikai csapattal való kommunikáció a felülvizsgálatok megkezdése előtt.

* A figyelmeztetési kommunikáció időzítése és tartalma, ha a felülvizsgálat kimarad.

* Útmutatás a mi a teendő, ha a tulajdonosok nem vizsgálják át vagy válaszolnak. Előfordulhat például, hogy le kívánja tiltani (de nem törli) a fiókot, amíg a felülvizsgálat be nem fejeződik.

* Útmutatás a felsőbb rétegbeli és az alsóbb rétegbeli Függőségek meghatározásához, valamint az egyéb erőforrás-tulajdonosok bármilyen hatásának bejelentéséhez.

**A felülvizsgálatnak tartalmaznia kell a tulajdonost és az informatikai partnert, amely igazolja, hogy:**

* A fiók továbbra is szükséges.

* A fiókhoz megadott engedélyek megfelelőek és szükségesek, vagy módosításra van szükség.

* A fiókhoz és a hitelesítő adataihoz való hozzáférés ellenőrzése megtörténik.

* A fiók által használt hitelesítő adatok arra a kockázatra vonatkoznak, hogy a fiók értékelése megtörtént (a hitelesítő adatok típusa és a hitelesítő adatok élettartama is szükséges)

* A fiók kockázati pontozása nem változott az utolsó újraminősítés óta

* A fiók várható élettartamának és a következő érvényességi dátumnak a frissítése.

### <a name="deprovision-service-accounts"></a>Szolgáltatásfiókok kiépítése

* * Szolgáltatásfiókok kiépítése a következő esetekben: * * * *

* A szolgáltatáshoz létrehozott parancsfájl vagy alkalmazás ki lett vonva.

* A parancsfájlban vagy alkalmazásban található függvényt (például egy adott erőforráshoz való hozzáféréshez) használja a rendszer.


* A szolgáltatási fiókot egy másik szolgáltatásfiók váltja fel.

* A hitelesítő adatok lejártak, vagy ha a fiók nem működik, és nincs semmilyen panasz.

**A megszüntetési folyamatoknak a következő feladatokat kell tartalmazniuk.**

1. Ha a társított alkalmazást vagy parancsfájlt kiépítik, [Figyelje](../reports-monitoring/concept-all-sign-ins.md#sign-ins-report) a szolgáltatás fiókjának bejelentkezési és erőforrás-hozzáférését.

   * Ha a fiók továbbra is aktív, határozza meg, hogyan használja a rendszer a következő lépések megkezdése előtt.
 
2. Ha ez egy felügyelt szolgáltatás identitása, tiltsa le a szolgáltatásfiókot a bejelentkezéshez, de ne távolítsa el a címtárból.

3. A szerepkör-hozzárendelések visszavonása és a OAuth2 beleegyezési támogatásai.

4. Egy meghatározott időszak után, és a tulajdonosokra vonatkozó figyelmeztetéssel, törölje a szolgáltatásfiókot a címtárból.

## <a name="next-steps"></a>Következő lépések
Az Azure-szolgáltatásfiókok biztonságossá tételével kapcsolatos további információkért lásd:

[Az Azure Service-fiókok bemutatása](service-accounts-introduction-azure.md)

[Felügyelt identitások biztonságossá tétele](service-accounts-managed-identities.md)

[A szolgáltatási alapelvek védelme](service-accounts-principal.md)




 

 
