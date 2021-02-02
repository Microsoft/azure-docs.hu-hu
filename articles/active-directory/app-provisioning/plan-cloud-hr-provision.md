---
title: A Cloud HR-alkalmazás megtervezése Azure Active Directory a felhasználók üzembe helyezéséhez
description: Ez a cikk a Felhőbeli HR-rendszerek (például a munkanapok és a SuccessFactors) integrálásának telepítési folyamatát ismerteti Azure Active Directoryokkal. Az Azure AD és a Felhőbeli HR rendszer integrálásával egy teljes identitás-életciklus-kezelési rendszer is elérhető.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 11/22/2019
ms.author: kenwith
ms.reviewer: arvindha, celested
ms.openlocfilehash: 9c896d4cccf898b8818b4c363c5bc891a8734ca5
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99256711"
---
# <a name="plan-cloud-hr-application-to-azure-active-directory-user-provisioning"></a>A Cloud HR-alkalmazás megtervezése Azure Active Directory a felhasználók üzembe helyezéséhez

Az informatikai munkatársak az alkalmazottak létrehozására, frissítésére és törlésére szolgáló manuális metódusokra támaszkodtak. Olyan módszereket használtak, mint például a CSV-fájlok vagy egyéni parancsfájlok feltöltése az alkalmazotti adatszinkronizáláshoz. Ezek a kiépítési folyamatok a hibákra hajlamosak, nem biztonságosak, és nehezen kezelhetők.

Az alkalmazottak, szállítók vagy függő munkavégzők identitási életciklusának kezeléséhez [Azure Active Directory (Azure ad) felhasználó-kiépítési szolgáltatás](../app-provisioning/user-provisioning.md) a felhőalapú emberi erőforrások (HR) alkalmazásaival való integrációt is biztosítja. Ilyenek például a munkanap vagy a SuccessFactors.

Az Azure AD ezt az integrációt használja a következő Felhőbeli HR-alkalmazás-munkafolyamatok engedélyezéséhez:

- **Felhasználók kiépítése a Active Directoryba:** Egy Felhőbeli HR-alkalmazásból kiválasztott felhasználói készletek kiépítése egy vagy több Active Directory tartományba.
- **Csak felhőalapú felhasználók kiépítése az Azure ad-be:** Olyan helyzetekben, ahol a Active Directory nincs használatban, közvetlenül a Cloud HR-alkalmazásból kiépítheti a felhasználókat az Azure AD-be.
- **Írjon vissza a Cloud HR alkalmazásba:** Az Azure AD-beli e-mail-címek és felhasználónevek attribútumainak megírása a Cloud HR alkalmazásba.

> [!NOTE]
> Ez a telepítési terv bemutatja, hogyan helyezheti üzembe a Felhőbeli HR-alkalmazás munkafolyamatait az Azure AD-beli felhasználók kiépítésével. További információ az automatikus felhasználó-kiépítési szolgáltatás (SaaS) alkalmazásaiban történő üzembe helyezéséről: [automatikus felhasználó üzembe helyezésének megtervezése](./plan-auto-user-provisioning.md).

## <a name="enabled-hr-scenarios"></a>Engedélyezett HR-forgatókönyvek

Az Azure AD-beli felhasználói kiépítési szolgáltatás lehetővé teszi a következő HR-alapú identitás-kezelési forgatókönyvek automatizálását:

- **Új alkalmazottak felvétele:** Amikor új alkalmazott van hozzáadva a Cloud HR-alkalmazáshoz, a rendszer automatikusan létrehoz egy felhasználói fiókot a Active Directory és az Azure AD-ben, hogy az e-mail-cím és a username attribútumokat a Cloud HR alkalmazásba írja vissza.
- **Az Employee attribútum és a profil frissítései:** Ha a Cloud HR alkalmazásban egy alkalmazotti rekord, például a név, a cím vagy a felettes frissül, a felhasználói fiókja automatikusan frissül Active Directory és az Azure AD-ben.
- **Alkalmazotti megszakítások:** Ha egy alkalmazott leáll a Cloud HR alkalmazásban, a felhasználói fiókja automatikusan le lesz tiltva Active Directory és az Azure AD-ben.
- **Alkalmazottak újrabérlése:** Ha egy alkalmazottat a Cloud HR alkalmazásban újra felvesznek, a régi fiókja automatikusan újraaktiválható vagy újraépíthető a Active Directory és az Azure AD szolgáltatásba.

## <a name="who-is-this-integration-best-suited-for"></a>Ki a legmegfelelőbb integrációs csomag?

A Felhőbeli HR-alkalmazás integrációja az Azure AD-vel – a felhasználók üzembe helyezése ideális olyan szervezetek számára, amelyek:

- Szeretne egy előre elkészített felhőalapú megoldást használni a Felhőbeli HR-felhasználók üzembe helyezéséhez.
- A Felhőbeli HR-alkalmazásból Active Directory vagy az Azure AD-be való közvetlen felhasználói üzembe helyezés szükséges.
- A Felhőbeli HR-alkalmazásból beszerzett adatok használatával kell kiépíteni a felhasználókat.
- A felhasználók egy vagy több Active Directory-erdő, tartomány és szervezeti egység számára való szinkronizálásának megkövetelése csak a Cloud HR alkalmazásban észlelt változási információk alapján.
- E-mailek Microsoft 365 használata.

## <a name="learn"></a>Learn

A felhasználók üzembe helyezése létrehoz egy alapot a folyamatos identitás-irányításhoz. Fokozza a mérvadó személyazonossági adatokra támaszkodó üzleti folyamatok minőségét.

### <a name="terms"></a>Kifejezések

Ez a cikk a következő kifejezéseket használja:

- **Forrásoldali rendszer**: az Azure ad által kiépített felhasználók tárháza. Ilyen például egy Felhőbeli HR-alkalmazás, például a munkanap vagy a SuccessFactors.
- Célrendszer: az Azure AD által **kiépített** felhasználók tárháza. Ilyenek például a következők: Active Directory, Azure AD, Microsoft 365 vagy más SaaS-alkalmazás.
- **Csatlakozások**– a kimaradók folyamata: az új bérletek, átadások és leállítások kifejezése, amely egy FELHŐbeli HR-alkalmazás a rekordok rendszereként való használatával történik. A folyamat akkor fejeződik be, amikor a szolgáltatás sikeresen kiépíti a szükséges attribútumokat a célszámítógépen.

### <a name="key-benefits"></a>Főbb előnyök

A HR-alapú IT-kiépítés ezen funkciója a következő jelentős üzleti előnyöket kínálja:

- **Növelje a termelékenységet:** Mostantól automatizálhatja a felhasználói fiókok és Microsoft 365 licencek hozzárendelését, és hozzáférést biztosíthat a kulcsfontosságú csoportokhoz. A hozzárendelések automatizálása lehetővé teszi, hogy az új Hirek azonnal hozzáférjenek a feladathoz, és növelik a termelékenységet.
- **Kockázat kezelése:** Növelheti a biztonságot úgy, hogy az alkalmazottak állapota vagy csoporttagságok alapján automatizálja a módosításokat a Felhőbeli HR-alkalmazásból származó adatokkal. A változások automatizálása biztosítja, hogy a felhasználói identitások és a Key apps hozzáférése automatikusan megtörténjen, amikor a felhasználók áttérnek vagy elhagyják a szervezetet.
- **Címek megfelelősége és szabályozása:** Az Azure AD támogatja a natív naplókat a forrás-és a célként megadott alkalmazások által végrehajtott felhasználói kiépítési kérelmek esetében. A naplózás segítségével nyomon követheti, hogy ki férhet hozzá az alkalmazásokhoz egyetlen képernyőről.
- **Kezelés díja:** Az automatikus kiépítés csökkenti a költségeket, és elkerüli a manuális kiépítés során felmerülő eredménytelenség és emberi hibák elkerülését. Ez csökkenti a régi és elavult platformok használatával az idő múlásával létrehozott, egyéni fejlesztésű felhasználói megoldások igényét.

### <a name="licensing"></a>Licencek

Ha a Cloud HR-alkalmazást az Azure AD-beli felhasználók kiépítéséhez szeretné konfigurálni, érvényes [prémium szintű Azure ad licencre](https://azure.microsoft.com/pricing/details/active-directory/) és licencre van szükség a Cloud HR-alkalmazáshoz, például a munkanapokhoz vagy a SuccessFactors.

Emellett érvényes prémium szintű Azure AD P1 vagy magasabb szintű előfizetési licencre van szükség minden olyan felhasználóhoz, aki a Cloud HR-alkalmazásból származik, és amely Active Directory vagy az Azure AD-hez lett kiépítve. A Felhőbeli HR-alkalmazás tulajdonában lévő licencek helytelen száma hibát okozhat a felhasználók kiosztása során.

### <a name="prerequisites"></a>Előfeltételek

- Azure AD [Hybrid Identity Administrator](../roles/permissions-reference.md#hybrid-identity-administrator)  az Azure ad Connect létesítési ügynök konfigurálásához.
- Az Azure AD- [alkalmazás rendszergazdai](../roles/permissions-reference.md#application-administrator) szerepköre a kiépítési alkalmazás konfigurálásához a Azure Portal
- A Cloud HR-alkalmazás tesztelési és éles példánya.
- Rendszergazdai jogosultságok a Cloud HR alkalmazásban egy rendszerintegrációs felhasználó létrehozásához, valamint a tesztelési célú alkalmazottak ellenőrzésének megváltoztatásához.
- A Active Directory való felhasználói üzembe helyezéshez egy Windows Server 2012 vagy újabb rendszert futtató kiszolgáló szükséges a .NET 4.7.1 + Runtime futtatásához a Azure AD Connect kiépítési ügynök üzemeltetéséhez.
- [Azure ad Connect](../hybrid/whatis-azure-ad-connect.md) a felhasználók Active Directory és az Azure ad közötti szinkronizálásához.

### <a name="training-resources"></a>Erőforrások betanítása

| **Erőforrások** | **Hivatkozás és leírás** |
|:-|:-|
| Videók | [Mi a felhasználók üzembe helyezése az aktív Azure-címtárban?](https://youtu.be/_ZjARPpI6NI) |
| | [A felhasználók üzembe helyezésének központi telepítése az Active Directory Azure-címtárban](https://youtu.be/pKzyts6kfrw) |
| Oktatóanyagok | [Az SaaS-alkalmazások Azure AD-vel való integrálásával kapcsolatos oktatóanyagok listája](../saas-apps/tutorial-list.md) |
| | [Oktatóanyag: munkanapok konfigurálása a felhasználók automatikus kiépítési felállításához](../saas-apps/workday-inbound-tutorial.md#frequently-asked-questions-faq) |
| GYIK | [Automatikus felhasználó-kiépítés](../app-provisioning/user-provisioning.md#what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning) |
| | [Kiépítés a munkanapokból az Azure AD-be](../saas-apps/workday-inbound-tutorial.md#frequently-asked-questions-faq) |

### <a name="solution-architecture"></a>Megoldásarchitektúra

Az alábbi példa bemutatja a teljes körű felhasználó kiépítési megoldási architektúráját a gyakori hibrid környezetekhez, és a következőket tartalmazza:

- **Mérvadó HR-adatfolyam a Cloud HR-alkalmazásból a Active Directoryba.** Ebben a folyamatban a HR-eseményt (összekapcsolások – a kimaradók folyamatát) a Cloud HR-alkalmazás bérlője kezdeményezi. Az Azure AD kiépítési szolgáltatás és a Azure AD Connect kiépítési ügynök kiépíti a felhasználói adatait a Cloud HR-alkalmazás bérlőből a Active Directoryba. Az eseménytől függően előfordulhat, hogy létrehoz, frissít, engedélyez és letilt műveleteket a Active Directoryban.
- **Szinkronizálja az Azure AD-vel, és írjon vissza e-maileket és felhasználóneveket a helyszíni Active Directory a Cloud HR alkalmazásba.** A fiókok Active Directory-ben való frissítése után szinkronizálva lesz az Azure AD-val Azure AD Connecton keresztül. Az e-mail-címek és a felhasználónevek attribútumai visszaírhatók a Cloud HR-alkalmazás bérlője számára.

![Munkafolyamat-diagram](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img1.png)

#### <a name="description-of-workflow"></a>A munkafolyamat leírása

A következő fő lépések szerepelnek a diagramon:  

1. **HR-csapat** végzi el a tranzakciókat a Cloud HR-alkalmazás bérlője számára.
2. Az **Azure ad-kiépítési szolgáltatás** futtatja az ütemezett ciklusokat a Cloud HR-alkalmazás bérlőn, és azonosítja azokat a módosításokat, amelyeket a Active Directory való szinkronizáláshoz fel kell dolgozni.
3. Az **Azure ad-kiépítési szolgáltatás** meghívja a Azure ad Connect létesítési ügynököt egy olyan kérési adattartalommal, amely Active Directory fiók létrehozási, frissítési, engedélyezési és letiltási műveleteit tartalmazza.
4. **Azure ad Connect kiépítési ügynök** egy szolgáltatásfiókot használ a Active Directory fiókadatok kezeléséhez.
5. A **Azure ad Connect** a különbözeti [szinkronizálást](../hybrid/how-to-connect-sync-whatis.md) futtatja a frissítések Active Directoryban való lekéréséhez.
6. **Active Directory** frissítések szinkronizálva vannak az Azure ad-vel.
7. Az **Azure ad-kiépítési szolgáltatás** az Azure ad-ből a Cloud HR-alkalmazás bérlője számára ír e-mail-attribútumot és felhasználónevet.

## <a name="plan-the-deployment-project"></a>Az üzembe helyezési projekt megtervezése

A környezetében érdemes figyelembe vennie a szervezeti igényeket is.

### <a name="engage-the-right-stakeholders"></a>A megfelelő résztvevők bevonása

Ha a technológiai projektek sikertelenek, ezek általában a hatás, az eredmények és a felelősségek eltérő elvárásai miatt válnak. A buktatók elkerülése érdekében [Győződjön meg arról, hogy a megfelelő érintett feleket folytatja](../fundamentals/active-directory-deployment-plans.md). Győződjön meg arról is, hogy a projektben szereplő érintett szerepkörök jól megértettek. Dokumentálja az érintett feleket és a projekt bemenetét és elszámoltathatóság.

Vegyen fel egy képviselőt a HR-szervezetből, amely a meglévő HR üzleti folyamatokon és a feldolgozó identitáson, valamint a feladatok adatfeldolgozási követelményein keresztül biztosít bemeneteket.

### <a name="plan-communications"></a>A kommunikáció tervezése

A kommunikáció minden új szolgáltatás sikeressége szempontjából kritikus fontosságú. Proaktív módon kommunikálhat a felhasználókkal arról, hogy mikor és hogyan változnak a felhasználói élmény. Tudassa velük, hogyan szerezhetnek támogatást, ha problémákat tapasztalnak.

### <a name="plan-a-pilot"></a>Pilóta megtervezése

A HR üzleti folyamatainak és az identitás munkafolyamatainak a Cloud HR-alkalmazásból a megcélzott rendszerbe való integrálásához jelentős mennyiségű adatellenőrzésre, adatátalakításra, Adattisztításra és teljes körű tesztelésre van szükség, mielőtt üzembe helyezi a megoldást éles környezetben.

Futtassa a kezdeti konfigurációt egy [kísérleti környezetben](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot) , mielőtt az összes éles üzemben lévő felhasználóra méretezni kívánja.

## <a name="select-cloud-hr-provisioning-connector-apps"></a>Felhőbeli HR kiépítési összekötő alkalmazásainak kiválasztása

Az Azure AD üzembe helyezési munkafolyamatainak a Cloud HR-alkalmazás és a Active Directory közötti megkönnyítéséhez több kiépítési összekötő-alkalmazást is hozzáadhat az Azure AD-alkalmazás-katalógusból:

- **Cloud HR-alkalmazás a Active Directory a felhasználók üzembe** helyezéséhez: Ez a kiépítési összekötő alkalmazás a Cloud HR-alkalmazásból egyetlen Active Directory tartományba helyezi el a felhasználói fiókok kiépítési folyamatát. Ha több tartománya van, az alkalmazás egy példányát hozzáadhatja az Azure AD-alkalmazás-katalógusból minden Active Directory-tartományhoz, amelyre telepítenie kell.
- **Cloud HR-alkalmazás az Azure ad-** beli felhasználók számára: míg Azure ad Connect az az eszköz, amelyet a felhasználók Azure ad-vel való Active Directory szinkronizálásához kell használni, ez a kiépítési összekötő alkalmazás a Cloud HR-alkalmazásból egyetlen Azure ad-bérlővé való kiépítés megkönnyítésére használható.
- **Cloud HR-alkalmazás írása**: Ez a kiépítési összekötő alkalmazás megkönnyíti a felhasználó e-mail-címeinek visszaírását az Azure ad-ből a Cloud HR alkalmazásba.

Az alábbi képen például megtekintheti az Azure AD App Galleryben elérhető munkanapokat összekötő alkalmazásokat.

![Azure Active Directory portál alkalmazás-gyűjtemény](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img2.png)

### <a name="decision-flow-chart"></a>Döntési folyamatábra

A következő döntési folyamatábra segítségével azonosíthatja, hogy mely Felhőbeli HR-kiépítési alkalmazások kapcsolódnak a forgatókönyvhöz.

![Döntési folyamatábra](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img3.png)

## <a name="design-the-azure-ad-connect-provisioning-agent-deployment-topology"></a>A Azure AD Connect létesítési ügynök telepítési topológiájának megtervezése

A Cloud HR-alkalmazás és Active Directory közötti kiépítési integrációhoz négy összetevő szükséges:

- Cloud HR-alkalmazás bérlője
- Összekötő-alkalmazás üzembe helyezése
- Azure AD Connect kiépítési ügynök
- Active Directory-tartomány

A Azure AD Connect kiépítési ügynök üzembe helyezési topológiája a felhőalapú HR-alkalmazások bérlői számától és az integrálni kívánt, Active Directory alárendelt tartományokból függ. Ha több Active Directory tartománya van, attól függ, hogy a Active Directory tartományok folytonos vagy [különállóak](/windows-server/identity/ad-ds/plan/disjoint-namespace)-e.

Az Ön döntése alapján válasszon egyet a telepítési forgatókönyvek közül:

- Egyetlen Felhőbeli HR-alkalmazás bérlője – > cél egy vagy több Active Directory gyermektartomány egy megbízható erdőben
- Egyetlen Felhőbeli HR-alkalmazás bérlője – > több gyermektartomány megcélzása különálló Active Directory erdőben

### <a name="single-cloud-hr-app-tenant---target-single-or-multiple-active-directory-child-domains-in-a-trusted-forest"></a>Egyetlen Felhőbeli HR-alkalmazás bérlője – > cél egy vagy több Active Directory gyermektartomány egy megbízható erdőben

A következő éles konfigurációt javasoljuk:

|Követelmény|Ajánlás|
|:-|:-|
|Telepítendő Azure AD Connect kiépítési ügynökök száma|Kettő (a magas rendelkezésre álláshoz és a feladatátvételhez)
|A konfigurálni kívánt kiépítési összekötő-alkalmazások száma|Egy alkalmazás/gyermektartomány|
|Azure AD Connect kiépítési ügynök kiszolgálójának gazdagépe|Windows 2012 R2 + a köthetők Active Directory tartományvezérlőkön</br>A Azure AD Connect szolgáltatással együtt is létezhet|

![A helyszíni ügynökök felé irányuló folyamat](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img4.png)

### <a name="single-cloud-hr-app-tenant---target-multiple-child-domains-in-a-disjoint-active-directory-forest"></a>Egyetlen Felhőbeli HR-alkalmazás bérlője – > több gyermektartomány megcélzása különálló Active Directory erdőben

Ez a forgatókönyv magában foglalja a felhasználókat a Cloud HR-alkalmazásból a különálló Active Directory erdőkben lévő tartományokra való kiépítés során.

A következő éles konfigurációt javasoljuk:

|Követelmény|Ajánlás|
|:-|:-|
|A helyszíni üzembe helyezéshez Azure AD Connect kiépítési ügynökök száma|Két különálló Active Directory erdőben|
|A konfigurálni kívánt kiépítési összekötő-alkalmazások száma|Egy alkalmazás/gyermektartomány|
|Azure AD Connect kiépítési ügynök kiszolgálójának gazdagépe|Windows 2012 R2 + a köthetők Active Directory tartományvezérlőkön</br>A Azure AD Connect szolgáltatással együtt is létezhet|

![Egyetlen Felhőbeli HR-alkalmazás bérlője különálló Active Directory erdőben](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img5.png)

### <a name="azure-ad-connect-provisioning-agent-requirements"></a>Azure AD Connect kiépítési ügynökre vonatkozó követelmények

A Felhőbeli HR-alkalmazásnak Active Directory a felhasználó kiépítési megoldásához egy vagy több Azure AD Connect üzembe helyezési ügynököt kell telepítenie a Windows 2012 R2 vagy újabb rendszert futtató kiszolgálókon. A kiszolgálóknak legalább 4 GB RAM-mal és .NET 4.7.1 + futtatókörnyezettel kell rendelkezniük. Győződjön meg arról, hogy a gazdagépnek van hálózati hozzáférése a cél Active Directory tartományhoz.

A helyszíni környezet előkészítéséhez az Azure AD Connect létesítési ügynök konfigurálása varázsló regisztrálja az ügynököt az Azure AD-Bérlővel, megnyitja a [portokat](../manage-apps/application-proxy-add-on-premises-application.md#open-ports), [engedélyezi az URL-címek elérését](../manage-apps/application-proxy-add-on-premises-application.md#allow-access-to-urls), és támogatja a [kimenő HTTPS-proxy konfigurációját](../saas-apps/workday-inbound-tutorial.md#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication).

A kiépítési ügynök egy szolgáltatásfiókot használ a Active Directory tartományokkal való kommunikációhoz. Az ügynök telepítése előtt hozzon létre egy szolgáltatásfiókot Active Directory felhasználók és számítógépek számára, amelyek megfelelnek az alábbi követelményeknek:

- Olyan jelszó, amely nem jár le
- Delegált vezérlési engedélyek a felhasználói fiókok olvasásához, létrehozásához, törléséhez és kezeléséhez

Kiválaszthatja azokat a tartományvezérlőket, amelyeknek kezelniük kell a kiépítési kérelmeket. Ha több földrajzilag elosztott tartományvezérlővel rendelkezik, telepítse a kiépítési ügynököt az elsődleges tartományvezérlővel megegyező helyen. Ez a pozíció javítja a végpontok közötti megoldás megbízhatóságát és teljesítményét.

A magas rendelkezésre állás érdekében több Azure AD Connect kiépítési ügynököt is telepíthet. Regisztrálja az ügynököt a helyszíni Active Directory tartományok azonos készletének kezeléséhez.

## <a name="plan-scoping-filters-and-attribute-mapping"></a>A hatóköri szűrők és az attribútumok megfeleltetésének megtervezése

Ha engedélyezi az üzembe helyezést a Cloud HR alkalmazásból Active Directory vagy az Azure AD-be, a Azure Portal attribútum-hozzárendelésen keresztül vezérli az attribútum értékeit.

### <a name="define-scoping-filters"></a>Hatóköri szűrők definiálása

A [hatóköri szűrők](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) használatával meghatározhatja azokat az attribútum-alapú szabályokat, amelyek meghatározzák, hogy mely felhasználókat kell kiépíteni a Cloud HR-alkalmazásból a Active Directory vagy az Azure ad-ba.

Az összekapcsolási folyamat indításakor a következő követelményeket kell összegyűjtenie:

- A Felhőbeli HR-alkalmazás az alkalmazottakat és a függő alkalmazottakat is felhasználja?
- Azt tervezi, hogy a Cloud HR-alkalmazást az Azure AD-ben a felhasználók és a függőben lévő munkavállalók kezelésére használja?
- Tervezi, hogy a Cloud HR-alkalmazást az Azure AD-felhasználók számára csak a Cloud HR-alkalmazás felhasználói számára szeretné kiépíteni? Lehetséges például, hogy csak az alkalmazottak.

A követelményektől függően az attribútumok leképezésének konfigurálásakor beállíthatja a **forrás objektum hatóköre** mezőt, hogy kiválassza a FELHŐbeli HR-alkalmazás felhasználói készleteit, hogy a kiépítés hatóköre Active Directory. További információ: a Cloud HR alkalmazás oktatóanyaga a gyakran használt hatóköri szűrőkhöz.

### <a name="determine-matching-attributes"></a>Egyező attribútumok meghatározása

A kiépítés lehetővé teszi a meglévő fiókok egyeztetését a forrás-és a célként megadott rendszeren. Ha az Azure AD-létesítési szolgáltatással integrálja a Felhőbeli HR-alkalmazást, [konfigurálhatja az attribútum-hozzárendelést](../app-provisioning/configure-automatic-user-provisioning-portal.md#mappings) annak meghatározásához, hogy a felhő HR-alkalmazásból milyen felhasználói adatok kerüljenek a Active Directory vagy az Azure ad-ba.

Az összekapcsolási folyamat indításakor a következő követelményeket kell összegyűjtenie:

- Mi a Felhőbeli HR-alkalmazás egyedi azonosítója, amely az egyes felhasználók azonosítására szolgál?
- Az identitás életciklusának perspektívájában hogyan kezeli az újratelepítést? Megőrzik a régi alkalmazotti azonosítókat?
- Készen áll-e a jövőbeli bérletek feldolgozására és Active Directory fiókok előzetes létrehozására?
- Az identitás életciklusának perspektívájában hogyan kezelheti az alkalmazottakat a függőben lévő munkavégzők átalakítására, vagy egyéb módon?
- A konvertált felhasználók megtartják a régi Active Directory fiókjaikat, vagy újakat kapnak?

A követelményektől függően az Azure AD támogatja a közvetlen attribútum-attribútum hozzárendelést, ha állandó értékeket biztosít, vagy [kifejezést ír az attribútum-hozzárendelésekhez](../app-provisioning/functions-for-customizing-application-data.md). Ez a rugalmasság lehetővé teszi, hogy a megtekintett app attribútumban lévő adatokra vonatkozó végső felügyeletet biztosítson. A [Microsoft Graph API](../app-provisioning/export-import-provisioning-configuration.md) és a Graph Explorer használatával exportálhatja a felhasználók kiépítési attribútumait a JSON-fájlba, majd importálhatja azt az Azure ad-be.

Alapértelmezés szerint a Felhőbeli HR alkalmazás azon attribútuma, amely az egyedi alkalmazott AZONOSÍTÓját jelöli, a rendszer a *Active Directory egyedi attribútumához hozzárendelt* egyező attribútumként használja. A munkanap alkalmazási forgatókönyvben például a **munkanap** **WorkerID** attribútum a Active Directory **AlkalmazottKód** attribútumra van leképezve.

Több egyező attribútumot is beállíthat, és hozzárendelheti a megfeleltetési prioritást. A kiértékelésük a megfeleltetések sorrendje szerint történik. Amint talál egyezést, nem lesz kiértékelve további egyező attribútumok.

[Testre szabhatja az alapértelmezett attribútumok leképezéseit](../app-provisioning/customize-application-attributes.md#understanding-attribute-mapping-types)is, például módosíthatja vagy törölheti a meglévő attribútumok hozzárendeléseit. Az üzleti igényeknek megfelelően új attribútum-leképezéseket is létrehozhat. További információ: a Cloud HR-alkalmazás oktatóanyaga (például [munkanap](../saas-apps/workday-inbound-tutorial.md#managing-your-configuration)) a leképezésre szolgáló egyéni attribútumok listájához.

### <a name="determine-user-account-status"></a>Felhasználói fiók állapotának meghatározása

Alapértelmezés szerint a kiépítési összekötő alkalmazás a HR felhasználói profil állapotát az Active Directory vagy az Azure AD felhasználói fiókjának állapotára képezi le, hogy eldöntse, hogy engedélyezi vagy letiltja a felhasználói fiókot.

A Joiners-Leavers folyamat indításakor a következő követelményeket kell összegyűjtenie.

| Folyamat | Követelmények |
| - | - |
| **Asztalos** | Az identitás életciklusának perspektívájában hogyan kezeli az újratelepítést? Megőrzik a régi alkalmazotti azonosítókat? |
| | Készen áll-e a jövőbeli bérletek feldolgozására és Active Directory fiókok előzetes létrehozására? Ezek a fiókok engedélyezve vagy letiltott állapotban lettek létrehozva? |
| | Az identitás életciklusának perspektívájában hogyan kezelheti az alkalmazottakat a függőben lévő munkavégzők átalakítására, vagy egyéb módon? |
| | A konvertált felhasználók megtartják a régi Active Directory fiókjaikat, vagy újakat kapnak? |
| **Iskolaelhagyók** | Az alkalmazottak és a függőben lévő munkavállalók eltérő módon kezelhetők a Active Directoryban? |
| | Milyen érvényes dátumokat kell figyelembe venni a felhasználói megszakítás feldolgozásához? |
| | Hogyan befolyásolja az alkalmazottak és a függő Worker-átalakítások a meglévő Active Directory fiókokat? |
| | Hogyan dolgozza fel az elhagyás műveletet a Active Directoryban? Az elállási műveleteket kezelni kell, ha a jövőbeli dátummal rendelkező bérletek Active Directory az asztalos folyamat részeként jönnek létre. |

A követelményektől függően testreszabhatja a leképezési logikát az [Azure ad-kifejezések](../app-provisioning/functions-for-customizing-application-data.md) használatával, hogy az Active Directory fiók engedélyezve legyen vagy le legyen tiltva az adatpontok kombinációja alapján.

### <a name="map-cloud-hr-app-to-active-directory-user-attributes"></a>A Cloud HR-alkalmazás leképezése Active Directory felhasználói attribútumokra

A Felhőbeli HR-alkalmazások alapértelmezett Felhőbeli HR-alkalmazásával Active Directory leképezéseket.

Az összekapcsolást kezdeményezők által kezdeményezett folyamat indításakor a következő követelményeket kell összegyűjtenie.

| Folyamat | Követelmények |
| - | - |
| **Asztalos** | A Active Directory fiók létrehozási folyamata manuális, automatizált vagy részlegesen automatizált? |
| | Tervezi a Cloud HR-alkalmazás egyéni attribútumainak propagálását Active Directoryre? |
| **Mozgatói** | Milyen attribútumokat szeretne feldolgozni a Cloud HR-alkalmazásban, amikor egy mozgató művelet zajlik? |
| | A felhasználói frissítések időpontjában bizonyos attribútumok érvényesek? Ha igen, adja meg a részleteket. |
| **Iskolaelhagyók** | Az alkalmazottak és a függőben lévő munkavállalók eltérő módon kezelhetők a Active Directoryban? |
| | Milyen érvényes dátumokat kell figyelembe venni a felhasználói megszakítás feldolgozásához? |
| | Hogyan befolyásolja az alkalmazottak és a függő munkavégzők a meglévő Active Directory fiókokat? |

A követelményektől függően módosíthatja a leképezéseket, hogy azok megfeleljenek az integrációs céloknak. További információkért tekintse meg az adott Felhőbeli HR-alkalmazás oktatóanyagát (például a [munkanapokat](../saas-apps/workday-inbound-tutorial.md#part-4-configure-attribute-mappings)) a leképezésre szolgáló egyéni attribútumok listájához.

### <a name="generate-a-unique-attribute-value"></a>Egyedi attribútumérték létrehozása

Az összekapcsolási folyamat indításakor előfordulhat, hogy egyedi attribútum-értékeket kell létrehoznia, amikor olyan attribútumokat állít be, mint például a CN, a samAccountName és az egyszerű felhasználónév, amely egyedi korlátozásokkal rendelkezik.

Az Azure AD-függvény [SelectUniqueValues](../app-provisioning/functions-for-customizing-application-data.md#selectuniquevalue) értékeli az egyes szabályokat, majd ellenőrzi az egyediséghez generált értéket a célként megadott rendszeren. Példaként tekintse meg [a userPrincipalName (UPN) attribútum egyedi értékének létrehozása](../app-provisioning/functions-for-customizing-application-data.md#generate-unique-value-for-userprincipalname-upn-attribute)című témakört.

> [!NOTE]
> Ez a függvény jelenleg csak a munkanapokon Active Directory a felhasználók üzembe helyezését támogatja. Más kiépítési alkalmazásokkal nem használható.

### <a name="configure-active-directory-ou-container-assignment"></a>Active Directory OU-tároló hozzárendelésének konfigurálása

Ez egy gyakori követelmény, hogy a felhasználói fiókokat az üzleti egységeken, helyeken és részlegeken alapuló tárolókban helyezze el Active Directory. Amikor kezdeményezői folyamatot indít el, és ha a felügyeleti szervezet módosul, előfordulhat, hogy a felhasználót át kell helyeznie az egyik szervezeti egységből a másikba Active Directory.

A [Switch ()](../app-provisioning/functions-for-customizing-application-data.md#switch) függvénnyel konfigurálja az üzleti logikát a szervezeti egység hozzárendeléséhez, és leképezheti azt a Active Directory attribútum **parentDistinguishedName**.

Ha például a HR-attribútum **helyhatósága** alapján szeretne felhasználókat létrehozni a szervezeti egységben, a következő kifejezést használhatja:

`
Switch([Municipality], "OU=Default,OU=Users,DC=contoso,DC=com", "Dallas", "OU=Dallas,OU=Users,DC=contoso,DC=com", "Austin", "OU=Austin,OU=Users,DC=contoso,DC=com", "Seattle", "OU=Seattle,OU=Users,DC=contoso,DC=com", "London", "OU=London,OU=Users,DC=contoso,DC=com")
`

Ezzel a kifejezéssel, ha a település értéke Dallas, Austin, Seattle vagy London, a felhasználói fiók a megfelelő szervezeti egységben lesz létrehozva. Ha nincs egyezés, a rendszer az alapértelmezett szervezeti egységben hozza létre a fiókot.

## <a name="plan-for-password-delivery-of-new-user-accounts"></a>Új felhasználói fiókok jelszavas kézbesítésének megtervezése

Az összekapcsolási folyamat indításakor be kell állítania és kézbesíteni kell az új felhasználói fiókok ideiglenes jelszavát. A Felhőbeli HR és az Azure AD felhasználói üzembe helyezése esetén az Azure AD önkiszolgáló [jelszó-visszaállítási](../authentication/tutorial-enable-sspr.md) (SSPR) képességét kivezetheti a felhasználó számára az első napon.

A SSPR egy egyszerű módja annak, hogy a rendszergazdák lehetővé tegyék a felhasználók számára a jelszavuk visszaállítását vagy a fiókok feloldását. A **Mobile Number** attribútumot kiépítheti a Cloud HR-alkalmazásból, hogy Active Directory és szinkronizálja az Azure ad-vel. Miután a **Mobile Number** attribútum az Azure ad-ben van, engedélyezheti a SSPR a felhasználói fiókhoz. Ezután az új felhasználó használhatja a regisztrált és ellenőrzött mobil számot a hitelesítéshez.

## <a name="plan-for-initial-cycle"></a>A kezdeti ciklus megtervezése

Amikor az Azure AD-létesítési szolgáltatás első alkalommal fut, a a Cloud HR-alkalmazásból [kezdeti ciklust](../app-provisioning/how-provisioning-works.md#initial-cycle) hajt végre a Cloud HR alkalmazásban lévő összes felhasználói objektum pillanatképének létrehozásához. A kezdeti ciklusokhoz szükséges idő közvetlenül attól függ, hogy hány felhasználó van jelen a forrásoldali rendszeren. Néhány Felhőbeli HR-alkalmazás, több mint 100 000 felhasználó esetén a kezdeti ciklus hosszú időt vehet igénybe.

A **nagyméretű Felhőbeli HR-alkalmazások bérlői számára (>30 000 felhasználó)** futtassa a kezdeti ciklust a fokozatos fázisokban. A növekményes frissítések csak akkor indíthatók el, ha ellenőrzi, hogy a megfelelő attribútumok be vannak-e állítva a Active Directory a különböző felhasználói kiépítési forgatókönyvek esetében. Kövesse a sorrendet.

1. A kiindulási ciklust csak korlátozott felhasználók számára futtassa a hatókör- [szűrő](#plan-scoping-filters-and-attribute-mapping)beállításával.
2. Ellenőrizze Active Directory fiók kiépítés és az első futtatáshoz kiválasztott felhasználók számára beállított attribútumok értékét. Ha az eredmény megfelel az elvárásainak, bontsa ki a hatókör-szűrőt, hogy fokozatosan több felhasználót tartalmazzon, és ellenőrizze a második Futtatás eredményét.

Miután elégedett a kezdeti ciklus eredményével a felhasználók teszteléséhez, indítsa el a [növekményes frissítéseket](../app-provisioning/how-provisioning-works.md#incremental-cycles).

## <a name="plan-testing-and-security"></a>A tesztelés és a biztonság tervezése

Az üzembe helyezés minden egyes fázisában a kezdeti tesztelésen keresztül a felhasználók kiépítésének engedélyezésével győződjön meg arról, hogy a várt módon teszteli az eredményeket, és naplózza a kiépítési ciklusokat.

### <a name="plan-testing"></a>Tesztelési terv

Miután beállította a Cloud HR-alkalmazást az Azure AD-felhasználók üzembe helyezéséhez, futtasson tesztelési eseteket annak ellenőrzéséhez, hogy a megoldás megfelel-e a szervezet követelményeinek.

|Forgatókönyvek|Várt eredmények|
|:-|:-|
|Új alkalmazott a Cloud HR alkalmazásban.| – A felhasználói fiók Active Directory van kiépítve.</br>– A felhasználó be tud jelentkezni Active Directory tartományi alkalmazásokba, és végrehajthatja a kívánt műveleteket.</br>– Ha a Azure AD Connect Sync konfigurálva van, a felhasználói fiók is létrejön az Azure AD-ben.
|A felhasználó leáll a Cloud HR-alkalmazásban.|– A felhasználói fiók le van tiltva Active Directoryban.</br>– A felhasználó nem tud bejelentkezni Active Directory által védett vállalati alkalmazásba.
|A felhasználó-felügyeleti szervezet frissült a Cloud HR-alkalmazásban.|Az attribútumok leképezése alapján a felhasználói fiók az egyik szervezeti egységből egy másikba kerül át Active Directory.|
|A HR frissíti a felhasználó felettesét a Cloud HR alkalmazásban.|A Active Directory vezető mezője frissül, hogy tükrözze az új felettes nevét.|
|HR új szerepkörbe helyezi át az alkalmazottat.|A viselkedés attól függ, hogy a Cloud HR-alkalmazás hogyan állítható be az alkalmazotti azonosítók létrehozásához:</br>-Ha a régi alkalmazott AZONOSÍTÓját újra felhasználják, az összekötő engedélyezi a meglévő Active Directory fiókot a felhasználó számára.</br>– Ha a rehire új alkalmazotti azonosítót kap, az összekötő létrehoz egy új Active Directory fiókot a felhasználó számára.|
|Az HR átalakítja az alkalmazottat egy szerződéses feldolgozóra, vagy fordítva.|Új Active Directory fiók jön létre az új personához, és a régi fiók le lesz tiltva az átalakítás érvényességi dátumán.|

Az előző eredmények alapján meghatározhatja, hogyan alakíthatja át az automatikus felhasználó-kiépítési implementációt éles környezetben a létrehozott ütemtervek alapján.

> [!TIP]
> Használjon olyan technikákat, mint például az adatok csökkentése és az adattisztítás, amikor a tesztkörnyezetben frissíti a környezeti adatokat a bizalmas személyes adatok eltávolításához vagy maszkolásához, hogy azok megfeleljenek az adatvédelmi és biztonsági előírásoknak. 

### <a name="plan-security"></a>A biztonság megtervezése

Egy új szolgáltatás üzembe helyezésének részeként gyakran előfordul, hogy biztonsági felülvizsgálatra van szükség. Ha egy biztonsági felülvizsgálatra van szükség vagy nem történt meg, tekintse meg a számos [Azure ad-tanulmányt](https://www.microsoft.com/download/details.aspx?id=36391) , amely áttekintést nyújt az identitásról szolgáltatásról.

### <a name="plan-rollback"></a>Terv visszaállítása

Előfordulhat, hogy a Felhőbeli HR-felhasználó kiépítési implementációja nem fog megfelelően működni az éles környezetben. Ha igen, a következő visszaállítási lépések segíthetnek a korábbi ismert jó állapot visszaállításában.

1. Tekintse át a [kiépítési összegző jelentést](../app-provisioning/check-status-user-account-provisioning.md#getting-provisioning-reports-from-the-azure-portal) és az üzembe helyezési [naplókat](../app-provisioning/check-status-user-account-provisioning.md#provisioning-logs-preview) , és állapítsa meg, hogy az érintett felhasználók vagy csoportok milyen helytelen műveleteket hajtottak végre. További információ a kiépítési összegző jelentésről és a naplókról: a [Cloud HR-alkalmazás felhasználó általi üzembe](#manage-your-configuration)helyezésének kezelése.
2. Az érintett felhasználók vagy csoportok utolsó ismert megfelelő állapota a kiépítési naplók alapján vagy a megcélzott rendszerek (Azure AD vagy Active Directory) felülvizsgálatával határozható meg.
3. Az alkalmazás tulajdonosával az utolsó ismert helyes állapotinformációkat használva frissítheti a közvetlenül az alkalmazásban érintett felhasználókat vagy csoportokat.

## <a name="deploy-the-cloud-hr-app"></a>A Cloud HR-alkalmazás üzembe helyezése

Válassza ki a Felhőbeli HR-alkalmazást, amely igazodik a megoldás követelményeihez.

**Munkanap**: Ha munkavégző profilokat szeretne importálni a munkanapokból Active Directory és Azure ad-be, tekintse meg a következőt [: oktatóanyag: munkanapok konfigurálása automatikus felhasználó](../saas-apps/workday-inbound-tutorial.md#planning-your-deployment)kiépítéséhez. Igény szerint visszaírhatja az e-mail-címet, a felhasználónevet és a telefonszámot a munkanapokra.

**SAP-SuccessFactors**: munkavégző profilok importálása a SuccessFactors-ből a Active Directory és az Azure ad-be, lásd [: OKTATÓANYAG: az SAP-SuccessFactors konfigurálása a felhasználók automatikus](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)üzembe helyezéséhez. Igény szerint visszaírhatja az e-mail-címet és a felhasználónevet a SuccessFactors.

## <a name="manage-your-configuration"></a>A konfiguráció kezelése

Az Azure AD további betekintést nyújt a szervezet felhasználó általi használatára és működési állapotára a naplók és a jelentések segítségével.

### <a name="gain-insights-from-reports-and-logs"></a>Jelentések és naplók betekintése

A sikeres [kezdeti ciklust](../app-provisioning/how-provisioning-works.md#initial-cycle)követően az Azure ad-kiépítési szolgáltatás továbbra is határozatlan ideig futtatja az egyes alkalmazásokra vonatkozó oktatóanyagokban meghatározott időközöket, amíg az alábbi események valamelyike be nem következik:

- A szolgáltatás kézi leállítása megtörténik. Az új kezdeti ciklust a [Azure Portal](https://portal.azure.com/) vagy a megfelelő [Microsoft Graph API](/graph/api/resources/synchronization-overview) -parancs használatával indítja el.
- Az attribútum-hozzárendelések és a hatóköri szűrők módosítása miatt új kezdeti ciklust indítunk el.
- A kiépítési folyamat a nagy hibák miatt Karanténba kerül. Több mint négy hétig marad karanténba helyezve, ekkor automatikusan le van tiltva.

Az események és a kiépítési szolgáltatás által végrehajtott egyéb tevékenységek áttekintéséhez tekintse [át a naplók áttekintését és a kiépítési tevékenységekről szóló jelentések beszerzését ismertető témakört](../app-provisioning/check-status-user-account-provisioning.md).

#### <a name="azure-monitor-logs"></a>Azure Monitor-naplók

A kiépítési szolgáltatás által végrehajtott összes tevékenységet az Azure AD-naplók rögzítik. További elemzés céljából átirányíthatja az Azure AD-naplókat Azure Monitor naplókba. A Azure Monitor-naplók (más néven Log Analytics munkaterület) segítségével lekérdezheti az eseményeket, elemezheti a trendeket, és elvégezheti a különböző adatforrások közötti korrelációt. Tekintse meg ezt a [videót](https://youtu.be/MP5IaCTwkQg) , amelyből megtudhatja, milyen előnyökkel jár a Azure monitor naplók használata az Azure ad-naplókhoz gyakorlati felhasználói helyzetekben.

Telepítse a [log Analytics-nézeteket az Azure ad-tevékenység naplóihoz](../reports-monitoring/howto-install-use-log-analytics-views.md) , hogy hozzáférjen az előre elkészített [jelentésekhez](https://github.com/AzureAD/Deployment-Plans/tree/master/Log%20Analytics%20Views) a környezetében található kiépítési események köré.

További információkért lásd: [Az Azure ad-tevékenységek naplóinak elemzése az Azure monitor naplókkal](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md).

### <a name="manage-personal-data"></a>Személyes adatok kezelése

A Windows Serverre telepített Azure AD Connect üzembe helyezési ügynök olyan naplókat hoz létre a Windows-eseménynaplóban, amelyek a Felhőbeli HR-alkalmazástól függően személyes adatait is tartalmazhatják Active Directory attribútum-hozzárendeléseket. A felhasználói adatvédelmi kötelezettségek betartása érdekében állítson be egy Windows ütemezett feladatot az Eseménynapló törléséhez, és győződjön meg arról, hogy a 48 órán túli adatok nem maradnak meg.

Az Azure AD-létesítési szolgáltatás nem hoz létre jelentéseket, nem végez elemzést, vagy 30 napon túli elemzéseket biztosít, mivel a szolgáltatás 30 napon belül nem tárolja, dolgozza fel vagy nem tartja meg az adatokból.

### <a name="troubleshoot"></a>Hibaelhárítás

A kiépítés során esetlegesen felmerülő problémák elhárításához tekintse meg a következő cikkeket:

- [Hiba történt a felhasználók Azure AD Gallery-alkalmazásba való konfigurálásának beállításakor](application-provisioning-config-problem.md)
- [Egy attribútum szinkronizálása a helyszíni Active Directory az Azure AD-be az alkalmazásba való kiépítéshez](user-provisioning-sync-attributes-for-mapping.md)
- [Probléma a rendszergazdai hitelesítő adatok mentésekor, miközben a felhasználók üzembe helyezését egy Azure Active Directory Gallery-alkalmazáshoz konfigurálja](./user-provisioning.md)
- [Egyetlen felhasználó sincs kiépítve egy Azure AD Gallery-alkalmazásba](application-provisioning-config-problem-no-users-provisioned.md)
- [Helytelenek a felhasználók egy Azure AD Gallery-alkalmazásban való üzembe helyezése](../manage-apps/add-application-portal-assign-users.md)
- [Windows Eseménynapló beállítása az ügynökhöz – hibaelhárítás](../saas-apps/workday-inbound-tutorial.md#setting-up-windows-event-viewer-for-agent-troubleshooting)
- [Azure Portal naplók beállítása a szolgáltatás hibaelhárításához](../saas-apps/workday-inbound-tutorial.md#setting-up-azure-portal-audit-logs-for-service-troubleshooting)
- [Az AD felhasználói fiók létrehozási műveleteinek naplói](../saas-apps/workday-inbound-tutorial.md#understanding-logs-for-ad-user-account-create-operations)
- [A kezelői frissítési műveletek naplóinak ismertetése](../saas-apps/workday-inbound-tutorial.md#understanding-logs-for-manager-update-operations)
- [Gyakran előforduló hibák elhárítása](../saas-apps/workday-inbound-tutorial.md#resolving-commonly-encountered-errors)

### <a name="next-steps"></a>Következő lépések

- [Kifejezések írása attribútum-hozzárendelésekhez](functions-for-customizing-application-data.md)
- [Az Azure AD szinkronizációs API áttekintése](/graph/api/resources/synchronization-overview)
- [A hatókörön kívüli felhasználói fiókok törlésének kihagyása](skip-out-of-scope-deletions.md)
- [Azure AD Connect kiépítési ügynök: verziók kiadásának előzményei](provisioning-agent-release-version-history.md)