---
title: Azure Container-ajánlat létrehozása – Azure Marketplace
description: Ismerje meg, hogyan hozhat létre és tehet közzé tároló-ajánlatokat az Azure Marketplace-en.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: keferna
ms.author: keferna
ms.date: 06/17/2020
ms.openlocfilehash: bcb334074d842fa5c35e4619f1c5d654405388a7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96459568"
---
# <a name="create-an-azure-container-offer-in-azure-marketplace"></a>Azure-tárolóra vonatkozó ajánlat létrehozása az Azure Marketplace-en

Ez a cikk bemutatja, hogyan hozhat létre és tehet közzé egy tároló-ajánlatot az Azure Marketplace-en. A Kezdés előtt [hozzon létre egy kereskedelmi Piactéri fiókot a partner Centerben](./partner-center-portal/create-account.md) , ha még nem tette meg. Győződjön meg róla, hogy a fiókja regisztrálva van a kereskedelmi piactér programban.

## <a name="create-a-new-offer"></a>Új ajánlat létrehozása

1. Jelentkezzen be a [partner központba](https://partner.microsoft.com/dashboard/home).

2. A bal oldali navigációs menüben válassza a **kereskedelmi piactér**  >  **– Áttekintés** lehetőséget.

3. Az Áttekintés lapon válassza az **+ új ajánlat**  >  **Azure-tároló** elemet.

   ![A bal oldali navigációs menü ábrázolása.](./partner-center-portal/media/new-offer-azure-container.png)

> [!TIP]
> Az ajánlat közzétételét követően a csak az ajánlat újbóli közzététele után az online áruházakban megjelenő szerkesztési lehetőségek jelennek meg a partner Centerben. Győződjön meg arról, hogy a módosítások végrehajtása után mindig újra közzé kell tennie a módosításokat.

### <a name="offer-id-and-alias"></a>Ajánlat azonosítója és alias

Adja meg az **ajánlat azonosítóját**. Ez a fiókban található egyes ajánlatok egyedi azonosítója.

- Ez az azonosító látható az ügyfelek számára a Piactéri ajánlathoz tartozó webcímek és Azure Resource Manager sablonok esetében, ha van ilyen.
- Csak kisbetűket és számokat használjon. Tartalmazhat kötőjeleket és aláhúzásokat, de nem tartalmazhat szóközt, és legfeljebb 50 karakter hosszú lehet. Ha például a **test-Offer-1** értéket adja meg, az ajánlat webes címe lesz `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
- Az ajánlat azonosítója a **Létrehozás** gombra kattintva nem módosítható.

Adjon meg egy **ajánlat-aliast**. Ez a partner Centerben az ajánlathoz használt név.

- Ez a név nem használatos a piactéren, és nem egyezik meg az ajánlat nevével és az ügyfelek számára megjelenített egyéb értékekkel.
- Ez a **Létrehozás** gombra kattintva nem módosítható.

Válassza a **Létrehozás** lehetőséget az ajánlat létrehozásához és a folytatáshoz.

## <a name="offer-overview"></a>Ajánlat áttekintése

Az **ajánlat áttekintése** oldalon láthatók az ajánlat közzétételéhez szükséges lépések (a befejezett és a közelgő), valamint az egyes lépések befejezésének időtartamát.

Ezen az oldalon különböző hivatkozások jelennek meg az ajánlat aktuális állapota alapján. Például:

- Ha az ajánlat Piszkozat-törlési ajánlat
- Ha az ajánlat élő – [az ajánlat értékesítésének leállítása](./partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan)
- Ha az ajánlat előzetes verzióban érhető [el](review-publish-offer.md#previewing-and-approving-your-offer)
- Ha még nem fejezte be a közzétevő kijelentkezését – a [Közzététel megszakítása](review-publish-offer.md#cancel-publishing) folyamatban van.

## <a name="offer-setup"></a>Ajánlat beállítása

Az ajánlat beállításához kövesse az alábbi lépéseket.

### <a name="customer-leads--optional"></a>Ügyfél-érdeklődők – nem kötelező

Ha az ajánlatot a kereskedelmi piactéren a partner centerrel teszi közzé, akkor azt az Ügyfélkapcsolat-kezelési (CRM) rendszerhez is összekapcsolhatjuk. Ez lehetővé teszi az ügyfelek kapcsolattartási adatainak megszerzését, amint valaki érdeklődik a termék iránt, vagy használja a terméket.

1. **Válassza ki a kívánt érdeklődő célhelyet, ahová az ügyfél-érdeklődőket küldeni szeretné**. A partner Center a következő CRM-rendszereket támogatja:

   - [Dynamics 365](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) ügyfél-engagement
   - [Marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md)
   - [Salesforce](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md)

   > [!NOTE]
   > Ha a CRM-rendszer nem szerepel a fentiekben, az [Azure Table](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md) vagy a [https-végpont](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md) használatával tárolhatja az ügyfelek vezető adatait, majd exportálhatja az adatait a CRM-rendszerbe.

2. Az ajánlat összekötése a vezető célhoz a partner Centerben való közzététel során.
3. Győződjön meg arról, hogy a vezető célhoz való kapcsolódás megfelelően van konfigurálva. Miután közzétette a partner Centerben, érvényesítjük a kapcsolatot, és elküldünk egy tesztelési érdeklődőt. Miközben az ajánlat előzetes verziójának megtekintése előtt megtekinti az ajánlatot, tesztelheti az érdeklődői kapcsolatokat is, ha az előnézeti környezetben próbálja megvásárolni az ajánlatot.
4. Győződjön meg arról, hogy a vezető célhely kapcsolata frissül, így nem veszíti el az érdeklődőket.

Íme néhány további érdeklődő felügyeleti erőforrás:

- [Érdeklődők a kereskedelmi piactéri ajánlattól](./partner-center-portal/commercial-marketplace-get-customer-leads.md)
- [Az érdeklődők felügyeletével kapcsolatos gyakori kérdések](lead-management-faq.md#common-questions-about-lead-management)
- [Az érdeklődők konfigurálásával kapcsolatos hibák elhárítása](lead-management-faq.md#publishing-config-errors)
- Az [érdeklődők kezelése – áttekintés](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (ellenőrizze, hogy ki van-e kapcsolva az előugró ablakok blokkolása).

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

### <a name="properties"></a>Tulajdonságok

Ezen a lapon megadhatja az ajánlatnak a piactéren való csoportosításához használt kategóriákat, valamint az ajánlatot támogató jogi szerződéseket.

#### <a name="category"></a>Kategória

Válassza ki a kategóriákat és alkategóriákat, hogy az ajánlatot a piactér megfelelő keresési területein helyezze el. Ügyeljen arra, hogy az ajánlata milyen módon támogassa ezeket a kategóriákat az ajánlat leírásában. A következők szerint válasszon:

- Legalább egy és legfeljebb két kategória, beleértve az elsődleges és a másodlagos kategóriát is (opcionális).
- Legfeljebb két alkategória lehet minden elsődleges és/vagy másodlagos kategóriához. Ha nem alkalmazható alkategória az ajánlatra, válassza a **nem alkalmazható** lehetőséget.

Tekintse meg a kategóriák és alkategóriák teljes listáját az [ajánlatban az ajánlott eljárásokat felsorolva](gtm-offer-listing-best-practices.md). A tárolók mindig **a tárolók alatt,** majd a **tároló lemezképek** kategóriájában jelennek meg.

#### <a name="legal"></a>Jogi tudnivalók

Meg kell adnia az ajánlat használati feltételeit. Két lehetőség érhető el:

- A Microsoft kereskedelmi piactér standard szerződését használhatja.
- Adja meg a saját használati feltételeit.

#### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Standard szintű szerződés a Microsoft kereskedelmi piactérről

A kereskedelmi piactéren a tranzakciókat megkönnyítő standard szintű szerződési sablon nyújt segítséget. Dönthet úgy is, hogy a standard szerződés keretében kínálja a megoldását, amelyet az ügyfeleknek csak egyszer kell ellenőriznie és elfogadni. Ez jó megoldás, ha nem szeretne egyéni használati feltételeket létrehozni.

A standard szintű szerződéssel kapcsolatos további tudnivalókért tekintse meg [a Microsoft kereskedelmi piactérre vonatkozó standard szintű szerződést](standard-contract.md). Letöltheti a [standard szerződés](https://go.microsoft.com/fwlink/?linkid=2041178) PDF-fájlját is (Győződjön meg róla, hogy az előugró ablakok blokkolása ki van kapcsolva).

A standard szintű szerződés használatához válassza a * * standard szerződést a Microsoft kereskedelmi piactéren] (.. /standard-contract.md)

> [!NOTE]
> Miután közzétette az ajánlatot a Microsoft kereskedelmi piactérre vonatkozó standard szerződéssel, nem használhatja a saját egyéni használati feltételeit. A standard szerződés keretében vagy a saját használati feltételeinek megfelelően kínálja megoldását.

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-1-standard-contract.png" alt-text="Bemutatja a Microsoft kereskedelmi Marketplace-re vonatkozó szabványos szerződés használatát jelölőnégyzetet.":::

##### <a name="your-own-terms-and-conditions"></a>A saját használati feltételei

A saját használati feltételeinek megadásához adja meg azokat a **feltételek és kikötések** mezőben. Ebben a mezőben korlátlan számú karakterláncot adhat meg. Az ügyfeleknek el kell fogadniuk ezeket a feltételeket, mielőtt kipróbálhatják az ajánlatot.

Válassza a **Piszkozat mentése** lehetőséget, mielőtt továbblépne a következő szakaszra.

## <a name="offer-listing"></a>Ajánlati lista

Ezen a lapon megadhatja a kereskedelmi piactéren megjelenő ajánlat részleteit. Ez magában foglalja az ajánlat nevét, leírását és lemezképeit.

> [!NOTE]
> Az ajánlat részletei nem szükségesek angolul, ha az ajánlat leírása a következő kifejezéssel kezdődik: "Ez az alkalmazás csak a [nem angol nyelven] érhető el." Azt is megteheti, hogy hasznos hivatkozást biztosít, hogy olyan nyelven kínálja a tartalmat, amely különbözik az ajánlati lista részleteiben használt nyelvtől.

### <a name="name"></a>Name

Az itt megadott név az ajánlat címeként jelenik meg. Ez a mező előre ki van töltve az ajánlat- **alias** mezőben megadott szöveggel az ajánlat létrehozásakor. A nevet később módosíthatja.

A név:

- Védjeggyel rendelkezhet (és a védjegyek és a szerzői jogi szimbólumok is szerepelhetnek).
- Nem lehet hosszabb 50 karakternél.
- Nem szerepelhetnek hangulatjelek.

### <a name="search-results-summary"></a>Keresési eredmények összegzése

Az ajánlat rövid leírása. Ez akár 100 karakter hosszú lehet, és a piactér keresési eredményeiben használható.

### <a name="long-summary"></a>Hosszú összefoglalás

Az ajánlat részletesebb leírása. Ez akár 256 karakter hosszú lehet, és a piactér keresési eredményeiben használható.

### <a name="description"></a>Description

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

#### <a name="privacy-policy-link"></a>Adatvédelmi szabályzat hivatkozása

Adja meg a szervezete adatvédelmi szabályzatának webes címeit. Ön felelős annak biztosításáért, hogy az ajánlat megfelel az adatvédelmi törvényeknek és előírásoknak. Ön is felelős az érvényes adatvédelmi szabályzatok közzétételéhez a webhelyén.

#### <a name="useful-links"></a>Hasznos hivatkozások

Adja meg az ajánlatával kapcsolatos kiegészítő online dokumentumokat. Legfeljebb 25 hivatkozást adhat hozzá. Hivatkozás hozzáadásához válassza a **+ hivatkozás hozzáadása** lehetőséget, majd hajtsa végre a következő mezőket:

- **Title** – az ügyfelek ezt az ajánlat részletek lapján láthatják.
- **Hivatkozás (URL)** – adjon meg egy hivatkozást az ügyfelek számára az online dokumentum megtekintéséhez. A hivatkozásnak a http://vagy a https://értékkel kell kezdődnie.

### <a name="contact-information"></a>Kapcsolattartási adatok

Meg kell adnia egy **támogatási kapcsolattartó** nevét, e-mail-címét és telefonszámát, valamint egy **mérnöki kapcsolattartót**. Ez az információ nem jelenik meg az ügyfelek számára, de a Microsoft számára elérhető. Emellett a Cloud Solution Provider (CSP) partnerei is megadhatók.

- Támogatási kapcsolattartó (kötelező): általános támogatási kérdések.
- Engineering Contact (kötelező): technikai kérdések és minősítési problémák esetén.
- CSP programbeli kapcsolat (nem kötelező): a CSP-programhoz kapcsolódó viszonteladói kérdésekért.

A **támogatási kapcsolattartó** szakaszban adja meg azt a **támogatási webhelyet** , ahol a partnerek támogatást kaphatnak az ajánlathoz, attól függően, hogy az ajánlat elérhető-e a globális Azure-ban, Azure Government vagy mindkettőben.

A **CSP-program elérhetősége** szakaszban adja meg a hivatkozást (**CSP-program marketing-anyagok**), ahol a CSP-partnerek megtalálhatják az ajánlathoz tartozó értékesítési anyagokat.

#### <a name="additional-marketplace-listing-resources"></a>További erőforrások listázása a piactéren

Az ajánlatok listájának létrehozásával kapcsolatos további tudnivalókért tekintse meg az ajánlott [eljárások listáját](gtm-offer-listing-best-practices.md) ismertető témakört.

### <a name="marketplace-images"></a>Marketplace-rendszerképek

Adja meg az ajánlathoz használni kívánt emblémákat és képeket. Minden képnek PNG formátumúnak kell lennie. A rendszer elutasítja a homályos képeket.

[!INCLUDE [logo tips](./includes/graphics-suggestions.md)]

>[!Note]
>Ha probléma merül fel a fájlok feltöltésekor, győződjön meg arról, hogy a helyi hálózat nem blokkolja a https://upload.xboxlive.com partner központ által használt szolgáltatást.

#### <a name="store-logos"></a>Emblémák tárolása

Adjon meg egy PNG-fájlt a **nagy** méretű emblémához. A partner Center ezt fogja használni egy **kis** és **közepes** embléma létrehozásához. Ezeket később is lecserélheti a különböző rendszerképekre.

- **Nagyméretű** (216 x 216 és 350 x 350 px, kötelező)
- **Közepes** (90 x 90 px, nem kötelező)
- **Kicsi** (48 x 48 px, opcionális)

Ezeket az emblémákat a lista különböző helyein használják:

[!INCLUDE [logos-azure-marketplace-only](./includes/logos-azure-marketplace-only.md)]

[!INCLUDE [Logo tips](./includes/graphics-suggestions.md)]

#### <a name="screenshots-optional"></a>Képernyőképek (nem kötelező)

Akár öt képernyőképet is felvehet, amelyek bemutatják, hogyan működik az ajánlata. Mindegyiknek 1280 x 720 képpont méretűnek és PNG formátumúnak kell lennie.

#### <a name="videos-optional"></a>Videók (nem kötelező)

Akár öt videót is hozzáadhat, amelyek bemutatják az ajánlatát. Adja meg a videó nevét, a webcímet és a videó miniatűr PNG-képét, amely 1280 x 720 képpont méretű.

#### <a name="offer-examples"></a>Ajánlatpéldák

Az alábbi példák azt mutatják be, hogyan jelennek meg az ajánlat-listaelemek mezői az ajánlat különböző helyein.

Ez az Azure Marketplace **ajánlati lista** lapját jeleníti meg:

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-6-offer-listing-mkt-plc.png" alt-text="Az ajánlati lista oldalát szemlélteti az Azure piactéren." :::

Ez a keresési eredményeket mutatja az Azure Marketplace-en:

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-7-search-results-mkt-plc.png" alt-text="A keresési eredményeket szemlélteti az Azure Marketplace-en.":::

Itt látható az **ajánlati lista** lapja Azure Portal:

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-8-offer-listing-portal.png" alt-text="Az ajánlati lista oldalát mutatja Azure Portalban.":::

Ez a Azure Portal keresési eredményeit jeleníti meg:

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-9-search-results-portal.png" alt-text="A keresési eredményeket mutatja Azure Portalban.":::

## <a name="preview"></a>Előnézet

Az Előnézet lapon kiválaszthatja az ajánlat érvényességének ellenőrzéséhez szükséges korlátozott **előzetes** verziót, mielőtt közzéteszi a szolgáltatást.

> [!IMPORTANT]
> Miután megtekintette az ajánlatát **előzetes** verzióban, ki kell választania az **élő** adás lehetőséget az ajánlat nyilvános közzétételéhez.

Adja meg az előnézeti célközönséget az Azure-előfizetési azonosító GUID azonosítójának használatával, valamint az egyes verziók opcionális leírását. Ezen mezők egyikét sem láthatja az ügyfelek.

> [!NOTE]
> Az Azure-előfizetési azonosítót a Azure Portal előfizetések oldalán találja.

Adjon hozzá legalább egy Azure-előfizetési azonosítót egyenként (legfeljebb 10) vagy egy CSV-fájl feltöltésével (legfeljebb 100). Az előfizetés-azonosítók hozzáadásával meghatározhatja, hogy ki tekintheti meg az ajánlatot az élő közzététel előtt. Ha az ajánlat már aktív, kiválaszthatja az előnézeti közönséget az ajánlat változásainak és frissítéseinek teszteléséhez.

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

## <a name="plan-overview"></a>A terv áttekintése

Ezen a lapon különböző csomag-beállításokat adhat meg ugyanazon az ajánlaton belül. A (korábban SKU-nak nevezett) csomagok eltérhetnek az elérhető felhőktől, például a globális felhőktől, a kormányzati felhőktől és a csomag által hivatkozott képpel. Ha ajánlatát szeretné kilistázni a kereskedelmi piactéren, be kell állítania legalább egy csomagot.

Az egyes ajánlatokhoz legfeljebb 100 csomagot hozhat létre: ezek közül legfeljebb 45 lehet privát. További információ a [Microsoft kereskedelmi piactéren](private-offers.md)elérhető privát csomagokról.

A csomagok létrehozása után a **terv áttekintése** lap a következőket jeleníti meg:

- Csomag neve
- Díjszabási modell
- Azure-régiók (globális vagy kormányzati)
- Jelenlegi közzétételi állapot
- Bármely elérhető művelet

A terv áttekintésében elérhető műveletek a csomag aktuális állapotától függően változnak. Ezek közé tartoznak például az alábbiak:

- **Piszkozat törlése** – ha a terv állapota vázlat.
- **Eladási terv leállítása** – ha a csomag állapota élőben van közzétéve.

### <a name="create-new-plan"></a>Új csomag létrehozása

Válassza az **új csomag létrehozása** lehetőséget. Megjelenik az **új terv** párbeszédpanel.

A **terv azonosítója** mezőben hozzon létre egy egyedi díjcsomag-azonosítót az ajánlat minden egyes csomaghoz. Ez az azonosító a termék webcímen lévő ügyfelek számára látható lesz. Csak kisbetűket és számokat, kötőjeleket vagy aláhúzásokat, illetve legfeljebb 50 karaktert használjon.

> [!NOTE]
> A terv azonosítója a **Létrehozás** gombra kattintva nem módosítható.

A **terv neve** mezőbe írja be a csomag nevét. Az ügyfelek ezt a nevet látják, amikor dönti el, hogy melyik tervet szeretné kiválasztani az ajánlaton belül. Minden csomaghoz hozzon létre egyedi nevet az ajánlatban. Használhatja például a Windows Server-ajánlat nevét a **Windows server 2016** **és a** **Windows Server 2019** csomaggal.

### <a name="plan-setup"></a>Csomag beállítása

Ezen a lapon kiválaszthatja, hogy a terv mely felhőknél érhető el. Az ezen a lapon megjelenő válaszok hatással vannak arra, hogy mely mezők jelenjenek meg más lapokon.

#### <a name="azure-regions"></a>Azure-régiók

Az Azure-beli tárolók ajánlatait az **Azure Global**-ban automatikusan elérhetővé kell tenni.  A tervet a kereskedelmi piactért használó összes globális Azure-régióban használhatja. Részletekért lásd: a [földrajzi Elérhetőség és a pénznemek támogatása](marketplace-geo-availability-currencies.md).

Válassza a [Azure Government](../azure-government/documentation-government-welcome.md) lehetőséget a megoldás megjelenítéséhez. Ez egy kormányzati közösségi felhő, amely az Egyesült Államok szövetségi, állami és helyi vagy törzsi szerveinek ügyfeleinek, valamint az azok kiszolgálására jogosult partnereknek szóló szabályozott hozzáféréssel rendelkezik. Közzétevőként Ön felelős a felhőalapú Közösség megfelelőségi ellenőrzésével, biztonsági intézkedéseivel és ajánlott eljárásaival. A Azure Government fizikailag elkülönített adatközpontokat és hálózatokat használ (csak az Egyesült Államokban található). A Azure Government való [Közzététel](../azure-government/documentation-government-manage-marketplace-partners.md) előtt tesztelje és erősítse meg a megoldást az adott területen belül, mivel az eredmények eltérőek lehetnek. A megoldás létrehozásához és teszteléséhez kérjen egy próbaverziós fiókot [Microsoft Azure Government próbaverzióból](https://azure.microsoft.com/global-infrastructure/government/request/).

> [!NOTE]
> Miután közzétette és elérhetővé tette a csomagot egy adott régióban, nem távolíthatja el a régiót.

#### <a name="azure-government-certifications"></a>Azure Government minősítések

Ez a beállítás csak akkor látható, ha **Azure Government** van kiválasztva az **Azure-régiókban**.

Azure Government szolgáltatások kezelik bizonyos kormányzati előírások és követelmények hatálya alá eső adatmennyiséget. Például: FedRAMP, NIST 800,171 (DIB), ITAR, IRS 1075, DoD L4 és CJIS.

A programok minősítésének megjelenítéséhez akár 100 hivatkozást is megadhat, amely leírja azokat. Ezek a program közvetlenül vagy a saját webhelyére mutató hivatkozásokat is magukban foglalhatnak. Ezek a hivatkozások csak Azure Government ügyfelek számára láthatók.

### <a name="plan-listing"></a>Csomag listázása

Ezen a lapon az aktuális ajánlaton belüli különböző csomagokra vonatkozó információk láthatók.

### <a name="plan-name"></a>Csomag neve

Ez előre ki van töltve azzal a névvel, amelyet a tervének létrehozásakor adott meg. Igény szerint módosíthatja ezt a nevet. Akár 50 karakter hosszú lehet. Ez a név jelenik meg a terv címében az Azure Marketplace-en és a Azure Portalban. A rendszer az alapértelmezett modul neveként használja, miután a terv készen áll a használatra.

### <a name="plan-summary"></a>Csomag összegzése

A szoftver csomagjának rövid összefoglalása (nem az ajánlat). Ez az összefoglalás az Azure Marketplace keresési eredményei között jelenik meg, és akár 100 karaktert is tartalmazhat.

### <a name="plan-description"></a>Csomag leírása

Írja le, hogy mi teszi ezt a csomagot egyedivé, valamint az ajánlaton belüli csomagok közötti különbségeket. Ne írja le az ajánlatot, csak a csomagot. Ez a leírás az Azure Marketplace-en és az **ajánlati lista** lapjának Azure Portal fog megjelenni. Ez lehet ugyanaz a tartalom, amelyet a csomag összefoglalásában megadott, és legfeljebb 2 000 karaktert tartalmazhat.

A mezők befejezése után válassza a **Mentés** lehetőséget.

#### <a name="plan-examples"></a>Példák tervezése

Az alábbi példák bemutatják, hogyan jelennek meg a díjcsomag mezői különböző nézetekben.

A terv részleteinek megtekintésekor ezek a mezők az Azure Marketplace-en:

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-10-plan-details-mtplc.png" alt-text="A terv részleteinek az Azure Marketplace-en való megtekintésekor megjelenő mezőket mutatja be.":::

Ezek a Azure Portal terv részletei:

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-11-plan-details-portal.png" alt-text="A Azure Portal tervének részleteit mutatja be.":::

### <a name="plan-availability"></a>A rendelkezésre állás megtervezése

Ha el szeretné rejteni a közzétett ajánlatot, így az ügyfelek nem kereshetik meg, nem tallózhatják vagy nem vásárolhatják meg a piactéren, a **rendelkezésre állás** lapon jelölje be a **terv elrejtése** jelölőnégyzetet.

Ezt a mezőt a következő esetekben használja a rendszer:

- Az ajánlat olyan közvetve használható, amikor egy másik alkalmazás hivatkozik rá.
- Az ajánlatot nem szabad egyenként megvásárolni.
- A tervet a kezdeti teszteléshez használták, és már nem releváns.
- A csomag ideiglenes vagy szezonális ajánlatokhoz lett felhasználva, és a továbbiakban nem ajánlott.

## <a name="technical-configuration"></a>Technikai konfiguráció

A tároló lemezképeit privát [Azure Container Registryban](https://azure.microsoft.com/services/container-registry/)kell tárolni. A **technikai konfiguráció** lapon adja meg a tároló rendszerkép-tárházának hivatkozási adatait a Azure Container Registryon belül.

Az ajánlat közzététele után a rendszer átmásolja a tároló képét az Azure Marketplace-re egy adott nyilvános tároló beállításjegyzékében. A rendszer minden, a tároló képét használó kérelmet az Azure piactér nyilvános tárolójának beállításjegyzékében, nem pedig a saját privát szolgáltatásában szolgál ki. Részletekért lásd: [Az Azure Container műszaki eszközeinek előkészítése](create-azure-container-technical-assets.md).

### <a name="image-repository-details"></a>Rendszerkép-tárház részletei

Adja meg a következő információkat a **rendszerkép-tárház részletei** lapon.

**Azure-előfizetés azonosítója** – adja meg az előfizetés azonosítóját, ahol a rendszer a használati adatokat tartalmazza, és a szolgáltatások számlázása a tároló képét tartalmazó Azure Container Registry. Ezt az azonosítót a Azure Portal [előfizetések oldalán](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) találja.

**Azure-erőforráscsoport neve** – adja meg az [erőforráscsoport](../azure-resource-manager/management/manage-resource-groups-portal.md) nevét, amely tartalmazza a Azure Container Registry a tároló képével. Az erőforráscsoport számára elérhetőnek kell lennie az előfizetés-AZONOSÍTÓban (fent). A nevet a Azure Portal [erőforráscsoportok](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) lapján találja.

**Azure Container Registry neve** – adja meg a tároló képével rendelkező [Azure Container Registry](../container-registry/container-registry-intro.md) nevét. A tároló-beállításjegyzéknek a korábban megadott Azure-erőforráscsoporthoz kell tartoznia. Csak a beállításjegyzék nevét adja meg, ne a teljes bejelentkezési kiszolgáló nevét. Ügyeljen arra, hogy kihagyja a **azurecr.IO** a névben. A beállításjegyzék neve a Azure Portal [tároló-nyilvántartások lapján](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerRegistry%2Fregistries) található.

**A Azure Container Registry rendszergazdai felhasználóneve** – adja meg a [rendszergazdai felhasználónevet](../container-registry/container-registry-authentication.md#admin-account), amely a tároló képével rendelkező Azure Container Registryhoz van társítva. A felhasználónévnek és a jelszónak meg kell győződnie arról, hogy a vállalata hozzáférhessen a beállításjegyzékhez. A rendszergazdai Felhasználónév és jelszó beszerzéséhez állítsa a **rendszergazdai** jogosultsággal rendelkező tulajdonságot **true** értékre az Azure Command-Line Interface (CLI) használatával. Igény szerint beállíthatja, hogy a **rendszergazda felhasználó** Azure Portal **engedélyezze** .

 :::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-12-update-container-registry-edit.png" alt-text="A tároló-beállításjegyzék frissítése párbeszédpanelt mutatja be.":::

**A Azure Container Registry jelszava** – adja meg a Azure Container Registryhoz társított rendszergazdai Felhasználónév és a tároló képét. A felhasználónévnek és a jelszónak meg kell győződnie arról, hogy a vállalata hozzáférhessen a beállításjegyzékhez. A jelszót a Azure Portal **Container Registry**  >  **hozzáférési kulcsok** vagy az Azure CLI használatával a [show paranccsal](/cli/azure/acr/credential#az-acr-credential-show)kérheti le.

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-13-access-keys.png" alt-text="A hozzáférési kulcs menüjét mutatja be.":::

**A tárház neve a Azure Container Registryon belül**. Adja meg a rendszerképet tartalmazó Azure Container Registry adattár nevét. Adja meg az adattár nevét, amikor a rendszerképet a beállításjegyzékbe küldi. A tárház nevét a [Container Registry](https://azure.microsoft.com/services/container-registry/)  >  **adattárak** lapon találja. További információ: [a Container Registry-Tárházak megtekintése Azure Portalban](../container-registry/container-registry-repositories.md).

> [!NOTE]
> A név beállítása után nem módosítható. Egyedi nevet adjon a fiókjában található összes ajánlathoz.

### <a name="image-tags-for-new-versions-of-your-offer"></a>Képcímkék az ajánlat új verzióihoz

A frissítések közzétételekor az ügyfeleknek képesnek kell lenniük automatikusan beolvasni a frissítéseket az Azure piactéren. Ha nem szeretnék frissíteni, akkor a rendszerkép egy adott verziójával kell tudniuk maradni. Ezt úgy teheti meg, hogy minden alkalommal új képcímkéket ad hozzá, amikor frissítést végez a rendszerképhez.

### <a name="image-tag"></a>Rendszerkép címkéje

Ennek a mezőnek tartalmaznia kell egy **legújabb** címkét, amely a lemezkép legújabb verziójára mutat az összes támogatott platformon. Tartalmaznia kell egy Version címkét is (például a XX. xx. xx-es verziójától kezdődően, ahol XX egy szám). Az ügyfeleknek több platform megcélzásához [jegyzékfájl-címkéket](https://github.com/estesp/manifest-tool) kell használniuk. A jegyzékfájl által hivatkozott összes címkét is fel kell venni, hogy fel lehessen tölteni őket.

Az összes manifest-címkének (kivéve a legújabb címkét) X. Y **-** vagy x. y. Z értékkel kell kezdődnie, ahol az x, y és Z érték egész szám lehet. Ha például egy **legújabb** címke a 1.0.1-Linux-x64, 1.0.1-Linux-arm32 és a 1.0.1-Windows-arm32 értékre mutat, ezt a mezőt fel kell venni a mezőbe. Részletekért lásd: [Az Azure Container műszaki eszközeinek előkészítése](create-azure-container-technical-assets.md).

> [!NOTE]
> Ne feledje, hogy adjon hozzá egy teszt címkét a képhez, hogy a tesztelés során azonosítható legyen a rendszerkép.

## <a name="review-and-publish"></a>Felülvizsgálat és közzététel

Miután elvégezte az összes szükséges szakaszt, elküldheti azt a felülvizsgálat és közzététel lehetőségre.

A portál jobb felső sarkában válassza a **felülvizsgálat és** **Közzététel** lehetőséget.

A felülvizsgálati oldalon a következőket teheti:

- Tekintse meg az ajánlat egyes szakaszainak befejezési állapotát. A közzététel addig nem lehetséges, amíg az ajánlat összes része készként van megjelölve.
  - **Nincs elindítva – nem** indult el, és el kell végezni.
  - **Hiányos** – olyan hibákat tartalmaz, amelyeket meg kell oldania, vagy további információkat kell megadnia. További segítségért tekintse meg a jelen dokumentum korábbi szakaszait.
  - **Complete (kész** ) – az összes kötelezően megadandó, hibák nélküli adattal rendelkezik. Az ajánlat elküldése előtt az ajánlat összes részének teljesnek kell lennie.
- Adja meg a minősítési csapat tesztelési utasításait, hogy az ajánlat megfelelően legyen tesztelve. Továbbá adjon meg az ajánlat megismeréséhez hasznos kiegészítő megjegyzéseket.

Az ajánlat közzétételre való elküldéséhez válassza a **Közzététel** lehetőséget.

Egy e-mailt küldünk, amelyből megtudhatja, hogy az ajánlat előzetes verziója elérhető-e a felülvizsgálathoz és jóváhagyáshoz.

Az ajánlat nyilvános közzétételéhez lépjen a partner Center webhelyre, és válassza a **Go-Live** lehetőséget.

## <a name="next-step"></a>Következő lépés

- [Meglévő ajánlat frissítése a kereskedelmi piactéren](./partner-center-portal/update-existing-offer.md)