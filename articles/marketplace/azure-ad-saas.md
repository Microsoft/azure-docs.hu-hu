---
title: Azure Active Directory és az SaaS-ajánlatok a kereskedelmi piactéren
description: Ismerje meg, hogy a Azure Active Directory hogyan működik a Microsoft kereskedelmi piactéren a transactd SaaS-ajánlatokkal.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/04/2020
ms.openlocfilehash: 674f267d3d99dd22c1ae06b6d32587761d5983ce
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93124917"
---
# <a name="azure-ad-and-transactable-saas-offers-in-the-commercial-marketplace"></a>Az Azure AD és a transactd SaaS-ajánlatok a kereskedelmi piactéren

A Microsoft felhőalapú identitás-és hozzáférés-kezelési szolgáltatása, [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure ad) segít a felhasználóknak bejelentkezni, és hozzáférni a belső és külső erőforrásokhoz. A Microsoft kereskedelmi piactéren az Azure AD lehetővé teszi, hogy a transacter SaaS könnyebb és biztonságosabb legyen mindenki számára, beleértve a közzétevőket, a vásárlókat és a felhasználókat. Az Azure AD-vel a kiadók automatizálják a felhasználók kiépített szolgáltatásként (SaaS) való üzembe helyezését, a vásárlók pedig maguk kezelhetik ezeket a kiosztott felhasználókat. 

Emellett az [Azure ad egyszeri bejelentkezés](../active-directory/manage-apps/what-is-single-sign-on.md) (SSO) biztonsági és kényelmi funkciókat biztosít, amikor a felhasználók bejelentkeznek az Azure ad-alkalmazásokba. A gyorsabb engagement és az optimalizált tapasztalatok is ösztönzik a vásárlói és a felhasználói bizalmat a kiadói SaaS-alkalmazással való első interakció során. Ez pozitív benyomást jelent, amely kiépíti a láthatóságot, és elősegíti az ismétlődő üzleti tevékenységet.

Az ebben a cikkben ismertetett útmutatást követve az SaaS-ajánlat a kereskedelmi piactéren való hitelesítéséhez nyújt segítséget. A minősítéssel kapcsolatos további információkért olvassa el a [kereskedelmi piactér részletes tanúsítási szabályzatait](/legal/marketplace/certification-policies#100-general), beleértve az SaaS-ra [jellemzőket](/legal/marketplace/certification-policies#1000-software-as-a-service-saas)is.

## <a name="before-you-begin"></a>Előkészületek

Ha az SaaS-ajánlatot a partner Centerben [hozza létre](./create-new-saas-offer.md) , az ajánlati listán megjelenő, meghatározott listaelemek közül választhat. Ön dönti el, hogyan történik az ajánlat a kereskedelmi piactéren való lebonyolításában. A Microsoft által eladott ajánlatokat felhívható ajánlatoknak nevezzük. Az ügyfelet az Ön nevében számlázjuk minden visszagörgethető ajánlatért. Ha úgy dönt, hogy a Microsofton keresztül értékesít, és az Ön nevében üzemelteti az Egyesült államokbeli tranzakciókat (az **Igen** lehetőséggel), akkor egy visszagörgethető ajánlat létrehozását választotta, és ez a cikk Önnek szól. Javasoljuk, hogy teljes egészében olvassa el.

Ha úgy dönt, hogy csak a kereskedelmi Piactéren keresztül sorolja fel az ajánlatát, és a tranzakciókat egymástól függetlenül dolgozza fel (a **No** lehetőséggel), három lehetősége van arra, hogy a potenciális ügyfelek hogyan férhessenek hozzá az ajánlathoz: azonnali letöltés (ingyenes), ingyenes próbaverzió és kapcsolatfelvétel. Ha a letöltés **most (ingyenes)** vagy az **ingyenes próbaverzió** lehetőséget választja, akkor ez a cikk nem áll az Ön számára. További információkért lásd: [az ingyenes vagy próbaverziós SaaS-ajánlat kezdőlapjának létrehozása a kereskedelmi piactéren](./azure-ad-free-or-trial-landing-page.md) . Ha a **Kapcsolatfelvétel** lehetőséget választja, nincsenek közvetlen közzétevői felelősségek. Folytassa az ajánlat létrehozását a partner Centerben.

## <a name="how-azure-ad-works-with-the-commercial-marketplace-for-saas-offers"></a>Hogyan működik az Azure AD a kereskedelmi piactéren az SaaS-ajánlatok számára

Az Azure AD a kereskedelmi Piactéri megoldások zökkenőmentes megvásárlását, teljesítését és felügyeletét teszi lehetővé. Az 1. ábra azt mutatja, hogy a közzétevő, a vevő és a felhasználó hogyan kommunikál az előfizetés megvásárlásával és aktiválásával. Azt is bemutatja, hogy az ügyfelek hogyan használják és kezelhetik a kereskedelmi piactéren elérhető SaaS-alkalmazásokat. Ebben az ábrán a vásárló a SaaS-alkalmazás azon felhasználója, aki a kereskedelmi piactéren vásárolja meg a vásárlást.

Ahogy az 1. ábrán is látható, amikor a vevő kiválasztja az ajánlatot, elindítanak egy munkafolyamatok láncát, amely tartalmazza a vásárlást, az előfizetést és a felhasználói felügyeletet. Ezen a láncon belül Ön, ahogy a kiadó felelős az egyes követelményekért, a Microsoft a kulcsfontosságú pontokat támogató támogatást nyújt.

***1. ábra: az Azure AD for SaaS-ajánlatok használata a kereskedelmi piactéren***

:::image type="content" source="./media/azure-ad-saas/azure-ad-saas-flow.png" alt-text="Bemutatja a vásárlási felügyeletet, az előfizetések kezelését és a nem kötelező felhasználói felügyeleti folyamat lépéseit.":::

A következő szakaszokban részletesen ismertetjük az egyes folyamatok lépéseinek követelményeit.

## <a name="process-steps-for-purchase-management"></a>A vásárlási felügyelet folyamatának lépései

Ez az ábra a vásárlás felügyeletének négy folyamat lépését mutatja be.

:::image type="content" source="./media/azure-ad-saas/azure-ad-saas-flow-1-4.png" alt-text="A vásárlások kezelésének négy folyamatának lépéseit mutatja be.":::

Ez a táblázat a vásárlás felügyeleti folyamatának lépéseit ismerteti.

| Folyamat lépése | Közzétevői művelet | A közzétevők számára ajánlott vagy kötelező |
| ------------ | ------------- | ------------- |
| 1. a vásárló bejelentkezik a kereskedelmi piactéren az Azure ID-identitással, és kiválasztja az SaaS-ajánlatot. | Nincs szükség közzétevői műveletre. | Nem alkalmazható |
| 2. a vásárlást követően a vevő kiválasztja a **fiók konfigurálása** az Azure piactéren vagy a AppSource **konfigurálása** lehetőséget, amely a vásárlót a kiadó kezdőlapjának adja át ehhez az ajánlathoz. A vásárlónak képesnek kell lennie arra, hogy bejelentkezzen a közzétevő SaaS-alkalmazásba az Azure AD SSO-val, és csak olyan minimális hozzájárulást kérjen, amely nem igényli az Azure AD-rendszergazda jóváhagyását. | Tervezze meg az [ajánlat kezdőlapját](azure-ad-transactable-saas-landing-page.md) , hogy az Azure ad-vagy Microsoft-fiók-(MSA-) identitással rendelkező felhasználót kapjon, és megkönnyíti a szükséges további kiépítést vagy beállítást. | Kötelező |
| 3. a közzétevő a SaaS-teljesítési API-ból vásárolja meg a vásárlás részleteit. | A Kezdőlap alkalmazás-AZONOSÍTÓjában létrehozott [hozzáférési jogkivonat](./partner-center-portal/pc-saas-registration.md) használatával [hívja meg a feloldási végpontot](./partner-center-portal/pc-saas-fulfillment-api-v2.md#resolve-a-purchased-subscription) a vásárlással kapcsolatos részletek lekéréséhez. | Kötelező |
| 4. az Azure AD-n és a Microsoft Graph API-n keresztül a kiadó összegyűjti a vállalat és a felhasználó adatait a közzétevő SaaS-alkalmazásban való kiépítéséhez.  | Az Azure AD felhasználói jogkivonat lebontása a név és az e-mail kereséséhez, vagy [a Microsoft Graph API meghívásához](/graph/use-the-api) , valamint a delegált engedélyek használata a bejelentkezett felhasználó [adatainak lekéréséhez](/graph/api/user-get) . | Kötelező |
||||

## <a name="process-steps-for-subscription-management"></a>Az előfizetés-kezelés folyamatának lépései

Ez az ábra az előfizetés-kezelés két folyamatának lépéseit mutatja be.

:::image type="content" source="./media/azure-ad-saas/azure-ad-saas-flow-5-6.png" alt-text="Az előfizetés-kezelés két folyamatának lépéseit mutatja be.":::

Ez a táblázat az előfizetés-kezelési folyamat lépéseinek részleteit ismerteti.

| Folyamat lépése | Közzétevői művelet | A közzétevők számára ajánlott vagy kötelező |
| ------------ | ------------- | ------------- |
| 5. a közzétevő a SaaS-megvalósítási API-n keresztül kezeli az előfizetést az SaaS-alkalmazáshoz. | Kezeli az előfizetés változásait és az egyéb felügyeleti feladatokat a [SaaS-megvalósítási API](./partner-center-portal/pc-saas-fulfillment-api-v2.md)-kon keresztül.<br><br>Ennek a lépésnek a 3. lépésében leírtak szerint hozzáférési jogkivonatra van szüksége. | Kötelező |
| 6. a mért díjszabás használatakor a kiadó használati eseményeket bocsát ki a mérési szolgáltatás API-hoz. | Ha az SaaS-alkalmazás használati alapú számlázást használ, a használati értesítéseket a [Marketplace-mérési szolgáltatás API-jai](./partner-center-portal/marketplace-metering-service-apis.md)segítségével teheti meg.<br><br>Ehhez a lépéshez a 3. lépésben leírtak szerint hozzáférési jogkivonat szükséges. | Méréshez szükséges |
||||

## <a name="process-steps-for-user-management"></a>A felhasználói felügyelet folyamatának lépései

Ez az ábra a felhasználói felügyelet három folyamatának lépéseit mutatja be.

:::image type="content" source="./media/azure-ad-saas/azure-ad-saas-flow-7-9.png" alt-text="A felhasználói felügyelet három választható folyamatának lépéseit mutatja be.":::

A 7 – 9. lépést feldolgozhatja a felhasználói felügyeleti folyamatok opcionális lépéseivel. Az Azure AD egyszeri bejelentkezést (SSO) támogató kiadók számára további előnyöket biztosítanak. Ez a táblázat a felhasználói felügyeleti folyamat lépéseinek részleteit ismerteti.

| Folyamat lépése | Közzétevői művelet | A közzétevők számára ajánlott vagy kötelező |
| ------------ | ------------- | ------------- |
| 7. az Azure AD-rendszergazdák az Azure AD-n keresztül igény szerint kezelhetik a felhasználók és csoportok hozzáférését. | Ha az Azure AD SSO felhasználói számára van beállítva (9. lépés), nincs szükség közzétevői műveletre. | Nem alkalmazható |
| 8. az Azure AD-kiépítési szolgáltatás kommunikál az Azure AD és a közzétevő SaaS-alkalmazásának változásaival. | [Hozzon létre egy scim-végpontot](../active-directory/app-provisioning/use-scim-to-provision-users-and-groups.md) a frissítések Azure ad-től való fogadásához, mivel a felhasználók hozzáadása és eltávolítása megtörtént. | Ajánlott |
| 9. az alkalmazás jogosultságának és üzembe helyezésének engedélyezése után a vásárló vállalatának felhasználói az Azure AD SSO használatával jelentkezhetnek be a közzétevő SaaS-alkalmazásba. | Az [Azure ad SSO használatával](../active-directory/manage-apps/what-is-single-sign-on.md) engedélyezheti, hogy a felhasználók egyszer jelentkezzenek be egy fiókkal a közzétevő SaaS-alkalmazásával. | Ajánlott |
||||

## <a name="next-steps"></a>Következő lépések

- [A kereskedelmi piactéren felépítheti a transacter SaaS-ajánlat kezdőlapját](azure-ad-transactable-saas-landing-page.md)
- [Az ingyenes vagy próbaverziós SaaS-ajánlat kezdőlapjának összeállítása a kereskedelmi piactéren](azure-ad-free-or-trial-landing-page.md)
- [SaaS-ajánlat létrehozása a kereskedelmi piactéren](create-new-saas-offer.md)