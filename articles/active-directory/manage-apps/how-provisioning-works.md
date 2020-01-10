---
title: Az Azure AD-kiépítés működésének megismerése | Microsoft Docs
description: Az Azure AD-kiépítés működésének megismerése
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 910317201275ba1598ed3e4d89815542b88fb108
ms.sourcegitcommit: 02160a2c64a5b8cb2fb661a087db5c2b4815ec04
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/07/2020
ms.locfileid: "75719970"
---
# <a name="how-provisioning-works"></a>Az üzembe helyezés menete

Az automatikus kiépítés olyan felhasználói identitások és szerepkörök létrehozására utal, amelyekhez a felhasználóknak hozzá kell férniük. A felhasználói identitások létrehozása mellett az automatikus kiépítés a felhasználói identitások karbantartását és eltávolítását is magában foglalja az állapot vagy a szerepkörök módosításakor. A központi telepítés megkezdése előtt tekintse át ezt a cikket, amelyből megtudhatja, hogyan működik az Azure AD-alkalmazás, és milyen javaslatokat kaphat. 

Az **Azure ad-kiépítési szolgáltatás** a felhasználókat az SaaS-alkalmazásokhoz és más rendszerekhez is kiépíti az alkalmazás gyártójától származó tartományok közötti IDENTITÁSKEZELÉS (SCIM) 2,0 felhasználói felügyeleti API-végponthoz való csatlakozással. Ez a SCIM-végpont lehetővé teszi, hogy az Azure AD programozott módon hozza létre, frissítse és távolítsa el a felhasználókat. A kiválasztott alkalmazások esetében a kiépítési szolgáltatás további, identitással kapcsolatos objektumokat, például csoportokat és szerepköröket is létrehozhat, frissíthet és eltávolíthat. Az Azure AD és az alkalmazás közötti üzembe helyezéshez használt csatorna HTTPS SSL-titkosítás használatával van titkosítva.


![Azure AD-létesítési szolgáltatás](./media/user-provisioning/provisioning0.PNG)
*1. ábra: az Azure ad-kiépítési szolgáltatás*

![kimenő felhasználó kiépítési munkafolyamata](./media/user-provisioning/provisioning1.PNG)
*2. ábra: "kimenő" felhasználó kiépítési munkafolyamata az Azure ad-ből a népszerű SaaS-alkalmazásokhoz*

![bejövő felhasználó kiépítési munkafolyamata](./media/user-provisioning/provisioning2.PNG)
*3. ábra: "bejövő" felhasználó kiépítési munkafolyamata a népszerű humántőke-felügyeleti (HCM) alkalmazásokból a Azure Active Directory és a Windows serverre Active Directory*

## <a name="provisioning-using-scim-20"></a>Kiépítés a SCIM 2,0 használatával

Az Azure AD-kiépítési szolgáltatás a [SCIM 2,0 protokollt](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/bg-p/IdentityStandards) használja az automatikus kiépítés számára. A szolgáltatás csatlakozik az alkalmazáshoz a SCIM-végponthoz, és a felhasználók és csoportok kiépítése és kiépítése automatizálható a SCIM felhasználói objektumok sémája és a REST API-k használatával. Az Azure AD-katalógus legtöbb alkalmazásához SCIM-alapú létesítési összekötőt biztosítunk. Az Azure AD-alkalmazások létrehozásakor a fejlesztők a SCIM 2,0 User Management API-val olyan SCIM-végpontot hozhatnak létre, amely integrálja az Azure AD-t az üzembe helyezéshez. Részletekért lásd: [scim-végpont létrehozása és a felhasználók üzembe](use-scim-to-provision-users-and-groups.md)helyezésének konfigurálása.

Ha olyan alkalmazáshoz szeretne automatikus Azure AD-kiépítési összekötőt igényelni, amely még nem rendelkezik ilyennel, töltsön ki egy [Azure Active Directory alkalmazás-kérelmet](https://aka.ms/aadapprequest).

## <a name="authorization"></a>Engedélyezés

A hitelesítő adatok szükségesek ahhoz, hogy az Azure AD csatlakozhasson az alkalmazás felhasználói felügyeleti API-hoz. Egy alkalmazás automatikus felhasználó-kiépítési beállításakor érvényes hitelesítő adatokat kell megadnia. Az alkalmazáshoz tartozó hitelesítő adatokat és követelményeket az alkalmazásra vonatkozó oktatóanyagra utalva találja meg. A Azure Portalon tesztelheti a hitelesítő adatokat azáltal, hogy az Azure AD megpróbál csatlakozni az alkalmazás kiépítési alkalmazásához a megadott hitelesítő adatok használatával.

Ha SAML-alapú egyszeri bejelentkezést is konfigurál az alkalmazáshoz, az Azure AD belső, alkalmazáson belüli tárolási korlátja 1024 bájt. Ez a korlát magában foglalja az alkalmazás egyetlen példányához társított összes tanúsítványt, titkos jogkivonatot, hitelesítő adatokat és kapcsolódó konfigurációs adatot (más néven az Azure AD-beli egyszerű szolgáltatásnév). Ha az SAML-alapú egyszeri bejelentkezés konfigurálva van, az SAML-tokenek aláírására használt tanúsítvány gyakran a terület 50%-át használja fel. A felhasználók üzembe helyezése során megadott további elemek (titkos jogkivonatok, URI-k, értesítő e-mail-címek, felhasználónevek és jelszavak) meghaladják a tárolási korlátot. További információ: a [rendszergazdai hitelesítő adatok mentése a felhasználó üzembe helyezésének beállítása során](application-provisioning-config-problem-storage-limit.md).

## <a name="mapping-attributes"></a>Leképezési attribútumok

Ha engedélyezi a felhasználók kiosztását egy külső SaaS-alkalmazáshoz, a Azure Portal attribútum-hozzárendeléseken keresztül vezérli az attribútum értékeit. A leképezések határozzák meg az Azure AD és a célalkalmazás közötti felhasználói attribútumokat, amikor a felhasználói fiókokat kiépítik vagy frissítik.

Az Azure AD felhasználói objektumai és az egyes SaaS-alkalmazások felhasználói objektumai között előre konfigurált attribútumok és attribútumok vannak megadva. Egyes alkalmazások más típusú objektumokat is kezelhetnek a felhasználók, például a csoportok mellett.

A kiépítés beállításakor fontos, hogy áttekintse és konfigurálja azokat az attribútum-hozzárendeléseket és munkafolyamatokat, amelyek meghatározzák, hogy az Azure AD mely felhasználói (vagy csoport-) tulajdonságait kell az alkalmazásnak megadnia. Tekintse át és konfigurálja a megfelelő tulajdonságot (az**attribútumot használó objektumok egyeztetése**), amely a felhasználók és csoportok egyedi azonosítására és a két rendszer közötti egyeztetésére szolgál.

Az alapértelmezett attribútum-hozzárendelések testreszabhatók az üzleti igényeknek megfelelően. Így módosíthatja vagy törölheti a meglévő attribútum-hozzárendeléseket, illetve létrehozhat új attribútum-hozzárendeléseket is. Részletekért lásd: [felhasználói üzembe helyezési attribútumok testreszabása – SaaS-alkalmazások leképezése](customize-application-attributes.md).

Ha egy SaaS-alkalmazásra konfigurálja az üzembe helyezést, a megadható attribútumok egyike egy kifejezés-hozzárendelés. Ezen hozzárendelések esetében olyan parancsfájl-szerű kifejezést kell írnia, amely lehetővé teszi a felhasználói adatai átalakítását olyan formátumokra, amelyek az SaaS-alkalmazás számára elfogadhatóak. Részletekért lásd: [kifejezések írása attribútum-hozzárendelésekhez](functions-for-customizing-application-data.md).

## <a name="scoping"></a>Tartalmazó 
### <a name="assignment-based-scoping"></a>Hozzárendelés-alapú hatókör

Az Azure AD-ből egy SaaS-alkalmazásba való kimenő kiépítés esetén a felhasználók [vagy csoportok hozzárendeléseire](assign-user-or-group-access-portal.md) támaszkodva a leggyakoribb módszer annak meghatározására, hogy mely felhasználók tartoznak a kiépítés hatálya alá. Mivel a felhasználói hozzárendelések az egyszeri bejelentkezés engedélyezéséhez is használatosak, ugyanezt a módszert használhatja a hozzáférés és a kiépítés kezelésére is. A hozzárendelés-alapú hatókör nem vonatkozik a bejövő kiépítési forgatókönyvekre, például a munkanapokra és a SuccessFactors.

* **Csoportok.** A prémium szintű Azure AD-licenccel a csoportok használatával rendelhet hozzá hozzáférést egy SaaS-alkalmazáshoz. Ezt követően, ha a kiépítési hatókör **csak a hozzárendelt felhasználók és csoportok szinkronizálására**van beállítva, az Azure ad-kiépítési szolgáltatás a felhasználókat attól függően fogja kiépíteni vagy kiépíteni, hogy az alkalmazáshoz rendelt csoport tagjai-e. Maga a csoport objektum sincs kiépítve, kivéve, ha az alkalmazás támogatja a csoportok objektumait.

* **Dinamikus csoportok.** Az Azure AD-beli felhasználó-kiépítési szolgáltatás a [dinamikus csoportokba](../users-groups-roles/groups-create-rule.md)tartozó felhasználókat tudja olvasni és kiépíteni. Tartsa szem előtt ezeket a figyelmeztetéseket és javaslatokat:

  * A dinamikus csoportok befolyásolhatják a végpontok közötti kiépítés teljesítményét az Azure AD-ből az SaaS-alkalmazásokba.

  * A dinamikus csoportokban lévő felhasználók egy SaaS-alkalmazásban való kiépítésének vagy kiépítésének gyorsasága attól függ, hogy a dinamikus csoport milyen gyorsan tudja kiértékelni a tagsági változásokat. További információ a dinamikus csoportok feldolgozási állapotának vizsgálatáról: [tagsági szabály feldolgozási állapotának](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule)megtekintése.

  * Ha a felhasználó elveszti a dinamikus csoport tagságát, akkor az egy kiépítési eseménynek számít. Vegye figyelembe ezt a forgatókönyvet a dinamikus csoportok szabályainak létrehozásakor.

* **Beágyazott csoportok.** Az Azure AD-beli felhasználói kiépítési szolgáltatás nem tudja beolvasni vagy kiépíteni a felhasználókat a beágyazott csoportokban. A szolgáltatás csak olyan felhasználókat tud olvasni és kiépíteni, akik közvetlenül tagjai egy explicit módon hozzárendelt csoportnak. Ez a "csoportos hozzárendelések alkalmazások számára" korlátozás az egyszeri bejelentkezést is befolyásolja (lásd: [csoport használata az SaaS-alkalmazásokhoz való hozzáférés kezeléséhez](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-saasapps)). Ehelyett közvetlenül a kiépíteni kívánt felhasználókat tartalmazó csoportokban vagy más [hatókörben](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) kell kiosztania.

### <a name="attribute-based-scoping"></a>Attribútum-alapú hatókör 

A hatókör-szűrők használatával olyan attribútum-alapú szabályokat határozhat meg, amelyek meghatározzák, hogy mely felhasználók legyenek kiépítve egy alkalmazáshoz. Ezt a módszert általában a HCM-alkalmazásokból az Azure AD-be és a Active Directoryra való bejövő kiépítés esetén használják. A hatóköri szűrők az egyes Azure AD-beli felhasználói létesítési összekötők attribútum-hozzárendeléseinek részeként vannak konfigurálva. Az attribútum-alapú hatóköri szűrők konfigurálásával kapcsolatos részletekért lásd: [attribútum-alapú alkalmazás kiépítés hatóköri szűrőkkel](define-conditional-rules-for-provisioning-user-accounts.md).

### <a name="b2b-guest-users"></a>B2B (vendég) felhasználók

Az Azure AD-beli felhasználói üzembe helyezési szolgáltatás használatával az Azure AD-ben elérhetővé teheti a VÁLLALATKÖZI (vagy vendég) felhasználókat az SaaS-alkalmazásokhoz. Ahhoz azonban, hogy a B2B-felhasználók az Azure AD-vel jelentkezzenek be az SaaS-alkalmazásba, az SaaS-alkalmazásnak adott módon konfigurált SAML-alapú egyszeri bejelentkezési képességgel kell rendelkeznie. További információ az SaaS-alkalmazások a B2B-felhasználóktól való támogatásához való konfigurálásáról: [SaaS-alkalmazások konfigurálása B2B-együttműködéshez]( https://docs.microsoft.com/azure/active-directory/b2b/configure-saas-apps).

## <a name="provisioning-cycles-initial-and-incremental"></a>Kiépítési ciklusok: kezdeti és növekményes

Ha az Azure AD a forrásoldali rendszer, a kiépítési szolgáltatás az [Azure ad Graph API különbözeti lekérdezési funkciójával](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query) figyeli a felhasználókat és a csoportokat. A kiépítési szolgáltatás kezdeti ciklust futtat a forrásrendszer és a célként megadott rendszeren, majd az időszakos növekményes ciklusok után.

### <a name="initial-cycle"></a>Kezdeti ciklus

A kiépítési szolgáltatás indításakor az első ciklus a következőket eredményezi:

1. Az összes felhasználó és csoport lekérdezése a forrásoldali rendszerből, az [attribútum-hozzárendelésekben](customize-application-attributes.md)definiált összes attribútum beolvasása.

2. A visszaadott felhasználók és csoportok szűrése bármely konfigurált [hozzárendelés](assign-user-or-group-access-portal.md) vagy [attribútum-alapú hatókör-szűrő](define-conditional-rules-for-provisioning-user-accounts.md)használatával.

3. Ha egy felhasználó hozzá van rendelve vagy hatókörben van az üzembe helyezéshez, a szolgáltatás lekérdezi a megfelelő felhasználót a megadott [egyező attribútumokkal](customize-application-attributes.md#understanding-attribute-mapping-properties). Példa: Ha a userPrincipal neve megegyezik a megfelelő attribútummal, és leképezi a felhasználónevet a célként megadott rendszeren, a kiépítési szolgáltatás lekérdezi a célként megadott rendszerrendszert a forrásrendszer userPrincipal-értékeinek megfelelő felhasználónevek számára.

4. Ha nem található egyező felhasználó a célszámítógépen, a rendszer a forrásrendszer által visszaadott attribútumok használatával hozza létre. A felhasználói fiók létrehozása után a kiépítési szolgáltatás észleli és gyorsítótárazza az új felhasználóhoz tartozó rendszer AZONOSÍTÓját. Ez az azonosító az adott felhasználóra vonatkozó összes jövőbeli művelet futtatására szolgál.

5. Ha a rendszer megfelelő felhasználót talál, az a forrásrendszer által megadott attribútumok használatával frissül. A felhasználói fiók egyeztetése után a kiépítési szolgáltatás észleli és gyorsítótárazza az új felhasználóhoz tartozó rendszer AZONOSÍTÓját. Ez az azonosító az adott felhasználóra vonatkozó összes jövőbeli művelet futtatására szolgál.

6. Ha az attribútum-hozzárendelések "Reference" attribútumokat tartalmaznak, a szolgáltatás további frissítéseket hoz létre a célszámítógépen a hivatkozott objektumok létrehozásához és összekapcsolásához. Előfordulhat például, hogy egy felhasználó "Manager" attribútummal rendelkezik a célszámítógépen, amely egy másik, a célszámítógépen létrehozott felhasználóhoz van társítva.

7. A kezdeti ciklus végén maradjon egy vízjelet, amely a későbbi növekményes ciklusok kiindulási pontját adja meg.

Egyes alkalmazások, például a ServiceNow, a G Suite és a Box támogatása nem csupán a felhasználókat, hanem a csoportok és a tagjaik létesítését is támogatják. Ezekben az esetekben, ha a csoportok kiosztása engedélyezve van a [leképezésekben](customize-application-attributes.md), a kiépítési szolgáltatás szinkronizálja a felhasználókat és a csoportokat, majd később szinkronizálja a csoporttagságok körét.

### <a name="incremental-cycles"></a>Növekményes ciklusok

A kezdeti ciklus után az összes többi ciklus a következő lesz:

1. A forrásrendszer lekérdezése minden olyan felhasználó és csoport esetében, amelyet az utolsó vízjel tárolása óta frissítettek.

2. A visszaadott felhasználók és csoportok szűrése bármely konfigurált [hozzárendelés](assign-user-or-group-access-portal.md) vagy [attribútum-alapú hatókör-szűrő](define-conditional-rules-for-provisioning-user-accounts.md)használatával.

3. Ha egy felhasználó hozzá van rendelve vagy hatókörben van az üzembe helyezéshez, a szolgáltatás lekérdezi a megfelelő felhasználót a megadott [egyező attribútumokkal](customize-application-attributes.md#understanding-attribute-mapping-properties).

4. Ha nem található egyező felhasználó a célszámítógépen, a rendszer a forrásrendszer által visszaadott attribútumok használatával hozza létre. A felhasználói fiók létrehozása után a kiépítési szolgáltatás észleli és gyorsítótárazza az új felhasználóhoz tartozó rendszer AZONOSÍTÓját. Ez az azonosító az adott felhasználóra vonatkozó összes jövőbeli művelet futtatására szolgál.

5. Ha a rendszer megfelelő felhasználót talál, az a forrásrendszer által megadott attribútumok használatával frissül. Ha egy újonnan hozzárendelt fiók, amely megfelel, a kiépítési szolgáltatás észleli és gyorsítótárazza az új felhasználóhoz tartozó rendszer AZONOSÍTÓját. Ez az azonosító az adott felhasználóra vonatkozó összes jövőbeli művelet futtatására szolgál.

6. Ha az attribútum-hozzárendelések "Reference" attribútumokat tartalmaznak, a szolgáltatás további frissítéseket hoz létre a célszámítógépen a hivatkozott objektumok létrehozásához és összekapcsolásához. Előfordulhat például, hogy egy felhasználó "Manager" attribútummal rendelkezik a célszámítógépen, amely egy másik, a célszámítógépen létrehozott felhasználóhoz van társítva.

7. Ha egy korábban a kiépítés hatókörében lévő felhasználó el lett távolítva a hatókörből, beleértve a hozzá nem rendelt szolgáltatást, a szolgáltatás egy frissítésen keresztül letiltja a felhasználót a célszámítógépen.

8. Ha egy korábban a kiépítés hatókörében lévő felhasználó le van tiltva vagy nem törlődik a forrásrendszer, a szolgáltatás egy frissítésen keresztül letiltja a felhasználót a célszámítógépen.

9. Ha egy korábban a kiépítés hatókörében lévő felhasználó nem törlődik a forrásoldali rendszeren, a szolgáltatás törli a felhasználót a célszámítógépen. Az Azure AD-ben a felhasználók a törlés után 30 nappal törlődnek.

10. A növekményes ciklus végén egy új vízjel marad, amely a későbbi növekményes ciklusok kiindulási pontját adja meg.

> [!NOTE]
> A **létrehozási**, **frissítési**vagy **törlési** műveletet letilthatja a [leképezések](customize-application-attributes.md) szakasz **cél objektum műveletei** jelölőnégyzetének használatával. A felhasználók egy frissítés során való letiltásának logikáját a (z) "accountEnabled" mezőből származó attribútum-hozzárendeléssel is ellenőrzik.

A kiépítési szolgáltatás továbbra is határozatlan időre futtatja az [egyes alkalmazásokra vonatkozó oktatóanyagban](../saas-apps/tutorial-list.md)meghatározott időközöket. A növekményes ciklusok addig folytatódnak, amíg az alábbi események egyike nem következik be:

- A szolgáltatás kézi leállítása a Azure Portal használatával vagy a megfelelő Graph API parancs használatával történik. 
- A rendszer az új kezdeti ciklust a **Törlés állapota és az újraindítás** lehetőség használatával indítja el a Azure Portalban, vagy a megfelelő Graph API parancs használatával. Ez a művelet törli a tárolt vízjeleket, és az összes forrásobjektum kiértékelését okozza.
- Az attribútum-hozzárendelések és a hatóköri szűrők változása miatt új kezdeti ciklust indít a rendszer. Ezzel a művelettel az összes tárolt vízjel törlődik, és az összes forrásoldali objektum újbóli kiértékelését okozza.
- A kiépítési folyamat Karanténba kerül (lásd alább), magas hiba miatt, és több mint négy hétig karanténba marad. Ebben az esetben a szolgáltatás automatikusan le lesz tiltva.

### <a name="errors-and-retries"></a>Hibák és újrapróbálkozások

Ha a célrendszer egyik hibája megakadályozza, hogy egy adott felhasználó hozzá legyen adva, frissítve vagy törölve legyen a célszámítógépen, a rendszer a következő szinkronizálási ciklusban újrapróbálkozik a művelettel. Ha a felhasználó továbbra is sikertelen, akkor az újrapróbálkozások kevesebb gyakorisággal fognak megtörténni, és a napi egyszeri próbálkozásra fokozatosan visszakerülnek. A hiba elhárításához a rendszergazdáknak ellenőriznie kell a [kiépítési naplókat](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) a kiváltó ok okának megállapításához és a megfelelő művelet meghozatalához. Gyakori hibák a következők lehetnek:

- Azok a felhasználók, akik nem rendelkeznek olyan attribútummal, amely a célként megadott rendszerhez szükséges a forrásoldali rendszeren
- Azok a felhasználók, akiknek attribútum értéke van abban a forrásrendszer esetében, amelyhez egyedi korlátozás van megadva a célszámítógépen, és ugyanaz az érték egy másik felhasználói rekordban van

Hárítsa el ezeket a hibákat úgy, hogy módosítja az érintett felhasználóhoz tartozó attribútumérték értékét a forrásrendszer vagy az attribútumok leképezésének módosításával, hogy ne okozzák az ütközéseket.

### <a name="quarantine"></a>Karantén

Ha a megcélzott rendszerre irányuló hívások többsége vagy mindegyike egy hiba miatt nem sikerül (például érvénytelen rendszergazdai hitelesítő adatok), a kiépítési feladat "karantén" állapotba kerül. Ez az állapot a [kiépítési összefoglaló jelentésben](check-status-user-account-provisioning.md) és e-mailben, ha az e-mailes értesítések konfigurálva vannak a Azure Portalban.

Karantén esetén a növekményes ciklusok gyakorisága naponta egyszer csökken.

A kiépítési feladatok kilépnek a karanténba helyezésből az összes jogsértő hiba kijavítása után, és a következő szinkronizálási ciklus elindul. Ha a kiépítési feladatok több mint négy hétig maradnak karanténban, a kiépítési feladatok le vannak tiltva. További [információ itt található a karantén állapotáról](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

### <a name="how-long-provisioning-takes"></a>Mennyi időt vesz igénybe az átadás?

A teljesítmény attól függ, hogy a kiépítési feladatok kezdeti kiépítési ciklust vagy növekményes ciklust futtatnak-e. A kiépítés időtartamáról és a kiépítési szolgáltatás állapotának figyeléséről a következő témakörben tájékozódhat: [a felhasználó kiépítési állapotának ellenőrzésével](application-provisioning-when-will-provisioning-finish-specific-user.md)kapcsolatos információk.

### <a name="how-to-tell-if-users-are-being-provisioned-properly"></a>Hogyan állapítható meg, hogy a felhasználók megfelelően lettek-e kiépítve

A felhasználói kiépítési szolgáltatás által futtatott összes művelet rögzítve van az Azure AD- [létesítési naplókban (előzetes verzió)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). A naplók tartalmazzák a forrás-és a megcélzott rendszerekre vonatkozó összes olvasási és írási műveletet, valamint az egyes műveletek során beolvasott vagy írt felhasználói adatok tartalmát. További információ a kiépítési naplók beolvasásáról a Azure Portalban: a [kiépítési jelentési útmutató](check-status-user-account-provisioning.md).

## <a name="de-provisioning"></a>Kiépítés megszüntetése

Az Azure AD-kiépítési szolgáltatás megőrzi a forrás-és a megcélzott rendszereket a kiépítési fiókok szinkronizálásával, ha a felhasználóknak többé nem férnek hozzá. 

Az Azure AD-kiépítési szolgáltatás helyreállítja a felhasználót az alkalmazásban, ha az alkalmazás suupports a Soft deletes (Update Request with Active = false), és az alábbi események bármelyike előfordul:

* A felhasználói fiók törlődik az Azure AD-ben
*   A felhasználó nincs kiosztva az alkalmazásból
*   A felhasználó már nem felel meg egy hatóköri szűrőnek, és kikerül a hatókörből
    * Alapértelmezés szerint az Azure AD-kiépítési szolgáltatás nem törli vagy letiltja a hatókörön kívüli felhasználókat. Ha szeretné felülbírálni ezt az alapértelmezett viselkedést, beállíthatja a jelölőt a [hatókörbeli törlés kihagyásához](skip-out-of-scope-deletions.md).
*   A AccountEnabled tulajdonság értéke false (hamis)

Ha a fenti négy esemény egyike következik be, és a célalkalmazás nem támogatja a Soft deletes szolgáltatást, a kiépítési szolgáltatás egy TÖRLÉSi kérelmet küld, amely véglegesen törli a felhasználót az alkalmazásból. 

30 nappal azután, hogy egy felhasználó törölve lett az Azure AD-ben, véglegesen törölve lesznek a bérlőről. Ezen a ponton a kiépítési szolgáltatás elküld egy TÖRLÉSi kérelmet, amely véglegesen törli a felhasználót az alkalmazásban. A 30 napos időszak alatt bármikor [manuálisan törölheti a felhasználót]( https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-restore), amely törlési kérelmet küld az alkalmazásnak.

Ha az IsSoftDeleted attribútumot lát, a rendszer a felhasználó állapotát határozza meg, valamint azt, hogy az aktív = false értékkel rendelkező frissítési kérést szeretné-e elküldeni a felhasználó számára. 

## <a name="next-steps"></a>Következő lépések

[A felhasználók automatikus üzembe helyezésének megtervezése](plan-auto-user-provisioning.md)

[Kiépítés konfigurálása egy katalógusbeli alkalmazáshoz](configure-automatic-user-provisioning-portal.md)

[Hozzon létre egy SCIM-végpontot, és konfigurálja a létesítést saját alkalmazás létrehozásakor](use-scim-to-provision-users-and-groups.md)

[A felhasználók egy alkalmazáshoz való konfigurálásával és üzembe helyezésével kapcsolatos problémák elhárítása](application-provisioning-config-problem.md).
