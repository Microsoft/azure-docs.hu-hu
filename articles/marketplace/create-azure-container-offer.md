---
title: Azure-tárolóajánlat létrehozása – Azure Marketplace
description: Megtudhatja, hogyan hozhat létre és tehet közzé tárolóajánlatot Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: keferna
ms.author: keferna
ms.date: 06/17/2020
ms.openlocfilehash: dc7a81f1646fc9f51a4e0bcaf37ef61ca669414e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780514"
---
# <a name="create-an-azure-container-offer-in-azure-marketplace"></a>Azure-tárolóra vonatkozó ajánlat létrehozása az Azure Marketplace-en

Ez a cikk bemutatja, hogyan hozhat létre és tehet közzé tárolóajánlatot Azure Marketplace. A kezdés előtt [hozzon létre egy kereskedelmi piactéri](create-account.md) fiókot a Partnerközpont, ha még nem tette meg. Győződjön meg arról, hogy a fiókja regisztrálva van a kereskedelmi piactér programjában.

## <a name="create-a-new-offer"></a>Új ajánlat létrehozása

1. Jelentkezzen be a [Partnerközpont.](https://partner.microsoft.com/dashboard/home)

2. A bal oldali navigációs menüben válassza a **Kereskedelmi piactér – áttekintés**  >  **lehetőséget.**

3. Az Áttekintés lapon válassza az **+ Új ajánlat**  >  **Azure-tároló lehetőséget.**

   ![A bal oldali navigációs menüt mutatja be.](./partner-center-portal/media/new-offer-azure-container.png)

> [!TIP]
> Az ajánlat közzététele után a szolgáltatásban Partnerközpont az ajánlat újra közzététele után jelennek meg az online áruházakban. A módosításokat követően mindig tegye közzé újra.

### <a name="offer-id-and-alias"></a>Ajánlat azonosítója és aliasa

Adjon meg egy **ajánlatazonosítót.** Ez a fiókban minden ajánlat egyedi azonosítója.

- Ez az azonosító látható az ügyfelek számára a Marketplace-ajánlat webcímén, és Azure Resource Manager sablonokat, ha van ilyen.
- Csak kisbetűket és számokat használjon. Kötőjelet és aláhúzásjelet tartalmazhat, szóközöket azonban nem tartalmazhat, és legfeljebb 50 karakter hosszúságú lehet. Ha például a **test-offer-1 címet adja** meg, az ajánlat webcíme `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` lesz.
- A Létrehozás lehetőséget választva az ajánlatazonosító nem **módosítható.**

Adjon meg egy **Ajánlat aliast.** Ez az ajánlat neve a Partnerközpont.

- Ez a név nem használatos a piactéren, és eltér az ajánlat nevétől és az ügyfelek számára megjelenő egyéb értékektől.
- Ez a Létrehozás lehetőséget választva nem **módosítható.**

Válassza **a Létrehozás** lehetőséget az ajánlat létrehozásához és a folytatáshoz.

## <a name="offer-overview"></a>Ajánlat áttekintése

Az **Ajánlat áttekintése** oldalon látható az ajánlat közzétételéhez szükséges lépések vizuális ábrázolása (mind a kész, mind a jövőbeli), valamint az egyes lépések befejezéséhez szükséges idő.

Ezen az oldalon az ajánlat aktuális állapota alapján különböző hivatkozások megjelenik. Például:

- Ha az ajánlat vázlat – Piszkozat-ajánlat törlése
- Ha az ajánlat élő – [az ajánlat értékesítésének leállítása](./partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan)
- Ha az ajánlat előzetes verzióban érhető el – [Élő adás](review-publish-offer.md#previewing-and-approving-your-offer)
- Ha még nem fejeződött be a közzétevői kijelentkezás – [Közzététel megszakítása.](review-publish-offer.md#cancel-publishing)

## <a name="offer-setup"></a>Ajánlat beállítása

Kövesse az alábbi lépéseket az ajánlat beállításhoz.

### <a name="customer-leads--optional"></a>Érdeklődők – nem kötelező

Amikor közzéteszi az ajánlatot a kereskedelmi piactéren a Partnerközpont, csatlakoztathatja azt az Ügyfélkapcsolat-kezelési (CRM) rendszerhez. Ez lehetővé teszi, hogy azonnal megkapja az ügyfél kapcsolattartási adatait, amint valaki érdeklődik a termék iránt, vagy használja azt.

1. **Válassza ki azt az érdeklődői célt, ahová szeretné, hogy elküldjék az érdeklődőket.** Partnerközpont a következő CRM-rendszereket támogatja:

   - [Dynamics 365](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) for Customer Engagement
   - [Marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md)
   - [Salesforce](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md)

   > [!NOTE]
   > Ha a CRM-rendszer nem szerepel a fenti listában, használja az [Azure-táblát](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md) vagy a [Https-végpontot](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md) az ügyfelek érdeklődői adatainak tárolására, majd exportálja az adatokat a CRM-rendszerbe.

2. Az ajánlat csatlakoztatása az érdeklődő céljához a közzététel Partnerközpont.
3. Ellenőrizze, hogy az érdeklődő célhelyhez való csatlakozás megfelelően van-e konfigurálva. Miután közzétette a Partnerközpont, érvényesítjük a kapcsolatot, és küldünk Egy tesztvezetőt. Amíg megtekinti az ajánlat előnézetét az élő adása előtt, tesztelheti az érdeklődői kapcsolatot is, ha megpróbálja megvásárolni az ajánlatot az előzetes verziójú környezetben.
4. Győződjön meg arról, hogy az érdeklődő célhelyhez való kapcsolódás folyamatosan frissül, hogy ne veszítsen el érdeklődőket.

Íme néhány további érdeklődőkezelési erőforrás:

- [Érdeklődők a kereskedelmi piactéri ajánlattól](./partner-center-portal/commercial-marketplace-get-customer-leads.md)
- [Gyakori kérdések az érdeklődőkezelésről](lead-management-faq.md#common-questions-about-lead-management)
- [Érdeklődő konfigurációs hibáinak elhárítása](lead-management-faq.md#publishing-config-errors)
- [Érdeklődőkezelés áttekintése](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (Győződjön meg arról, hogy az előugró ablak blokkoló ki van kapcsolva).

A **folytatás előtt válassza a Piszkozat** mentése lehetőséget.

### <a name="properties"></a>Tulajdonságok

Ezen az oldalon meghatározhatja az ajánlat piactéren való csoportosításához használt kategóriákat és az ajánlatot támogató jogi szerződéseket.

#### <a name="category"></a>Kategória

Válassza ki a kategóriákat és alkategóriákat, hogy az ajánlatot a megfelelő piactéri keresési területeken helyezze el. Mindenképpen írja le, hogy az ajánlat hogyan támogatja ezeket a kategóriákat az ajánlat leírásában. A következők szerint válasszon:

- Legalább egy és legfeljebb két kategória, köztük egy elsődleges és egy másodlagos kategória (nem kötelező).
- Minden elsődleges és/vagy másodlagos kategóriához legfeljebb két alkategória tartozik. Ha az ajánlatra nem vonatkozik alkategória, válassza a **Nem alkalmazható lehetőséget.**

A kategóriák és alkategóriák teljes listáját az Ajánlott [ajánlatlistázási eljárások rész tartalmazza.](gtm-offer-listing-best-practices.md) A tárolók mindig a **Tárolók, majd** a **Tárolóképek kategóriában jelennek** meg.

#### <a name="legal"></a>Jogi tudnivalók

Meg kell adnia az ajánlat feltételeit és feltételeit. Két lehetőség érhető el:

- Használja a standard szerződés Microsoft kereskedelmi piactérhez.
- Adja meg a saját használati feltételeit.

#### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>A Microsoft kereskedelmi piactér standard szerződése

A kereskedelmi piactéren standard szerződés tranzakciók megkönnyítésére egy sablont is kínálunk. A megoldását a következő standard szerződés kínálhatja, amelyet az ügyfeleknek csak egyszer kell ellenőriznie és elfogadniuk. Ez akkor jó választás, ha nem szeretne egyéni használati feltételeket létrehozni.

További információ a standard szerződés: standard szerződés [Microsoft kereskedelmi piactérhez.](standard-contract.md) Letöltheti az [előugró standard szerződés](https://go.microsoft.com/fwlink/?linkid=2041178) PDF-fájlt is (győződjön meg arról, hogy az előugró ablak blokkoló ki van kapcsolva).

Az alkalmazás standard szerződés válassza a **Standard szerződés használata a Microsoft kereskedelmi piacterén] (. /standard-contract.md)

> [!NOTE]
> Miután közzétett egy ajánlatot a Microsoft kereskedelmi piactér standard szerződésének használatával, nem használhatja a saját egyéni használati feltételeit. A megoldást a jelen standard szerződés vagy a saját használati feltételei szerint kínálják fel.

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-1-standard-contract.png" alt-text="Bemutatja a Standard szerződés microsoftos kereskedelmi piactérhez jelölőnégyzet használatát.":::

##### <a name="your-own-terms-and-conditions"></a>Saját használati feltételek

Ha saját egyéni használati feltételeket és feltételeket is meg kell adnia, adja meg őket a **Használati feltételek mezőben.** Ebben a mezőben korlátlan számú szöveget is beírhat. Az ügyfeleknek el kell fogadniuk ezeket a feltételeket, mielőtt kipróbálhatják az ajánlatot.

Mielőtt **továbblép a** következő, Ajánlatlista szakaszra, válassza a Piszkozat mentése lehetőséget.

## <a name="offer-listing"></a>Ajánlatlista

Ezen az oldalon meghatározhatja a kereskedelmi piactéren megjelenő ajánlat részleteit. Ide tartozik az ajánlat neve, leírása és rendszerképe.

> [!NOTE]
> Az ajánlat részleteinek nem kell angol nyelvűnek lennie, ha az ajánlat leírása a következő kifejezéssel kezdődik: "Ez az alkalmazás csak [nem angol nyelven] érhető el." Hasznos hivatkozást is meg lehet adni, hogy az ajánlat termékoldali részleteiben használttól eltérő nyelven kínáljon fel tartalmakat.

### <a name="name"></a>Name

Az itt beírott név az ajánlat címeként jelenik meg. Ez a mező előre ki van töltve az **Ajánlat** aliasa mezőben az ajánlat létrehozásakor megadott szöveggel. A nevet később módosíthatja.

A név:

- Védjegye lehet (ön pedig védjegyet és szerzői szimbólumot is tartalmazhat).
- Nem lehet hosszabb 50 karakternél.
- Nem tartalmazhat hangulatjeleket.

### <a name="search-results-summary"></a>Keresési eredmények összegzése

Az ajánlat rövid leírása. Ez legfeljebb 100 karakter hosszúságú lehet, és a Marketplace keresési eredményeiben használható.

### <a name="long-summary"></a>Hosszú összefoglalás

Az ajánlat részletesebb leírása. Ez legfeljebb 256 karakter hosszú lehet, és a Marketplace keresési eredményeiben használható.

### <a name="description"></a>Description

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

#### <a name="privacy-policy-link"></a>Adatvédelmi szabályzat hivatkozása

Adja meg a szervezet adatvédelmi szabályzatának webcímét. Az Ön felelőssége annak biztosítása, hogy ajánlata megfeleljen az adatvédelmi törvényeknek és előírásoknak. Emellett az Ön felelőssége, hogy érvényes adatvédelmi szabályzatot közzétensen a webhelyén.

#### <a name="useful-links"></a>Hasznos hivatkozások

Adjon meg kiegészítő online dokumentumokat az ajánlatról. Legfeljebb 25 hivatkozást adhat hozzá. Hivatkozás hozzáadásához válassza a **+ Hivatkozás hozzáadása lehetőséget,** majd töltse ki a következő mezőket:

- **Cím** – Ezt az ügyfelek az ajánlat részleteket tartalmazó oldalán láthatják.
- **Hivatkozás (URL)** – Adjon meg egy hivatkozást, amely alapján az ügyfelek megtekinthetik az online dokumentumot. A hivatkozásnak a következővel http:// vagy https://.

### <a name="contact-information"></a>Kapcsolattartási adatok

Meg kell adnia egy támogatási kapcsolattartó és  egy mérnöki kapcsolattartó nevét, e-mail-címét **és telefonszámát.** Ez az információ nem jelenik meg az ügyfelek számára, de elérhető a Microsoft számára. A csp-partnerek Felhőszolgáltató is meg lehet adni.

- Támogatási kapcsolattartó (kötelező): Általános támogatási kérdések.
- Mérnöki kapcsolattartó (kötelező): Műszaki kérdések és tanúsítási problémák esetén.
- CSP program kapcsolattartója (nem kötelező): A CSP programmal kapcsolatos viszonteladói kérdések esetén.

A Támogatási **kapcsolattartó szakaszban**  adja meg a támogatási webhelyet, ahol a partnerek az alapján találhatnak támogatást az ajánlathoz, hogy az ajánlat elérhető-e a globális Azure-ban, Azure Government vagy mindkettőben.

A **CSP Program kapcsolattartója** szakaszban adja meg a hivatkozást **(CSP Program Marketing Materials**), ahol a CSP-partnerek marketinganyagokat találhatnak az ajánlathoz.

#### <a name="additional-marketplace-listing-resources"></a>További piactéri termékoldali erőforrások

További információ az ajánlatoldalak létrehozásáról: [Ajánlatlista – ajánlott eljárások](gtm-offer-listing-best-practices.md)

### <a name="marketplace-images"></a>Marketplace-rendszerképek

Adja meg az ajánlathoz használni szükséges emblémákat és képeket. Minden képnek PNG formátumúnak kell lennie. A homályos képeket a rendszer elutasítja.

[!INCLUDE [logo tips](./includes/graphics-suggestions.md)]

>[!Note]
>Ha probléma ad a fájlok feltöltésével, győződjön meg arról, hogy a helyi hálózat nem blokkolja a https://upload.xboxlive.com Partnerközpont.

#### <a name="store-logos"></a>Emblémák tárolása

Adjon meg egy PNG-fájlt a **Nagy méretű emblémához.** Partnerközpont a segítségével létrehoz egy **Kicsi** és egy **Közepes emblémát.** Ezeket később más képekre is lecserélheti.

- **Nagy** (216 x 216 és 350 x 350 képpont között szükséges)
- **Közepes** (90 x 90 képpont, nem kötelező)
- **Kicsi** (48 x 48 képpont, nem kötelező)

Ezeket az emblémákat a lista különböző helyei használják:

[!INCLUDE [logos-azure-marketplace-only](./includes/logos-azure-marketplace-only.md)]

[!INCLUDE [Logo tips](./includes/graphics-suggestions.md)]

#### <a name="screenshots-optional"></a>Képernyőképek (nem kötelező)

Adjon hozzá legfeljebb öt képernyőképet, amelyek az ajánlat működését mutatják be. Mindegyiknek 1280 x 720 képpont méretűnek és PNG formátumúnak kell lennie.

#### <a name="videos-optional"></a>Videók (nem kötelező)

Adjon hozzá legfeljebb öt videót, amelyek bemutatják az ajánlatot. Adja meg a videó nevét, webcímét és a videó miniatűr PNG-képét 1280 x 720 képpont méretben.

#### <a name="offer-examples"></a>Ajánlatpéldák

Az alábbi példák bemutatják, hogyan jelennek meg az ajánlatlista mezői az ajánlat különböző helyeiben.

Ez megjeleníti az **Ajánlatlista oldalt** a Azure Marketplace:

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-6-offer-listing-mkt-plc.png" alt-text="Az ajánlatlista oldalának Azure Marketplace." :::

A keresési eredmények a következő Azure Marketplace:

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-7-search-results-mkt-plc.png" alt-text="A keresési eredményeket mutatja Azure Marketplace.":::

Ez az **Ajánlatlista oldalt jeleníti** meg a Azure Portal:

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-8-offer-listing-portal.png" alt-text="Az ajánlatlista oldalát mutatja be a Azure Portal.":::

A keresési eredmények a következő Azure Portal:

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-9-search-results-portal.png" alt-text="A keresési eredményeket mutatja Azure Portal.":::

## <a name="preview"></a>Előnézet

Az Előzetes verzió lapon kiválaszthat  egy korlátozott előzetes verziójú célközönséget, hogy érvényesítsen egy ajánlatot az élő közzététel előtt.

> [!IMPORTANT]
> Miután megtekinti az ajánlatot az előzetes **verzióban,** az Élő közvetítés **lehetőséget** kell választania az ajánlat közzétételéhez.

Adja meg az előzetes verzió célközönségét az Azure-előfizetés AZONOSÍTÓJÁNAK GUID azonosítójával, valamint opcionális leírást mindegyikhez. Az ügyfelek egyik mezőt sem láthatják.

> [!NOTE]
> Az Azure-előfizetés azonosítóját az Előfizetések oldalon találja a Azure Portal.

Adjon hozzá legalább egy Azure-előfizetési azonosítót egyenként (legfeljebb 10-hez), vagy töltsön fel egy CSV-fájlt (legfeljebb 100-at). Ezeknek az előfizetés-előfizetőinek az hozzáadásával meghatározhatja, hogy ki láthatja az ajánlat előnézetét az élő közzététel előtt. Ha az ajánlata már élő, kiválaszthatja az előzetes verzió célközönségét az ajánlat módosításainak vagy frissítésének teszteléséhez.

A **folytatás előtt válassza a Piszkozat** mentése lehetőséget.

## <a name="plan-overview"></a>Terv áttekintése

Ezen a lapon különböző csomagbeállításokat is meg lehet adni ugyanazon az ajánlaton belül. A csomagok (korábbi nevén termékkódok) eltérőek lehetnek a rendelkezésre álló felhők, például a globális felhők, a kormányzati felhők és a terv által hivatkozott rendszerkép tekintetében. Az ajánlat kereskedelmi piactéren való listához be kell állítania legalább egy tervet.

Minden ajánlathoz legfeljebb 100 csomag hozható létre: ezek közül legfeljebb 45 lehet privát. A privát csomagokról a Privát ajánlatok [a Microsoft kereskedelmi piacterén olvashat bővebben.](private-offers.md)

A csomagok létrehozása után a Csomag **áttekintése lapon** a következő jelenik meg:

- Tervnevek
- Díjszabási modell
- Azure-régiók (globális vagy kormányzati)
- Aktuális közzététel állapota
- Minden elérhető művelet

A terv áttekintésében elérhető műveletek a terv aktuális állapotától függően változnak. Ezek közé tartoznak például az alábbiak:

- **Piszkozat** törlése – Ha a terv vázlatos állapotú.
- **Eladási terv leállítása** – Ha a csomag állapota élőben van közzétéve.

### <a name="create-new-plan"></a>Új csomag létrehozása

Válassza **az Új csomag létrehozása lehetőséget.** Megjelenik **az** Új csomag párbeszédpanel.

A **Csomagazonosító mezőben** hozzon létre egy egyedi csomagazonosítót az ajánlat minden csomagja számára. Ez az azonosító a termék webcímének ügyfelei számára látható lesz. Csak kisbetűket és számokat, kötőjeleket vagy aláhúzásjeleket használjon, és legfeljebb 50 karaktert.

> [!NOTE]
> A csomagazonosító a Létrehozás lehetőséget választva nem **módosítható.**

A **Csomag neve mezőben** adja meg a csomag nevét. Az ügyfelek ezt a nevet látják, amikor eldöntik, hogy melyiket választják ki az ajánlaton belül. Hozzon létre egy egyedi nevet az ajánlat minden csomagja számára. Használhatja például a **Windows Server** nevű ajánlatnevet a Windows **Server 2016** és **a Windows Server 2019 csomagokkal.**

### <a name="plan-setup"></a>A csomag beállítása

Ezen a lapon kiválaszthatja, hogy a csomag mely felhőkben érhető el. Az ezen a lapon megjelenő válaszok befolyásolják, hogy mely mezők jelennek meg más lapokon.

#### <a name="azure-regions"></a>Azure-régiók

Az Azure Container-ajánlatok minden csomagja automatikusan elérhetővé válik az **Azure Globalban.**  A csomagját a kereskedelmi piacteret használatba vevő összes globális Azure-régióban használhatja. Részletekért [lásd: Földrajzi rendelkezésre állás és pénznemtámogatás.](marketplace-geo-availability-currencies.md)

Válassza a [Azure Government](../azure-government/documentation-government-welcome.md) lehetőséget, hogy a megoldás itt jelenjen meg. Ez egy kormányzati közösségi felhő, amely szabályozott hozzáférést biztosít az egyesült államokbeli szövetségi, állami és helyi vagy törzsi kormányzati szervektől, valamint az azok kiszolgálására jogosult partnerektől. Közzétevőként Ön felel a megfelelőségi vezérlőkért, a biztonsági intézkedésekért és a felhőalapú közösségre vonatkozó ajánlott eljárásokért. Azure Government fizikailag elkülönített adatközpontokat és hálózatokat használ (csak az Egyesült Államokban). Mielőtt [közzétenné](../azure-government/documentation-government-manage-marketplace-partners.md) a Azure Government, tesztelje és erősítse meg a megoldást az adott területen, mivel az eredmények eltérőek lehetnek. A megoldás létrehozásához és teszteléshez igényelje a próbafiókot a [Microsoft Azure Government fiókkal.](https://azure.microsoft.com/global-infrastructure/government/request/)

> [!NOTE]
> Miután közzétette és elérhetővé tette a tervet egy adott régióban, nem távolíthatja el a régiót.

#### <a name="azure-government-certifications"></a>Azure Government tanúsítványok

Ez a lehetőség csak akkor látható, **ha** Azure Government Azure-régiók alatt van **kiválasztva.**

Azure Government szolgáltatások bizonyos kormányzati szabályozások és követelmények hatálya alá tartozó adatokat kezelnek. Például FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 és CJIS.

A programok minősítésének megjelenítése érdekében akár 100 hivatkozást is meg tud adni, amelyek ismertetik őket. Ezek a programon belül közvetlenül vagy a saját webhelyén található termékoldalakra mutató hivatkozások is lehetek. Ezek a hivatkozások csak az ügyfelek Azure Government számára láthatók.

### <a name="plan-listing"></a>Csomaglista

Ez a lap az aktuális ajánlaton belüli egyes csomagokkal kapcsolatos információkat jeleníti meg.

### <a name="plan-name"></a>Terv neve

Ez előre ki van töltve azzal a névvel, amit a terv létrehozásakor adott meg. Ezt a nevet szükség szerint módosíthatja. Legfeljebb 50 karakter hosszú lehet. Ez a név jelenik meg a csomag címeként a Azure Marketplace és Azure Portal. Ez lesz az alapértelmezett modulnév, miután a terv használatra kész.

### <a name="plan-summary"></a>Terv összegzése

A szoftvercsomag rövid összefoglalása (nem az ajánlat). Ez az összefoglalás megjelenik Azure Marketplace keresési eredmények között, és legfeljebb 100 karaktert tartalmazhat.

### <a name="plan-description"></a>Terv leírása

Írja le, hogy mi teszi egyedivé ezt a szoftvercsomagot, valamint az ajánlaton belüli csomagok közötti különbségeket. Ne írja le az ajánlatot, csak a tervet. Ez a leírás megjelenik a Azure Marketplace az Ajánlatlista Azure Portal lapján található **üzenetben.** Ez lehet ugyanaz a tartalom, mint amit a csomag összegzésében megadott, és legfeljebb 2000 karaktert tartalmazhat.

A **mezők kitöltése** után válassza a Mentés lehetőséget.

#### <a name="plan-examples"></a>Példák a tervezésre

Az alábbi példák bemutatják, hogyan jelennek meg a tervlista mezői a különböző nézetekben.

A terv részleteinek megtekintésekor a Azure Marketplace mezői:

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-10-plan-details-mtplc.png" alt-text="A terv részleteinek megtekintésekor látható mezőket mutatja be a Azure Marketplace.":::

Ezek a terv részletei a Azure Portal:

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-11-plan-details-portal.png" alt-text="Bemutatja a terv részleteit a Azure Portal.":::

### <a name="plan-availability"></a>Terv rendelkezésre állása

Ha el szeretné rejteni a közzétett ajánlatot, hogy az ügyfelek ne tudjanak  keresni, tallózással vagy megvásárolni azt a piactéren, jelölje be a Csomag elrejtése jelölőnégyzetet a Rendelkezésre állás **lapon.**

Ezt a mezőt a következő esetben használja a program:

- Az ajánlatot közvetetten kell használni, ha egy másik alkalmazáson keresztül hivatkoznak rá.
- Az ajánlatot nem szabad egyenként megvásárolni.
- A tervet a kezdeti teszteléshez használták, és már nem releváns.
- A tervet ideiglenes vagy szezonális ajánlatokhoz használták, és már nem ajánlott.

## <a name="technical-configuration"></a>Technikai konfiguráció

A tároló rendszerképeket privát környezetben kell [Azure Container Registry.](https://azure.microsoft.com/services/container-registry/) A **Technikai konfiguráció lapon** adja meg a tároló rendszerkép-adattárának referenciainformációit a Azure Container Registry.

Az ajánlat közzététele után a rendszer átmásolja a tároló rendszerképét Azure Marketplace egy adott nyilvános tárolójegyzékbe. A tároló rendszerképének használatára vonatkozó összes kérést a nyilvános Azure Marketplace, és nem az Ön privát tárolójegyzéke szolgálja ki. Részletekért lásd: [Az Azure-tároló technikai eszközeinek előkészítése.](create-azure-container-technical-assets.md)

### <a name="image-repository-details"></a>Képtár részletei

Adja meg a következő információkat a **Képtár részletei lapon.**

**Azure-előfizetés azonosítója** – Adja meg azt az előfizetés-azonosítót, amelyben a használatot jelenti, és a szolgáltatások Azure Container Registry tároló rendszerképét tartalmazó előfizetésért. Ezt az azonosítót a lap [Előfizetések](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) lapján Azure Portal.

**Azure-erőforráscsoport neve** – Adja meg annak az [erőforráscsoportnak](../azure-resource-manager/management/manage-resource-groups-portal.md) a nevét, amely Azure Container Registry tároló rendszerképének nevét tartalmazza. Az erőforráscsoportnak elérhetőnek kell lennie a fenti előfizetés-azonosítóban. A nevet az Erőforráscsoportok lapon [találja](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) a Azure Portal.

**Azure Container Registry neve** – Adja meg a tároló [rendszerképét Azure Container Registry](../container-registry/container-registry-intro.md) tároló rendszerképét tartalmazó tároló nevét. A tároló-beállításjegyzéknek a korábban megadott Azure-erőforráscsoportban kell lennie. Csak a beállításjegyzék nevét foglalja bele, a bejelentkezési kiszolgáló teljes nevét ne. Ne azurecr.io **a** névből. A beállításjegyzék nevét a tárolóregisztrálók oldalán találja [a](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerRegistry%2Fregistries) Azure Portal.

**Rendszergazdai felhasználónév a Azure Container Registry** – Adja meg a [tároló](../container-registry/container-registry-authentication.md#admin-account)rendszerképét tartalmazó Azure Container Registry rendszergazdai felhasználónevet). A felhasználónév és a jelszó ahhoz szükséges, hogy a vállalat hozzáférjen a regisztrációs adatbázishoz. A rendszergazda felhasználónevének és  jelszavának lekért értékhez állítsa a rendszergazdai jogosultságot **True** (Igaz) tulajdonságra az Azure Command-Line Interface (CLI) használatával. Ha szeretné, a Rendszergazda **felhasználó beállítását engedélyezheti** **a** Azure Portal.

 :::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-12-update-container-registry-edit.png" alt-text="A Tárolójegyzék frissítése párbeszédpanelt mutatja be.":::

**Jelszó a Azure Container Registry** – Adja meg a fiókhoz társított és a tároló rendszerképével Azure Container Registry rendszergazdai felhasználónév jelszavát. A felhasználónév és a jelszó ahhoz szükséges, hogy a vállalat hozzáférjen a regisztrációs adatbázishoz. A jelszót a parancssorból Azure Portal hozzáférési kulcsok Container Registry az Azure CLI használatával a  >   [show paranccsal.](/cli/azure/acr/credential#az_acr_credential_show)

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-13-access-keys.png" alt-text="A Hozzáférési kulcs menüt mutatja be.":::

**Az adattár neve a** Azure Container Registry. Adja meg a rendszerképet Azure Container Registry tárház nevét. A lemezkép regisztrációs adatbázisba való leküldésekor a tárház nevét is meg kell adni. Az adattár nevét az Adattárak lapon [Container Registry](https://azure.microsoft.com/services/container-registry/)  >  **találhatja** meg. További információ: [Tároló-beállításjegyzék-adattárak megtekintése](../container-registry/container-registry-repositories.md)a Azure Portal.

> [!NOTE]
> A név beállítása után az már nem módosítható. A fiókban minden ajánlathoz egyedi nevet használjon.

### <a name="image-tags-for-new-versions-of-your-offer"></a>Rendszerképcímkék az ajánlat új verzióihoz

Az ügyfeleknek automatikusan le kell tudni szerezniük a frissítéseket a Azure Marketplace amikor Ön közzétesz egy frissítést. Ha nem kívánják frissíteni, akkor a rendszerkép egy adott verzióján kell maradniuk. Ezt úgy teheti meg, hogy minden alkalommal új képcímkéket ad hozzá, amikor frissíti a rendszerképet.

### <a name="image-tag"></a>Képcímke

Ennek a mezőnek tartalmaznia kell egy **legújabb** címkét, amely a rendszerkép legújabb verziójára mutat az összes támogatott platformon. Tartalmaznia kell egy verziócímkét is (például xx.xx.xx-xx-ről kezdve, ahol az xx egy szám). Az ügyfeleknek [jegyzékcímkéket kell használniuk](https://github.com/estesp/manifest-tool) több platform megcélhoz. A jegyzékcímkék által hivatkozott összes címkét is hozzá kell adni, hogy feltöltjük őket.

Minden jegyzékcímkének (a legújabb címke kivételével) X.Y-vel vagy X.Y.Z-vel kell kezdődnie, ahol az X, az Y és a Z egész **-** szám. Ha például egy  legújabb címke a 1.0.1-linux-x64, 1.0.1-linux-arm32 és 1.0.1-windows-arm32 címkére mutat, akkor ezt a hat címkét hozzá kell adni ehhez a mezőhöz. Részletekért lásd: [Az Azure-tároló technikai eszközeinek előkészítése.](create-azure-container-technical-assets.md)

> [!NOTE]
> Ne felejtsen el hozzáadni egy tesztcímkét a képhez, hogy a tesztelés során azonosítani tudja a képet.

## <a name="review-and-publish"></a>Felülvizsgálat és közzététel

Miután befejezte az ajánlat összes szükséges szakaszát, elküldheti felülvizsgálatra és közzétételre.

A portál jobb felső sarkában válassza az Áttekintés **és közzététel** **lehetőséget.**

A felülvizsgálati oldalon a következőt használhatja:

- Tekintse meg az ajánlat egyes szakaszának befejezési állapotát. Csak akkor tehet közzé közzétételt, ha az ajánlat minden szakasza befejezettként van megjelölve.
  - **Not started** (Nincs elindítva) – Még nem indult el, és be kell fejeződni.
  - **Hiányos** – Olyan hibákat tartalmaz, amelyek kijavítása szükséges, vagy amelyekhez további információkra van szükség. További segítségért tekintse meg a dokumentum korábbi szakaszait.
  - **Complete** (Kész) – Az összes szükséges adatot tartalmazza, hibák nélkül. Az ajánlat elküldését megelőzően az ajánlat minden szakaszának teljesnek kell lennie.
- Adja meg a tesztelési utasításokat a tanúsítási csapatnak, hogy az ajánlata megfelelően tesztelve legyen. Emellett adjon meg minden kiegészítő jegyzetet, amely hasznos lehet az ajánlat megértéséhez.

Az ajánlat közzétételre való elküldését a Közzététel lehetőséget **választva küldheti el.**

E-mailt küldünk Önnek, amely tudatja, hogy az ajánlat előzetes verziója mikor válik elérhetővé felülvizsgálatra és jóváhagyásra.

Az ajánlat nyilvános közzétételéhez kattintson a Partnerközpont **go-live (Élő adás) gombra.**

## <a name="next-step"></a>Következő lépés

- [Meglévő ajánlat frissítése a kereskedelmi piactéren](./partner-center-portal/update-existing-offer.md)