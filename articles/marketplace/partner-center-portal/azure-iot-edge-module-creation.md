---
title: Azure IoT Edge modul létrehozása az Azure Marketplace-en a partner centerrel
description: Megtudhatja, hogyan hozhat létre, konfigurálhat és tehet közzé IoT Edge modul-ajánlatokat az Azure piactéren a partner Center használatával.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: keferna
ms.author: keferna
ms.date: 08/07/2020
ms.openlocfilehash: 23af7b3f7cfd5ec0531c5cac73cf8a334ef4aa62
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2021
ms.locfileid: "99096440"
---
# <a name="create-an-iot-edge-module-offer"></a>IoT Edge-modulajánlat létrehozása

Ez a cikk bemutatja, hogyan hozhat létre és tehet közzé egy eszközök internetes hálózata (IoT) Edge-modult az Azure Marketplace-hez. A Kezdés előtt [hozzon létre egy kereskedelmi Piactéri fiókot a partner Centerben](create-account.md) , ha még nem tette meg. Győződjön meg róla, hogy a fiókja regisztrálva van a kereskedelmi piactér programban.

## <a name="create-a-new-offer"></a>Új ajánlat létrehozása

1. Jelentkezzen be a [partner központba](https://partner.microsoft.com/dashboard/home).
2. A bal oldali navigációs menüben válassza a **kereskedelmi piactér**  >  **– Áttekintés** lehetőséget.
3. Az Áttekintés lapon válassza az **+ új ajánlat**  >  **IoT Edge modul** lehetőséget.

    ![A bal oldali navigációs menü ábrázolása.](./media/new-offer-iot-edge.png)

> [!IMPORTANT]
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

Ezen a lapon olyan hivatkozások találhatók, amelyek az ajánlaton végzett műveletekre vonatkozó műveleteket hajtanak végre. Például:

- Ha az ajánlat Piszkozat-törlési ajánlat
- Ha az ajánlat élő – [az ajánlat értékesítésének leállítása](update-existing-offer.md#stop-selling-an-offer-or-plan)
- Ha az ajánlat előzetes verzióban érhető [el](../review-publish-offer.md#previewing-and-approving-your-offer)
- Ha még nem fejezte be a közzétevő kijelentkezését – a [Közzététel megszakítása](../review-publish-offer.md#cancel-publishing) folyamatban van.

## <a name="offer-setup"></a>Ajánlat beállítása

Az ajánlat beállításához kövesse az alábbi lépéseket.

### <a name="customer-leads"></a>Ügyfél-érdeklődők

Ha az ajánlatot a piactéren a partner centerrel teszi közzé, akkor opcionálisan kapcsolódhat az Ügyfélkapcsolat-kezelési (CRM) rendszerhez. Ez lehetővé teszi az ügyfelek kapcsolattartási adatainak megszerzését, amint valaki érdeklődik a termék iránt, vagy használja a terméket.

1. Válasszon egy érdeklődési célt, ahová küldhetjük az érdeklődőket. A partner Center a következő CRM-rendszereket támogatja:

    - [Dynamics 365](commercial-marketplace-lead-management-instructions-dynamics.md) ügyfél-engagement
    - [Marketo](commercial-marketplace-lead-management-instructions-marketo.md)
    - [Salesforce](commercial-marketplace-lead-management-instructions-salesforce.md)

    > [!NOTE]
    > Ha a CRM-rendszer nem szerepel a fentiekben, az [Azure Table](commercial-marketplace-lead-management-instructions-azure-table.md) vagy a [https-végpont](commercial-marketplace-lead-management-instructions-https.md) használatával tárolhatja az ügyfelek vezető adatait, majd exportálhatja az adatait a CRM-rendszerbe.

2. Az ajánlat összekötése a vezető célhoz a partner Centerben való közzététel során.
3. Győződjön meg arról, hogy a vezető célhoz való kapcsolódás megfelelően van konfigurálva. Miután közzétette a partner Centerben, érvényesítjük a kapcsolatot, és elküldünk egy tesztelési érdeklődőt. Miközben az ajánlat előzetes verziójának megtekintése előtt megtekinti az ajánlatot, tesztelheti az érdeklődői kapcsolatokat is, ha az előnézeti környezetben próbálja megvásárolni az ajánlatot.
4. Győződjön meg arról, hogy a vezető célhely kapcsolata frissül, így nem veszíti el az érdeklődőket.

Íme néhány további érdeklődő felügyeleti erőforrás:

- [Érdeklődők a kereskedelmi piactéri ajánlattól](commercial-marketplace-get-customer-leads.md)
- [Az érdeklődők felügyeletével kapcsolatos gyakori kérdések](../lead-management-faq.md#common-questions-about-lead-management)
- [Az érdeklődők konfigurálásával kapcsolatos hibák elhárítása](../lead-management-faq.md#publishing-config-errors)
- Az [érdeklődők kezelése – áttekintés](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (ellenőrizze, hogy ki van-e kapcsolva az előugró ablakok blokkolása).

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

### <a name="properties"></a>Tulajdonságok

Ezen a lapon megadhatja az ajánlatnak a piactéren való csoportosításához használt kategóriákat, valamint az ajánlatot támogató jogi szerződéseket.

#### <a name="category"></a>Kategória

Válassza ki a kategóriákat és alkategóriákat, hogy az ajánlatot a piactér megfelelő keresési területein helyezze el. Ügyeljen arra, hogy az ajánlata milyen módon támogassa ezeket a kategóriákat az ajánlat leírásában. A következők szerint válasszon:

- Legalább egy és legfeljebb két kategória, beleértve az elsődleges és a másodlagos kategóriát is (opcionális).
- Legfeljebb két alkategória lehet minden elsődleges és/vagy másodlagos kategóriához. Ha nem alkalmazható alkategória az ajánlatra, válassza a **nem alkalmazható** lehetőséget.

Tekintse meg a kategóriák és alkategóriák teljes listáját az [ajánlatban az ajánlott eljárásokat felsorolva](../gtm-offer-listing-best-practices.md). A piactéren IoT Edge modulok mindig a  **eszközök internetes hálózata**  >  **IoT Edge modul** kategóriában jelennek meg   .

#### <a name="legal"></a>Jogi tudnivalók

Meg kell adnia az ajánlat használati feltételeit. Erre két lehetősége van:

- A Microsoft kereskedelmi piactér standard szerződését használhatja.
- Adja meg a saját használati feltételeit.

##### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Standard szintű szerződés a Microsoft kereskedelmi piactérről

A kereskedelmi piactéren a tranzakciókat megkönnyítő standard szintű szerződési sablon nyújt segítséget. Dönthet úgy is, hogy a standard szerződés keretében kínálja a megoldását, amelyet az ügyfeleknek csak egyszer kell ellenőriznie és elfogadni. Ez jó megoldás, ha nem szeretne egyéni használati feltételeket használni.

A standard szintű szerződéssel kapcsolatos további tudnivalókért tekintse meg [a Microsoft kereskedelmi piactérre vonatkozó standard szintű szerződést](../standard-contract.md). Letöltheti a [standard szerződés](https://go.microsoft.com/fwlink/?linkid=2041178) PDF-fájlját is (Győződjön meg róla, hogy az előugró ablakok blokkolása ki van kapcsolva).

A normál szerződés használatához jelölje be a **szabványos szerződés használata a Microsoft kereskedelmi piactérhez** jelölőnégyzetet, majd kattintson az **elfogadás** gombra.

> [!NOTE]
> Miután közzétette az ajánlatot a Microsoft kereskedelmi piactérre vonatkozó standard szerződéssel, nem használhatja a saját egyéni használati feltételeit. A standard szerződés keretében vagy a saját használati feltételeinek megfelelően kínálja megoldását.

![A Microsoft kereskedelmi Piactéri szolgáltatásának szabványos szerződését mutatja be.](media//iot-edge-module-standard-contract-checkbox.png)

##### <a name="your-own-terms-and-conditions"></a>A saját használati feltételei

A saját használati feltételeinek megadásához adja meg azokat a **feltételek és kikötések** mezőben. Ebben a mezőben korlátlan számú karakterláncot adhat meg. Az ügyfeleknek el kell fogadniuk ezeket a feltételeket, mielőtt kipróbálhatják az ajánlatot.

Válassza a **Piszkozat mentése** lehetőséget, mielőtt továbblépne a következő szakaszra.

## <a name="offer-listing"></a>Ajánlati lista

Itt megadhatja a piactéren megjelenő ajánlat részleteit. Ebbe beletartozik az ajánlat neve, leírása, képei és így tovább. Ügyeljen arra, hogy az ajánlat konfigurálásakor kövesse a Microsoft szabályzat lapján részletesen ismertetett házirendeket.

> [!NOTE]
> Az ajánlat részleteit nem kötelező angol nyelven megadni, ha az ajánlat leírása a következő kifejezéssel kezdődik: "Ez az alkalmazás csak a [nem angol nyelvű] verzióban érhető el." Azt is megteheti, hogy hasznos hivatkozást biztosít, hogy olyan nyelven kínálja a tartalmat, amely különbözik az ajánlati lista részleteiben használt nyelvtől.

### <a name="name"></a>Name

Az itt megadott név az ajánlat címeként jelenik meg. Ez a mező előre ki van töltve az ajánlat- **alias** mezőben megadott szöveggel az ajánlat létrehozásakor. A nevet később módosíthatja.

A név:

- Védjeggyel is rendelkezhet (és a védjegyek és a szerzői jogi szimbólumok is szerepelhetnek).
- Nem lehet hosszabb 50 karakternél.
- Nem szerepelhetnek hangulatjelek.

### <a name="search-results-summary"></a>Keresési eredmények összegzése

Adja meg az ajánlat rövid leírását. Ez akár 100 karakter hosszú lehet, és a piactér keresési eredményeiben használható.

### <a name="long-summary"></a>Hosszú összefoglalás

Adja meg az ajánlat részletesebb leírását. Ez akár 256 karakter hosszú lehet, és a piactér keresési eredményeiben használható.

### <a name="description"></a>Description

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

IoT Edge modul ajánlatának tartalmaznia kell a minimális hardverkövetelmények bekezdést a Leírás alján, például:

- Minimális hardverkövetelmények: Linux x64 és arm32 OS, 1 GB RAM, 500 MB tárterület

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

#### <a name="privacy-policy-url"></a>Adatvédelmi szabályzat URL-címe

Adja meg a szervezete adatvédelmi szabályzatának webes címeit. Ön felelős annak biztosításáért, hogy az ajánlat megfelel az adatvédelmi törvényeknek és előírásoknak. Ön is felelős az érvényes adatvédelmi szabályzatok közzétételéhez a webhelyén.

#### <a name="useful-links"></a>Hasznos hivatkozások

Adja meg az ajánlatával kapcsolatos kiegészítő online dokumentumokat. Legfeljebb 25 hivatkozást adhat hozzá. Hivatkozás hozzáadásához válassza a **+ hivatkozás hozzáadása** lehetőséget, majd hajtsa végre a következő mezőket:

- **Title** – az ügyfelek az ajánlat részletek lapján láthatják a címet.
- **Hivatkozás (URL)** – adjon meg egy hivatkozást az ügyfelek számára az online dokumentum megtekintéséhez. A hivatkozásnak a vagy a értékkel kell kezdődnie `http://` `https://` .

Ügyeljen arra, hogy legalább egy hivatkozást adjon hozzá a dokumentációhoz, és egy hivatkozást az [Azure IoT-eszköz](https://catalog.azureiotsolutions.com/)kompatibilis IoT Edge eszközeire.

### <a name="contact-information"></a>Kapcsolattartási adatok

Meg kell adnia egy **támogatási kapcsolattartó** nevét, e-mail-címét és telefonszámát, valamint egy **mérnöki kapcsolattartót.** Ezek az adatok nem jelennek meg az ügyfelek számára. A Microsoft számára elérhető, és a Cloud Solution Provider (CSP) partnerei számára is biztosítható.

- Támogatási kapcsolattartó (kötelező): általános támogatási kérdések.
- Engineering Contact (kötelező): technikai kérdések és minősítési problémák esetén.
- CSP programbeli kapcsolat (nem kötelező): a CSP-programhoz kapcsolódó viszonteladói kérdésekért.

A **támogatási kapcsolattartó** szakaszban adja meg a **támogatási webhely** webcímet, ahol a partnerek az ajánlat támogatását a globális Azure-ban, Azure Governmentban vagy mindkettőben elérhetővé teszik.

A **CSP-program elérhetősége** szakaszban adja meg a hivatkozást (**CSP-program marketing-anyagok**), ahol a CSP-partnerek megtalálhatják az ajánlathoz tartozó értékesítési anyagokat.

#### <a name="additional-marketplace-listing-resources"></a>További erőforrások listázása a piactéren

Az ajánlatok listájának létrehozásával kapcsolatos további tudnivalókért tekintse meg az ajánlott [eljárások listáját](../gtm-offer-listing-best-practices.md).

### <a name="marketplace-images"></a>Marketplace-rendszerképek

Adja meg az ajánlathoz használni kívánt emblémákat és képeket. Minden képnek PNG formátumúnak kell lennie. A rendszer elutasítja a homályos képeket.

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

>[!Note]
>Ha probléma merül fel a fájlok feltöltésekor, győződjön meg arról, hogy a helyi hálózat nem blokkolja a https://upload.xboxlive.com partner központ által használt szolgáltatást.

#### <a name="store-logos"></a>Emblémák tárolása

Adjon meg egy PNG-fájlt a **nagy** méretű emblémához. A partner Center ezt fogja használni egy **kis** és **közepes** embléma létrehozásához. Ezeket később is lecserélheti a különböző rendszerképekre.

- **Nagyméretű** (216 x 216 és 350 x 350 px, kötelező)
- **Közepes** (90 x 90 px, nem kötelező)
- **Kicsi** (48 x 48 px, opcionális)

Ezeket az emblémákat a lista különböző helyein használják:

[!INCLUDE [logos-azure-marketplace-only](../includes/logos-azure-marketplace-only.md)]

[!INCLUDE [Logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots-optional"></a>Képernyőképek (nem kötelező)

Akár öt képernyőképet is felvehet, amelyek bemutatják, hogyan működik az ajánlata. Mindegyiknek 1280 x 720 képpont méretűnek és PNG formátumúnak kell lennie.

#### <a name="videos-optional"></a>Videók (nem kötelező)

Akár öt videót is hozzáadhat, amelyek bemutatják az ajánlatát. Adja meg a videó nevét, a webcímet és a videó miniatűr PNG-képét, amely 1280 x 720 képpont méretű.

#### <a name="marketplace--examples"></a>Marketplace-példák

Az alábbi példa bemutatja, hogyan jelennek meg az ajánlati információk az Azure Marketplace piactéren:

:::image type="content" source="media/example-iot-azure-marketplace-offer.png" alt-text="Bemutatja, hogyan jelenik meg az ajánlat az Azure piactéren.":::

#### <a name="call-out-descriptions"></a>Lehívási leírások

1. Nagyméretű embléma
2. Kategóriák
3. Támogatási címe (hivatkozás)
4. használati feltételei
5. Adatvédelmi szabályzat címe (hivatkozás)
6. Name
7. Összefoglalás
8. Description
9. Hasznos hivatkozások
10. Képernyőképek/videók

<br>Az alábbi példa bemutatja, hogyan jelennek meg az ajánlati információk az Azure Marketplace keresési eredményei között:

:::image type="content" source="media/example-iot-azure-marketplace-offer-search-results.png" alt-text="Bemutatja, hogyan jelenik meg az ajánlat az Azure piactér keresési eredményei között.":::

#### <a name="call-out-descriptions"></a>Lehívási leírások

1. Kis embléma
2. Ajánlat neve
3. Keresési eredmények összegzése

<br>Az alábbi példa bemutatja, hogyan jelennek meg az ajánlati információk a Azure Portalban:

:::image type="content" source="media/example-iot-azure-portal-offer.png" alt-text="Bemutatja, hogyan jelennek meg az ajánlat a Azure Portalban.":::

#### <a name="call-out-descriptions"></a>Lehívási leírások

1. Név
2. Leírás
3. Hasznos hivatkozások
4. Képernyőképek

<br>Az alábbi példa bemutatja, hogyan jelennek meg az ajánlati információk a Azure Portal keresési eredmények között:

:::image type="content" source="media/example-iot-azure-portal-offer-search-results.png" alt-text="Bemutatja, hogyan jelenik meg az ajánlat a Azure Portal keresési eredmények között.":::

#### <a name="call-out-descriptions"></a>Lehívási leírások

1. Kis embléma
2. Ajánlat neve
3. Keresési eredmények összegzése

<br>A következő szakaszra való továbblépés előtt válassza a **Piszkozat mentése** lehetőséget, és tekintse meg az előnézetet.

## <a name="preview"></a>Előnézet

Az **Előnézet lapon** kiválaszthatja, hogy az ajánlat érvényességét csak korlátozott **előzetes** verzióval kívánja-e közzétenni, mielőtt közzéteszi azt a piactéren elérhető szélesebb közönség számára.

> [!IMPORTANT]
> Miután megtekintette az ajánlatát előzetes verzióban, ki kell választania az **élő** adás lehetőséget az ajánlat nyilvános közzétételéhez.

Adja meg az előnézeti célközönséget az Azure-előfizetési azonosító GUID azonosítójának használatával, valamint az egyes verziók opcionális leírását. Ezen mezők egyikét sem láthatja az ügyfelek.

> [!NOTE]
> Az Azure-előfizetés AZONOSÍTÓját a Azure Portal előfizetések oldalán találja.

Adjon hozzá legalább egy Azure-előfizetési azonosítót egyenként (legfeljebb 10) vagy egy CSV-fájl feltöltésével (legfeljebb 100). Az előfizetés-azonosítók hozzáadásával meghatározhatja, hogy ki tekintheti meg az ajánlatot az élő közzététel előtt. Ha az ajánlat már élő, megadhatja az előnézeti közönséget az ajánlat változásainak és frissítéseinek teszteléséhez.

A következő szakaszra való továbblépés előtt válassza a **Piszkozat mentése** lehetőséget, majd a terv áttekintését.

## <a name="plan-overview"></a>A terv áttekintése

Ezen a lapon különböző csomag-beállításokat adhat meg a partner Center ugyanazon ajánlatán belül. A (korábban SKU-nak nevezett) csomagok eltérhetnek az elérhető felhőktől, például a globális felhőktől, a kormányzati felhőktől és a csomag által hivatkozott képpel. Az ajánlat a piactéren való listázásához be kell állítania legalább egy csomagot.

Az egyes ajánlatokhoz legfeljebb 100 csomagot hozhat létre: ezek közül legfeljebb 45 lehet privát. További információ a [Microsoft kereskedelmi piactéren](../private-offers.md)elérhető privát csomagokról.

A csomagok létrehozása után a **terv áttekintése** lap a következőket jeleníti meg:

- Csomag neve
- Díjszabási modell
- Azure-régiók (globális vagy kormányzati)
- Jelenlegi közzétételi állapot
- Bármely elérhető művelet

A terv áttekintésében elérhető műveletek a csomag aktuális állapotától függően változnak. Ezek közé tartoznak például az alábbiak:

- **Piszkozat törlése**: Ha a terv állapota vázlat.
- **Eladási terv leállítása**: Ha a csomag állapota élőben van közzétéve.

### <a name="create-new-plan"></a>Új csomag létrehozása

Válassza az **új csomag létrehozása** lehetőséget. Megjelenik az **új terv** párbeszédpanel.

A **terv azonosítója** mezőben hozzon létre egy egyedi díjcsomag-azonosítót az ajánlat minden egyes csomagjában. Ez az azonosító a termék webcímen lévő ügyfelek számára látható lesz. Csak kisbetűket és számokat, kötőjeleket vagy aláhúzásokat, illetve legfeljebb 50 karaktert használjon.

A **terv neve** mezőbe írja be a csomag nevét. Az ügyfelek ezt a nevet látják, amikor dönti el, hogy melyik tervet szeretné kiválasztani az ajánlaton belül. Minden csomaghoz hozzon létre egyedi nevet az ajánlatban. Használhatja például a Windows Server-ajánlat nevét a **Windows server 2016** **és a** **Windows Server 2019** csomaggal.

> [!NOTE]
> A terv azonosítója a **Létrehozás** gombra kattintva nem módosítható.

Válassza a **Létrehozás** lehetőséget.

### <a name="plan-setup"></a>Csomag beállítása

Ezen a lapon konfigurálhatja, hogy a terv mely felhők számára legyen elérhető. Az ezen a lapon megjelenő válaszok hatással vannak arra, hogy mely mezők jelenjenek meg más lapokon.

#### <a name="azure-regions"></a>Azure-régiók

Az IoT Edge modulhoz készült összes csomag automatikusan elérhető az **Azure Global**-ban.  A tervét az ügyfelek használhatják a piactért használó összes globális Azure-régióban. Részletekért lásd: a [földrajzi Elérhetőség és a pénznemek támogatása](../marketplace-geo-availability-currencies.md).

Válassza a [Azure Government](../../azure-government/documentation-government-welcome.md) lehetőséget a megoldás megjelenítéséhez. Ez egy kormányzati közösségi felhő, amely az Egyesült Államok szövetségi, állami és helyi vagy törzsi szerveinek ügyfeleinek, valamint az azok kiszolgálására jogosult partnereknek szóló szabályozott hozzáféréssel rendelkezik. Közzétevőként Ön felelős a felhőalapú Közösség megfelelőségi ellenőrzésével, biztonsági intézkedéseivel és ajánlott eljárásaival. A Azure Government fizikailag elkülönített adatközpontokat és hálózatokat használ (csak az Egyesült Államokban található). A Azure Government való [Közzététel](../../azure-government/documentation-government-manage-marketplace-partners.md) előtt tesztelje és erősítse meg a megoldást az adott területen belül, mivel az eredmények eltérőek lehetnek. A megoldás előkészítéséhez és teszteléséhez kérjen egy próbaverziós fiókot [Microsoft Azure Government próbaverzióból](https://azure.microsoft.com/global-infrastructure/government/request/).

> [!NOTE]
> Miután közzétette és elérhetővé tette a csomagot egy adott régióban, nem távolíthatja el a régiót.

#### <a name="azure-government-certifications"></a>Azure Government minősítések

Ez a beállítás csak akkor látható, ha **Azure Government** az **Azure-régiók** területen van kiválasztva.

Azure Government szolgáltatások kezelik bizonyos kormányzati előírások és követelmények hatálya alá eső adatmennyiséget. Például: FedRAMP, NIST 800,171 (DIB), ITAR, IRS 1075, DoD L4 és CJIS. A programok minősítésének megismeréséhez akár 100 hivatkozást is megadhat, amelyek a minősítéseket írják le. Ezek a program közvetlenül vagy a saját webhelyére mutató hivatkozásokat is magukban foglalhatnak. Ezek a hivatkozások csak Azure Government ügyfelek számára láthatók.

### <a name="plan-listing"></a>Csomag listázása

Ezen a lapon az adott ajánlaton belüli különböző csomagokra vonatkozó információk láthatók.

### <a name="plan-name"></a>Csomag neve

Ez előre ki van töltve azzal a névvel, amelyet a tervének létrehozásakor adott meg. Igény szerint módosíthatja ezt a nevet. Akár 50 karakter hosszú lehet. Ez a név jelenik meg a terv címében az Azure Marketplace-en és a Azure Portal. A rendszer az alapértelmezett modul neveként használja, miután a terv készen áll a használatra.

### <a name="plan-summary"></a>Csomag összegzése

Adja meg a csomag rövid összefoglalását (nem az ajánlatot). Ez az összefoglalás az Azure Marketplace keresési eredményei között jelenik meg, és akár 100 karaktert is tartalmazhat.

### <a name="plan-description"></a>Csomag leírása

Írja le, hogy mi teszi ezt a csomagot egyedivé, valamint az ajánlaton belüli csomagok közötti különbségeket. Ne írja le az ajánlatot, csak a csomagot. Ez a leírás az Azure Marketplace-en és az ajánlati lista lapjának Azure Portal fog megjelenni. Ez lehet ugyanaz a tartalom, amelyet a csomag összefoglalásában megadott, és legfeljebb 2 000 karaktert tartalmazhat.

A mezők befejezése után válassza a **Piszkozat mentése** lehetőséget.

#### <a name="plan-examples"></a>Példák tervezése

Íme egy példa az Azure Marketplace-csomag részleteire (a felsorolt árak kizárólag a tényleges költségekkel kapcsolatosak, és nem tükrözik a tényleges költségeket):

:::image type="content" source="media/example-iot-azure-marketplace-plan.png" alt-text="Bemutatja az Azure Marketplace-csomag részleteit.":::

#### <a name="call-out-descriptions"></a>Lehívási leírások

1. Ajánlat neve
2. Csomag neve
3. Csomag leírása

<br>Íme egy példa a Azure Portal terv részleteire (a felsorolt díjak csak a felhasználási célokra szolgálnak, és nem tükrözik a tényleges költségeket):

:::image type="content" source="media/example-iot-azure-marketplace-plan-details.png" alt-text="A Azure Portal terv részleteit mutatja be.":::

#### <a name="call-out-descriptions"></a>Lehívási leírások

1. Ajánlat neve
2. Csomag neve
3. Csomag leírása

### <a name="availability"></a>Rendelkezésre állás

Ha el szeretné rejteni a közzétett ajánlatot, így az ügyfelek nem kereshetik meg, nem tallózhatják vagy nem vásárolhatják meg a piactéren, a rendelkezésre állás lapon jelölje be a **terv elrejtése** jelölőnégyzetet.

Ezt a mezőt általában a következő esetekben használja a rendszer:

- Az ajánlat kizárólag közvetve használható, ha egy másik alkalmazás hivatkozik rá.
- Az ajánlatot nem szabad egyenként megvásárolni.
- A tervet a kezdeti teszteléshez használták, és már nem releváns.
- A csomag ideiglenes vagy szezonális ajánlatokhoz lett felhasználva, és a továbbiakban nem ajánlott.

## <a name="technical-configuration"></a>Technikai konfiguráció

A **IoT Edge modul** típusa egy adott típusú tároló, amely egy IoT Edge eszközön fut. A **technikai konfiguráció** lapon adja meg a tároló képtárat a [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)belül, valamint olyan konfigurációs beállításokat, amelyek segítségével az ügyfelek könnyedén használhatják a modult.

Az ajánlat közzététele után a rendszer átmásolja IoT Edge-tároló rendszerképét az Azure Marketplace-re egy adott nyilvános tároló-beállításjegyzékben. Az Azure-felhasználók által a modul használatára vonatkozó összes kérelem az Azure piactér nyilvános tárolójának beállításjegyzékében, nem pedig a privát tároló beállításjegyzékében szolgál.

A címkék használatával több platformot is megcélozhat, és a modul-tároló rendszerképének több verzióját is megadhatja. További információ a címkékről és a verziószámozásról: [a IoT Edge modul technikai eszközeinek előkészítése](create-iot-edge-module-asset.md).

### <a name="image-repository-details"></a>Rendszerkép-tárház részletei

A **rendszerkép-tárház részletei** lapon adja meg a következő információkat.

**Válassza ki a rendszerkép forrását**: válassza a **Azure Container Registry** lehetőséget.

**Azure-előfizetési azonosító**: adja meg azt az előfizetés-azonosítót, amelyben az erőforrás-használatot jelenteni kell, és a szolgáltatás számlázása a tároló képét tartalmazó Azure Container Registryért. Ezt az azonosítót a Azure Portal [előfizetések oldalán](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) találja.

**Azure-erőforráscsoport neve**: adja meg az [erőforráscsoport](../../azure-resource-manager/management/manage-resource-groups-portal.md) nevét, amely tartalmazza a Azure Container Registry a tároló képével. Az erőforráscsoport számára elérhetőnek kell lennie az előfizetés-AZONOSÍTÓban (fent). A nevet a Azure Portal [erőforráscsoportok](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) lapján találja.

Az **Azure Container Registry neve**: adja meg annak a [Azure Container Registry](../../container-registry/container-registry-intro.md) a nevét, amely tartalmazza a tároló képét. A tároló-beállításjegyzéknek a korábban megadott Azure-erőforráscsoporthoz kell lennie. Csak a beállításjegyzék nevét adja meg, ne a teljes bejelentkezési kiszolgáló nevét. Ügyeljen arra, hogy kihagyja a **azurecr.IO** a névben. A beállításjegyzék neve a Azure Portal [tároló-nyilvántartások lapján](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerRegistry%2Fregistries) található.

A **Azure Container Registry rendszergazdai felhasználóneve**: adja meg a tároló képével rendelkező Azure Container Registryhoz társított [rendszergazdai felhasználónevet](../../container-registry/container-registry-authentication.md#admin-account)). A felhasználónévnek és a jelszónak meg kell győződnie arról, hogy a vállalata hozzáférhessen a beállításjegyzékhez. A rendszergazdai Felhasználónév és jelszó beszerzéséhez állítsa a **rendszergazdai** jogosultsággal rendelkező tulajdonságot **true** értékre az Azure Command-Line Interface (CLI) használatával. Igény szerint beállíthatja, hogy a **rendszergazda felhasználó** **engedélyezze** a Azure Portal.

:::image type="content" source="media/example-iot-update-container-registry.png" alt-text="A tároló-beállításjegyzék frissítése párbeszédpanelt mutatja be.":::

#### <a name="call-out-description"></a>Visszahívás leírása

1. Rendszergazdai felhasználó

<br>**A Azure Container Registryhoz tartozó jelszó**: adja meg a Azure Container Registryhoz társított rendszergazdai Felhasználónév és a tároló képét. A felhasználónévnek és a jelszónak meg kell győződnie arról, hogy a vállalata hozzáférhessen a beállításjegyzékhez. A jelszót a Azure Portal **Container Registry**  >  **hozzáférési kulcsok** vagy az Azure CLI használatával a [show paranccsal](/cli/azure/acr/credential#az-acr-credential-show) kérheti le.

:::image type="content" source="media/example-iot-access-keys.png" alt-text="A Azure Portal a Hívóbetű képernyőjét mutatja.":::

#### <a name="call-out-descriptions"></a>Lehívási leírások

1. Elérési kulcs
2. Felhasználónév
3. Jelszó

**A tárház neve a Azure Container Registryon belül**. Adja meg a rendszerképet tartalmazó Azure Container Registry adattár nevét. Adja meg az adattár nevét, amikor leküldi a rendszerképet a beállításjegyzékbe. A tárház nevét a [Container Registry](https://azure.microsoft.com/services/container-registry/)  >  **adattárak lapon** találja. További információ: [a Container Registry-Tárházak megtekintése a Azure Portalban](../../container-registry/container-registry-repositories.md). A név beállítása után nem módosítható. Egyedi nevet adjon a fiókjában található összes ajánlathoz.

> [!NOTE]
> Az Edge-modul minősítéséhez nem támogatott a titkosított Azure Container Registry. Azure Container Registry a titkosítás engedélyezése nélkül kell létrehozni.

### <a name="image-tags-for-new-versions-of-your-offer"></a>Képcímkék az ajánlat új verzióihoz

A frissítések közzétételekor az ügyfeleknek képesnek kell lenniük automatikusan beolvasni a frissítéseket az Azure piactéren. Ha nem szeretnék frissíteni, akkor a rendszerkép egy adott verziójával kell tudniuk maradni. Ezt úgy teheti meg, hogy minden alkalommal új képcímkéket ad hozzá, amikor frissítést végez a rendszerképhez.

**Rendszerkép címkéje** Ennek a mezőnek tartalmaznia kell egy **legújabb** címkét, amely a lemezkép legújabb verziójára mutat az összes támogatott platformon. Tartalmaznia kell egy Version címkét is (például a XX. xx. xx-es verziójától kezdődően, ahol XX egy szám). Az ügyfeleknek több platform megcélzásához [jegyzékfájl-címkéket](https://github.com/estesp/manifest-tool) kell használniuk. A jegyzékfájl által hivatkozott összes címkét is fel kell venni, hogy fel lehessen tölteni őket. Az összes manifest-címkének (kivéve a legújabb címkét) X. Y vagy X. Y. Z értékkel kell kezdődnie, ahol az X, Y és Z érték egész szám lehet. Ha például egy legújabb címke a 1.0.1-Linux-x64, 1.0.1-Linux-arm32 és a 1.0.1-Windows-arm32 értékre mutat, ezt a mezőt fel kell venni a mezőbe. A címkékkel és a verziószámozással kapcsolatos részletekért lásd: [a IoT Edge modul technikai eszközeinek előkészítése.](create-iot-edge-module-asset.md)

### <a name="default-deployment-settings-optional"></a>Alapértelmezett központi telepítési beállítások (nem kötelező)

Adja meg a IoT Edge modul üzembe helyezésének leggyakoribb beállításait. Az ügyfelek központi telepítésének optimalizálása azáltal, hogy elindítják a IoT Edge modult az alapértelmezett beállításokkal.

**Alapértelmezett útvonalak**. Az IoT Edge hub a modulok, a IoT Hub és az eszközök közötti kommunikációt kezeli. Megadhat útvonalakat a modulok és a IoT Hub közötti adatbevitelhez és kimenethez, ami lehetővé teszi a rugalmasságot, hogy olyan üzeneteket küldjön, amelyekre szükségük van anélkül, hogy további szolgáltatásokat kellene feldolgoznia az üzenetek feldolgozásához vagy további kód írásához. Az útvonalak név/érték párok használatával vannak kialakítva. Legfeljebb öt alapértelmezett útvonal-nevet határozhat meg, amelyek mindegyike legfeljebb 512 karakter hosszú lehet.

Ügyeljen arra, hogy a megfelelő [útvonal-szintaxist](../../iot-edge/module-composition.md#declare-routes)használja az útvonal értékeként (ez általában a következő:/üzenet/* a $upstream). Ez azt jelenti, hogy a modulok által küldött összes üzenet a IoT Hub. A modulra való hivatkozáshoz használja az alapértelmezett modul nevét, amely az **ajánlat neve**, szóközök vagy speciális karakterek nélkül. Ha más, még nem ismert modulokra szeretne hivatkozni, használja a <FROM_MODULE_NAME>-konvenciót, hogy az ügyfelek tudják, hogy frissíteniük kell ezt az információt. IoT Edge útvonalakkal kapcsolatos további információkért lásd: [útvonalak deklarálása](../../iot-edge/module-composition.md#declare-routes)).

Ha például a modul ContosoModule figyeli a ContosoInput és a kimeneti adatok bemeneteit a ContosoOutput-on, érdemes megadnia a következő két alapértelmezett útvonalat:

- Név #1: ToContosoModule
- Érték #1: FROM/messages/modules/<FROM_MODULE_NAME>/outputs/* INTO BrokeredEndpoint ("/modules/ContosoModule/inputs/ContosoInput")
- Név #2: FromContosoModuleToCloud
- Érték #2:/messages/modules/ContonsoModule/outputs/ContosoOutput-ből $upstream

**Alapértelmezett modul – Twin kívánt tulajdonságok**. A Twin modul egy JSON-dokumentum a IoT Hubban, amely egy modul-példány állapotinformációkat tárolja, beleértve a kívánt tulajdonságokat is. A kívánt tulajdonságok a jelentett tulajdonságokkal együtt használhatók a modul konfigurációjának vagy feltételeinek szinkronizálásához. A megoldás háttere beállíthatja a kívánt tulajdonságokat, a modul pedig elolvashatja őket. A modul a kívánt tulajdonságok között is fogadhatja a módosítási értesítéseket. A kívánt tulajdonságok legfeljebb öt név/érték párok használatával jönnek létre, és minden alapértelmezett értéknek 512 karakternél rövidebbnek kell lennie. Legfeljebb öt név/érték Twin kívánt tulajdonságot adhat meg. A Twin kívánt tulajdonságok értékének érvényes JSON-nek kell lennie, és nem Escape-ként kell lennie, és nem szabad olyan tömbök nélkül lennie, amelyek maximális beágyazott hierarchiája négy szinten Olyan esetekben, amikor egy alapértelmezett értékhez szükséges paraméter nem értelmezhető (például az ügyfél kiszolgálójának IP-címe), az alapértelmezett értékként adhat hozzá egy paramétert. A Twin kívánt tulajdonságokkal kapcsolatos további tudnivalókért lásd a [kívánt tulajdonságok meghatározása vagy frissítése](../../iot-edge/module-composition.md#define-or-update-desired-properties)című témakört.

Ha például egy modul támogatja a dinamikusan konfigurálható frissítési sebességet Twin kívánt tulajdonságok használatával, érdemes megadnia a következő alapértelmezett dupla kívánt tulajdonságot:

- Név #1: RefreshRate
- Érték #1:60

**Alapértelmezett környezeti változók**. A környezeti változók kiegészítő információkat biztosítanak a konfigurációs folyamatot segítő modulhoz. A környezeti változók név/érték párok használatával jönnek létre. Az alapértelmezett környezeti változók nevének és értékének 512 karakternél rövidebbnek kell lennie, és legfeljebb öt adható meg. Ha az alapértelmezett értékhez egy paraméter szükséges (például az ügyfél kiszolgálójának IP-címe), az alapértelmezett értékként adhat hozzá egy paramétert.

Ha például egy modulnak el kell fogadnia a használati feltételeket az indítás előtt, megadhatja a következő környezeti változót:

- Név #1: ACCEPT_EULA
- Érték #1: Y

**Alapértelmezett tároló-létrehozási beállítások**. A tároló-létrehozási lehetőségek a IoT Edge modul Docker-tárolójának létrehozását irányítják. A IoT Edge támogatja a Docker-Motor API-hoz tartozó tároló-létrehozási lehetőségeket. Tekintse meg az összes lehetőséget a [List tárolók listájában.](https://docs.docker.com/engine/api/v1.30/#operation/ContainerList) A Create Options mezőnek érvényes JSON-, nem Escape-és 512 karakternél rövidebbnek kell lennie.

Ha például egy modulhoz port kötés szükséges, adja meg a következő létrehozási beállításokat:

"HostConfig": {"PortBindings": {"5012/TCP": [{"HostPort": "5012"}]}

## <a name="review-and-publish"></a>Felülvizsgálat és közzététel

Miután elvégezte az összes szükséges szakaszt, elküldheti azt a felülvizsgálat és közzététel lehetőségre.

A portál jobb felső sarkában válassza a **felülvizsgálat és közzététel** lehetőséget.

A felülvizsgálati oldalon láthatja a közzétételi állapotot:

- Tekintse meg az ajánlat egyes szakaszainak befejezési állapotát. A közzététel addig nem lehetséges, amíg az ajánlat összes része készként van megjelölve.
    - **Nincs elindítva** – a szakasz nem lett elindítva, és el kell végezni.
    - **Hiányos** – a szakasz olyan hibákat tartalmaz, amelyeket meg kell oldania, vagy további információkat kell megadnia. Útmutatásért tekintse meg a jelen dokumentum korábbi szakaszait.
    - **Befejezés** – a szakasz minden szükséges adattal rendelkezik, és nincsenek hibák. Az ajánlat elküldése előtt az ajánlat összes részének teljesnek kell lennie.
- Adja meg a minősítési csapat tesztelési utasításait, hogy az ajánlat megfelelően legyen tesztelve. Továbbá adjon meg az ajánlat megismeréséhez hasznos kiegészítő megjegyzéseket.

Az ajánlat közzétételre való elküldéséhez válassza a **Közzététel** lehetőséget.

Egy e-mailt küldünk, amelyből megtudhatja, hogy az ajánlat előzetes verziója elérhető-e a felülvizsgálathoz és jóváhagyáshoz. Az ajánlat nyilvános közzétételéhez lépjen a partner Center webhelyre, és válassza a **Go-Live** lehetőséget.

## <a name="next-steps"></a>Következő lépések

- [Meglévő ajánlat frissítése a kereskedelmi piactéren](update-existing-offer.md)
