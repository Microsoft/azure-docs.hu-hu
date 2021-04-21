---
title: Hozzon létre Azure IoT Edge modulajánlatot a Partnerközpont használatával Azure Marketplace
description: Megtudhatja, hogyan hozhat létre, konfigurálhatja és tehet közzé egy IoT Edge-modulajánlatot a Azure Marketplace használatával Partnerközpont.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: keferna
ms.author: keferna
ms.date: 08/07/2020
ms.openlocfilehash: 12600cadaa84ae116818eec06459d5db0c05304a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773419"
---
# <a name="create-an-iot-edge-module-offer"></a>IoT Edge-modulajánlat létrehozása

Ez a cikk azt ismerteti, hogyan hozhat létre és tehet közzé eszközök internetes hálózata (IoT) Edge-modulajánlatot a Azure Marketplace. A kezdés előtt [hozzon létre egy kereskedelmi piactéri](../create-account.md) fiókot a Partnerközpont, ha még nem tette meg. Győződjön meg arról, hogy a fiókja regisztrálva van a kereskedelmi piactéri programban.

## <a name="create-a-new-offer"></a>Új ajánlat létrehozása

1. Jelentkezzen be a [Partnerközpont.](https://partner.microsoft.com/dashboard/home)
2. A bal oldali navigációs menüben válassza a **Kereskedelmi piactér – áttekintés**  >  **lehetőséget.**
3. Az Áttekintés lapon válassza az **+ Új ajánlat IoT Edge**  >  **modulban.**

    ![A bal oldali navigációs menüt mutatja be.](./media/new-offer-iot-edge.png)

> [!IMPORTANT]
> Az ajánlat közzététele után a szolgáltatásban Partnerközpont az ajánlat újra közzététele után csak az online áruházakban jelennek meg. A módosításokat követően mindig tegye közzé újra.

### <a name="offer-id-and-alias"></a>Ajánlat azonosítója és aliasa

Adjon meg egy **ajánlatazonosítót.** Ez a fiók minden ajánlatának egyedi azonosítója.

- Ez az azonosító látható az ügyfelek számára a marketplace-ajánlat webcímén, és Azure Resource Manager sablonokat, ha van ilyen.
- Csak kisbetűket és számokat használjon. Kötőjeleket és aláhúzásjeleket tartalmazhat, szóközök nélkül, és legfeljebb 50 karakter hosszú lehet. Ha például a **test-offer-1** címet adja meg, az ajánlat webcíme `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` lesz.
- A Létrehozás lehetőséget választva az ajánlat azonosítója nem **módosítható.**

Adjon meg egy **Ajánlat aliast.** Ez a neve az ajánlatnak a Partnerközpont.

- Ez a név nem használatos a piactéren, és eltér az ajánlat nevétől és az ügyfelek számára megjelenő egyéb értékektől.
- Ez a Létrehozás lehetőséget választva nem **módosítható.**

Válassza **a Létrehozás lehetőséget** az ajánlat létrehozásához és a folytatáshoz.

## <a name="offer-overview"></a>Ajánlat áttekintése

Az **Ajánlat áttekintése** oldalon látható az ajánlat közzétételéhez szükséges lépések vizuális ábrázolása (befejezett és közelgő), valamint az egyes lépések befejezéséhez szükséges idő.

Ez az oldal hivatkozásokat tartalmaz az ajánlattal kapcsolatos műveletek végrehajtásához az Ön által kiválasztott beállítások alapján. Például:

- Ha az ajánlat vázlat – Piszkozat-ajánlat törlése
- Ha az ajánlat élő – Az [ajánlat értékesítésének leállítása](update-existing-offer.md#stop-selling-an-offer-or-plan)
- Ha az ajánlat előzetes verzióban érhető el – [Élő adás](../review-publish-offer.md#previewing-and-approving-your-offer)
- Ha még nem fejeződött be a közzétevői kijelentkezás – [Közzététel megszakítása.](../review-publish-offer.md#cancel-publishing)

## <a name="offer-setup"></a>Ajánlat beállítása

Kövesse az alábbi lépéseket az ajánlat beállításhoz.

### <a name="customer-leads"></a>Érdeklődők

Amikor közzétenné az ajánlatot a piactéren Partnerközpont, csatlakoztathatja azt az Ügyfélkapcsolat-kezelési (CRM) rendszerhez. Ez lehetővé teszi, hogy azonnal megkapja az ügyfél kapcsolattartási adatait, amint valaki érdeklődik a termék iránt, vagy használja azt.

1. Válasszon egy érdeklődési célt, ahová küldhetjük az érdeklődőket. Partnerközpont a következő CRM-rendszereket támogatja:

    - [Dynamics 365](commercial-marketplace-lead-management-instructions-dynamics.md) for Customer Engagement
    - [Marketo](commercial-marketplace-lead-management-instructions-marketo.md)
    - [Salesforce](commercial-marketplace-lead-management-instructions-salesforce.md)

    > [!NOTE]
    > Ha a CRM-rendszer nem szerepel a fenti listában, használja az [Azure Table](commercial-marketplace-lead-management-instructions-azure-table.md) vagy [a Https-végpontot](commercial-marketplace-lead-management-instructions-https.md) az ügyfelek érdeklődői adatainak tárolására, majd exportálja az adatokat a CRM-rendszerbe.

2. Az ajánlat csatlakoztatása az érdeklődő célhelyhez az Partnerközpont.
3. Ellenőrizze, hogy az érdeklődő célhelyhez való csatlakozás megfelelően van-e konfigurálva. Miután közzétette a Partnerközpont, érvényesítjük a kapcsolatot, és küldünk egy tesztvezetőt. Amíg az ajánlat előnézetét az élő adása előtt megtekinti, tesztelheti az érdeklődői kapcsolatot is, ha megpróbálja megvásárolni az ajánlatot az előzetes verziójú környezetben.
4. Győződjön meg arról, hogy az érdeklődő céljához való kapcsolat folyamatosan frissül, hogy ne veszítsen el érdeklődőket.

Íme néhány további érdeklődőkezelési erőforrás:

- [Érdeklődők a kereskedelmi piactéri ajánlattól](commercial-marketplace-get-customer-leads.md)
- [Gyakori kérdések az érdeklődőkezelésről](../lead-management-faq.md#common-questions-about-lead-management)
- [Érdeklődőkonfigurációs hibák elhárítása](../lead-management-faq.md#publishing-config-errors)
- [Érdeklődőkezelés áttekintése](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (Győződjön meg arról, hogy az előugró ablak blokkoló ki van kapcsolva).

A **folytatás előtt válassza a Piszkozat** mentése lehetőséget.

### <a name="properties"></a>Tulajdonságok

Ezen az oldalon meghatározhatja az ajánlata piactéren való csoportosításához használt kategóriákat és az ajánlatot támogató jogi szerződéseket.

#### <a name="category"></a>Kategória

Válassza ki a kategóriákat és alkategóriákat, hogy az ajánlatot a megfelelő piactéri keresési területeken helyezze el. Mindenképpen írja le, hogy az ajánlat hogyan támogatja ezeket a kategóriákat az ajánlat leírásában. A következők szerint válasszon:

- Legalább egy és legfeljebb két kategória, köztük egy elsődleges és egy másodlagos kategória (nem kötelező).
- Minden elsődleges és/vagy másodlagos kategóriához legfeljebb két alkategória tartozik. Ha az ajánlatra nem vonatkozik alkategória, válassza a **Nem alkalmazható lehetőséget.**

A kategóriák és alkategóriák teljes listáját az Ajánlatlistázással kapcsolatos ajánlott [eljárások rész tartalmazza.](../gtm-offer-listing-best-practices.md) A piactéren a IoT Edge modulok mindig  **** a Modul eszközök internetes hálózata  >  **IoT Edge alatt jelennek**   meg.

#### <a name="legal"></a>Jogi tudnivalók

Meg kell adnia az ajánlat feltételeit és feltételeit. Erre két lehetősége van:

- Használja standard szerződés Microsoft kereskedelmi piactérhez elérhető webhelyet.
- Adja meg saját használati feltételeit.

##### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>A Microsoft kereskedelmi piactér standard szerződése

A kereskedelmi piactéren standard szerződés tranzakciók megkönnyítésére egy sablont is kínálunk. A megoldását a következő standard szerződés kínálhatja, amelyet az ügyfeleknek csak egyszer kell ellenőriznie és elfogadniuk. Ez akkor jó választás, ha nem szeretne egyéni feltételeket és használati feltételeket megszabadni.

A termékről további standard szerződés lásd: standard szerződés [microsoftos kereskedelmi piactérhez.](../standard-contract.md) Letöltheti az [előugró standard szerződés](https://go.microsoft.com/fwlink/?linkid=2041178) PDF-fájlt is (győződjön meg arról, hogy az előugró ablak blokkoló ki van kapcsolva).

Az alkalmazás standard szerződés jelölje be a Use the standard szerződés for Microsoft's commercial marketplace (Az standard szerződés használata a Microsoft kereskedelmi **piacterén)** jelölőnégyzetet, majd kattintson az Accept (Elfogadás) **gombra.**

> [!NOTE]
> Miután közzétett egy ajánlatot a Microsoft kereskedelmi piactér standard szerződésének használatával, nem használhatja a saját egyéni használati feltételeit. A megoldást a jelen standard szerződés vagy a saját használati feltételei szerint kínálják fel.

![Bemutatja a standard szerződés Microsoft kereskedelmi piactérhez jelölőnégyzet használatát.](media//iot-edge-module-standard-contract-checkbox.png)

##### <a name="your-own-terms-and-conditions"></a>Saját használati feltételek

Ha saját egyéni használati feltételeket és feltételeket is meg kell adnia, adja meg őket a **Használati feltételek mezőben.** Ebben a mezőben korlátlan mennyiségű szöveget is beírhat. Az ügyfeleknek el kell fogadniuk ezeket a feltételeket, mielőtt kipróbálhatják az ajánlatot.

Mielőtt **továbblép a** következő, Ajánlatlista szakaszra, válassza a Piszkozat mentése lehetőséget.

## <a name="offer-listing"></a>Ajánlatlista

Itt meghatározhatja a piactéren megjelenő ajánlat részleteit. Ez magában foglalja az ajánlat nevét, leírását, rendszerképeket stb. Az ajánlat konfigurálása során mindig kövesse a Microsoft szabályzatlapján található szabályzatokat.

> [!NOTE]
> Az ajánlat részleteinek nem kell angol nyelvűnek lennie, ha az ajánlat leírása a következő kifejezéssel kezdődik: "Ez az alkalmazás csak [nem angol nyelven] érhető el." Hasznos hivatkozást is meg lehet adni, hogy az ajánlat termékoldali részleteiben használttól eltérő nyelven kínáljon fel tartalmakat.

### <a name="name"></a>Name

Az itt beírott név az ajánlat címeként jelenik meg. Ez a mező előre ki van töltve az Ajánlat aliasa mezőben az ajánlat létrehozásakor megadott szöveggel.  A nevet később módosíthatja.

A név:

- Védjegye is lehet (és védjegyeket vagy szerzői jogi szimbólumokat is tartalmazhat).
- Nem lehet hosszabb 50 karakternél.
- Nem tartalmazhat hangulatjeleket.

### <a name="search-results-summary"></a>Keresési eredmények összegzése

Adja meg az ajánlat rövid leírását. Ez legfeljebb 100 karakter hosszúságú lehet, és a Marketplace keresési eredményeiben használható.

### <a name="long-summary"></a>Hosszú összefoglalás

Adja meg az ajánlat részletesebb leírását. Ez legfeljebb 256 karakter hosszú lehet, és a Marketplace keresési eredményeiben használható.

### <a name="description"></a>Description

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

IoT Edge modul ajánlatának tartalmaznia kell egy minimális hardverkövetelményt ismertető bekezdést a leírás alján, például:

- Minimális hardverkövetelmények: Linux x64 és arm32 operációs rendszer, 1 GB RAM, 500 Mb tárhely

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

#### <a name="privacy-policy-url"></a>Adatvédelmi szabályzat URL-címe

Adja meg a szervezet adatvédelmi szabályzatának webcímét. Önnek kell gondoskodnia arról, hogy az ajánlata megfeleljen az adatvédelmi törvényeknek és előírásoknak. Az Ön felelőssége, hogy érvényes adatvédelmi szabályzatot tessen közzé a webhelyén.

#### <a name="useful-links"></a>Hasznos hivatkozások

Adjon meg kiegészítő online dokumentumokat az ajánlatról. Legfeljebb 25 hivatkozást adhat hozzá. Hivatkozás hozzáadásához válassza a **+ Hivatkozás hozzáadása lehetőséget,** majd töltse ki a következő mezőket:

- **Cím** – Az ügyfelek az ajánlat részleteit tartalmazó oldalon látják a címet.
- **Hivatkozás (URL)** – Itt adhatja meg az online dokumentum megtekintésére mutató hivatkozást az ügyfelek számára. A hivatkozásnak vagy kezdetsel kell `http://` `https://` kezdődnie.

Legalább egy, a dokumentációra mutató hivatkozást és egy hivatkozást adjon hozzá a kompatibilis IoT Edge az [Azure IoT-eszközkatalógusból.](https://devicecatalog.azure.com/)

### <a name="contact-information"></a>Kapcsolattartási adatok

Meg kell adnia egy támogatási kapcsolattartó és  egy mérnöki kapcsolattartó nevét, e-mail-címét és **telefonszámát.** Ezek az információk nem jelennek meg az ügyfelek számára. Elérhető a Microsoft számára, és elérhető a Felhőszolgáltató (CSP) partnerek számára.

- Támogatási kapcsolattartó (kötelező): Általános támogatási kérdések.
- Mérnöki kapcsolattartó (kötelező): Műszaki kérdések és tanúsítási problémák esetén.
- CSP program kapcsolattartója (nem kötelező): A CSP programmal kapcsolatos viszonteladói kérdések esetén.

A Támogatási **kapcsolattartó** szakaszban adja meg  a támogatási webhely webcímét, ahol a partnerek az alapján találhatnak támogatást az ajánlathoz, hogy az ajánlat elérhető-e a globális Azure-ban, Azure Government vagy mindkettőben.

A **CSP Program kapcsolattartója** szakaszban adja meg a hivatkozást **(CSP Program Marketing Materials**), ahol a CSP-partnerek marketinganyagokat találhatnak az ajánlathoz.

#### <a name="additional-marketplace-listing-resources"></a>További piactéri termékoldali erőforrások

Az ajánlatlista létrehozásával kapcsolatos további információkért tekintse meg az [ajánlatlista ajánlott eljárásait.](../gtm-offer-listing-best-practices.md)

### <a name="marketplace-images"></a>Marketplace-rendszerképek

Adja meg az ajánlathoz használni szükséges emblémákat és képeket. Minden képnek PNG formátumúnak kell lennie. A homályos képeket a rendszer elutasítja.

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

>[!Note]
>Ha probléma ad a fájlok feltöltésével, győződjön meg arról, hogy a helyi hálózat nem blokkolja a https://upload.xboxlive.com Partnerközpont.

#### <a name="store-logos"></a>Áruházi emblémák

Adjon meg egy PNG-fájlt a **Nagy méretű** emblémához. Partnerközpont ezzel létrehoz egy **Kicsi** és egy **Közepes emblémát.** Ezeket később más képekre is lecserélheti.

- **Nagy** (216 x 216 és 350 x 350 képpont között szükséges)
- **Közepes** (90 x 90 képpont, opcionális)
- **Kicsi** (48 x 48 képpont, opcionális)

Ezeket az emblémákat a lista különböző helyei használják:

[!INCLUDE [logos-azure-marketplace-only](../includes/logos-azure-marketplace-only.md)]

[!INCLUDE [Logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots-optional"></a>Képernyőképek (nem kötelező)

Adjon hozzá legfeljebb öt képernyőképet, amelyek az ajánlat működését mutatják be. Mindegyiknek 1280 x 720 képpont méretűnek és PNG formátumúnak kell lennie.

#### <a name="videos-optional"></a>Videók (nem kötelező)

Akár öt videót is hozzáadhat, amelyek bemutatják az ajánlatot. Adja meg a videó nevét, webcímét és a videó miniatűr PNG-képét 1280 x 720 képpont méretben.

#### <a name="marketplace--examples"></a>Marketplace-példák

Az alábbi példa bemutatja, hogyan jelennek meg az ajánlatinformációk a Azure Marketplace:

:::image type="content" source="media/example-iot-azure-marketplace-offer.png" alt-text="Bemutatja, hogyan jelenik meg ez az ajánlat Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>A hívás leírásai

1. Nagy embléma
2. Kategóriák
3. Támogatási cím (hivatkozás)
4. használati feltételei
5. Adatvédelmi szabályzat címe (hivatkozás)
6. Name
7. Összefoglalás
8. Description
9. Hasznos hivatkozások
10. Képernyőképek/videók

<br>Az alábbi példa bemutatja, hogyan jelennek meg az ajánlatinformációk Azure Marketplace keresési eredményekben:

:::image type="content" source="media/example-iot-azure-marketplace-offer-search-results.png" alt-text="Bemutatja, hogyan jelenik meg ez az ajánlat Azure Marketplace keresési eredmények között.":::

#### <a name="call-out-descriptions"></a>A felhívások leírása

1. Kis embléma
2. Ajánlat neve
3. Keresési eredmények összegzése

<br>Az alábbi példa bemutatja, hogyan jelennek meg az ajánlatinformációk a Azure Portal:

:::image type="content" source="media/example-iot-azure-portal-offer.png" alt-text="Bemutatja, hogyan jelenik meg ez az ajánlat a Azure Portal.":::

#### <a name="call-out-descriptions"></a>A felhívások leírása

1. Név
2. Leírás
3. Hasznos hivatkozások
4. Képernyőképek

<br>Az alábbi példa bemutatja, hogyan jelennek meg az ajánlatinformációk Azure Portal keresési eredmények között:

:::image type="content" source="media/example-iot-azure-portal-offer-search-results.png" alt-text="Bemutatja, hogyan jelenik meg ez az ajánlat Azure Portal keresési eredmények között.":::

#### <a name="call-out-descriptions"></a>A felhívások leírása

1. Kis embléma
2. Ajánlat neve
3. Keresési eredmények összegzése

<br>Mielőtt **továbblépne** a következő, Előzetes verzió szakaszra, válassza a Piszkozat mentése lehetőséget.

## <a name="preview"></a>Előnézet

Az **Előzetes verzió lapon** kiválaszthatja, hogy az előzetes verzió célközönsége csak korlátozottan legyen érvényes az ajánlatra, mielőtt élőben közzéteheti azt a szélesebb körű piactéren. 

> [!IMPORTANT]
> Miután megtekinti az ajánlatot az előzetes verzióban, az **Élőben** gombra kell választania az ajánlat nyilvános közzétételéhez.

Adja meg az előzetes verzió célközönségét az Azure-előfizetés GUID azonosítójával, és opcionális leírást mindegyikhez. Ezen mezők közül egyiket sem láthatják az ügyfelek.

> [!NOTE]
> Az Azure-előfizetés azonosítóját az Előfizetések oldalon találja a Azure Portal.

Adjon hozzá legalább egy Azure-előfizetési azonosítót egyenként (legfeljebb 10-hez), vagy töltsön fel egy CSV-fájlt (legfeljebb 100-at). Ezeknek az előfizetés-előfizetőinek az hozzáadásával megadhatja, hogy ki láthatja az ajánlat előnézetét az élő közzététel előtt. Ha az ajánlat már élő, előzetes verziójú célközönséget határozhat meg az ajánlat módosításainak vagy frissítésének teszteléséhez.

Mielőtt **továbblépne** a következő, Terv áttekintése című szakaszra, válassza a Piszkozat mentése lehetőséget.

## <a name="plan-overview"></a>Terv áttekintése

Ezen a lapon különböző csomagbeállításokat is meg lehet adni ugyanabban az ajánlatban a Partnerközpont. A csomagok (korábbi nevén termékkódok) eltérőek lehetnek a rendelkezésre álló felhők, például a globális felhők, a kormányzati felhők és a terv által hivatkozott rendszerkép tekintetében. Az ajánlat marketplace-en való listához be kell állítania legalább egy tervet.

Minden ajánlathoz legfeljebb 100 csomag hozható létre: ezek közül legfeljebb 45 lehet privát. A privát csomagokról a Privát ajánlatok [a Microsoft kereskedelmi piacterén olvashat bővebben.](../private-offers.md)

A csomagok létrehozása után a Csomag **áttekintése lapon** a következő jelenik meg:

- Tervnevek
- Díjszabási modell
- Azure-régiók (globális vagy kormányzati)
- Aktuális közzététel állapota
- Minden elérhető művelet

A csomag áttekintésében elérhető műveletek a terv aktuális állapotától függően változnak. Ezek közé tartoznak például az alábbiak:

- **Piszkozat** törlése: Ha a terv vázlatos állapotú.
- **Eladási terv leállítása:** Ha a csomag állapota élőben van közzétéve.

### <a name="create-new-plan"></a>Új csomag létrehozása

Válassza **az Új csomag létrehozása lehetőséget.** Megjelenik **az** Új csomag párbeszédpanel.

A **Csomagazonosító mezőben** hozzon létre egy egyedi csomagazonosítót az ajánlat minden csomagja számára. Ez az azonosító látható lesz az ügyfelek számára a termék webcímében. Csak kisbetűket és számokat, kötőjeleket vagy aláhúzásjeleket használjon, és legfeljebb 50 karaktert.

A **Csomag neve mezőben** adja meg a csomag nevét. Az ügyfelek ezt a nevet látják, amikor eldöntik, hogy melyiket választják ki az ajánlaton belül. Hozzon létre egy egyedi nevet az ajánlat minden csomagja számára. Használhatja például a **Windows Server** nevű ajánlatnevet a Windows **Server 2016** és **a Windows Server 2019 csomagokkal.**

> [!NOTE]
> A csomagazonosító a Létrehozás lehetőséget választva nem **módosítható.**

Válassza a **Létrehozás** lehetőséget.

### <a name="plan-setup"></a>A csomag beállítása

Ezen a lapon konfigurálhatja, hogy a csomag mely felhőkben érhető el. Az ezen a lapon megjelenő válaszok befolyásolják, hogy mely mezők jelennek meg más lapokon.

#### <a name="azure-regions"></a>Azure-régiók

Az összes modulra IoT Edge ajánlat automatikusan elérhetővé válik az **Azure Globalban.**  A csomagját a piacteret használatba vevő összes globális Azure-régióban használhatja. Részletekért [lásd: Földrajzi rendelkezésre állás és pénznemtámogatás.](../marketplace-geo-availability-currencies.md)

Válassza a [Azure Government](../../azure-government/documentation-government-welcome.md) lehetőséget, hogy a megoldás itt jelenjen meg. Ez egy kormányzati közösségi felhő, amely szabályozott hozzáférést biztosít az egyesült államokbeli szövetségi, állami és helyi vagy törzsi kormányzati szervektől, valamint az azok kiszolgálására jogosult partnerektől. Közzétevőként Ön felel a megfelelőségi vezérlőkért, a biztonsági intézkedésekért és a felhőalapú közösségre vonatkozó ajánlott eljárásokért. Azure Government fizikailag elkülönített adatközpontokat és hálózatokat használ (csak az Egyesült Államokban). Mielőtt [közzétenné](../../azure-government/documentation-government-manage-marketplace-partners.md) a Azure Government, tesztelje és erősítse meg a megoldást az adott területen, mivel az eredmények eltérőek lehetnek. A megoldás tesztelése és tesztelése érdekében igényelje a próbafiókot a [Microsoft Azure Government előfizetésből.](https://azure.microsoft.com/global-infrastructure/government/request/)

> [!NOTE]
> Miután közzétette és elérhetővé tette a tervet egy adott régióban, nem távolíthatja el azt a régiót.

#### <a name="azure-government-certifications"></a>Azure Government tanúsítványok

Ez a beállítás csak akkor látható, **Azure Government** Azure-régiók alatt van **kiválasztva.**

Azure Government szolgáltatások bizonyos kormányzati szabályozások és követelmények hatálya alá tartozó adatokat kezelnek. Például FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 és CJIS. A programok minősítésének felkeltése érdekében akár 100 hivatkozást is kínálhat a minősítések leírására. Ezek a programon belül közvetlenül vagy a saját webhelyén található termékoldalakra mutató hivatkozások is lehetek. Ezek a hivatkozások csak az Azure Government számára láthatók.

### <a name="plan-listing"></a>Csomaglista

Ezen a lapon az adott ajánlaton belüli egyes csomagokkal kapcsolatos információk megjelenik.

### <a name="plan-name"></a>Terv neve

Ez előre ki van töltve azzal a névvel, amit a csomag létrehozásakor adott meg. Ezt a nevet szükség szerint módosíthatja. Legfeljebb 50 karakter hosszú lehet. Ez a név jelenik meg a csomag címeként a Azure Marketplace és a Azure Portal. Ez lesz az alapértelmezett modulnév, miután a terv használatra kész.

### <a name="plan-summary"></a>Terv összegzése

Adja meg a csomag rövid összefoglalását (ne az ajánlatot). Ez az összefoglalás megjelenik Azure Marketplace keresési eredmények között, és legfeljebb 100 karaktert tartalmazhat.

### <a name="plan-description"></a>Terv leírása

Ismertesse, hogy mi teszi egyedivé ezt a tervet, valamint az ajánlaton belüli csomagok közötti különbségeket. Ne írja le az ajánlatot, csak a tervet. Ez a leírás megjelenik a Azure Marketplace az Ajánlatlista Azure Portal lapján található üzenetben. Ez lehet ugyanaz a tartalom, mint amit a csomag összegzésében megadott, és legfeljebb 2000 karaktert tartalmazhat.

A **mezők kitöltése** után válassza a Piszkozat mentése lehetőséget.

#### <a name="plan-examples"></a>Példák a tervezésre

Íme egy példa a Azure Marketplace részleteire (a felsorolt árak csak példaként szolgálnak, és nem a tényleges költségeket hivatottak tükrözni):

:::image type="content" source="media/example-iot-azure-marketplace-plan.png" alt-text="Bemutatja Azure Marketplace terv részleteit.":::

#### <a name="call-out-descriptions"></a>A felhívások leírása

1. Ajánlat neve
2. Terv neve
3. Terv leírása

<br>Íme egy példa a Azure Portal részleteire (a felsorolt árak csak példaként szolgálnak, és nem a tényleges költségeket hivatottak tükrözni):

:::image type="content" source="media/example-iot-azure-marketplace-plan-details.png" alt-text="Bemutatja a Azure Portal terv részleteit.":::

#### <a name="call-out-descriptions"></a>A felhívások leírása

1. Ajánlat neve
2. Terv neve
3. Terv leírása

### <a name="availability"></a>Rendelkezésre állás

Ha el szeretné rejteni a közzétett ajánlatot, hogy az ügyfelek ne tudjanak  keresni, tallózással vagy megvásárolni azt a piactéren, jelölje be a Csomag elrejtése jelölőnégyzetet a Rendelkezésre állás lapon.

Ezt a mezőt általában a következő célokra használják:

- Az ajánlat csak közvetetten használható, ha egy másik alkalmazásra hivatkoznak.
- Az ajánlatot nem szabad egyenként megvásárolni.
- A tervet a kezdeti teszteléshez használták, és már nem releváns.
- A csomag ideiglenes vagy szezonális ajánlatokhoz lett használva, és már nem ajánlott.

## <a name="technical-configuration"></a>Technikai konfiguráció

A **IoT Edge modul** ajánlattípusa egy adott típusú tároló, amely egy IoT Edge fut. A **Technikai konfiguráció lapon** meg kell adnia a tároló rendszerkép-adattárának referenciainformációit a [Azure Container Registry-on](https://azure.microsoft.com/services/container-registry/)belül, valamint konfigurációs beállításokat, amelyek lehetővé tévik az ügyfelek számára a modul egyszerű használatát.

Az ajánlat közzététele után a rendszer IoT Edge tároló rendszerképét egy Azure Marketplace nyilvános tárolójegyzékbe másolja. Az Azure-felhasználóktól a modul használatára vonatkozó összes kérést a nyilvános Azure Marketplace, és nem a privát tárolójegyzékből kell kiszolgálni.

Több platformot is megcélzott, és címkék használatával a modul tárolórendszerképének több verzióját is meg lehet adni. A címkékkel és verziószámozással kapcsolatos további információkért lásd a IoT Edge [modul technikai eszközeinek előkészítését.](create-iot-edge-module-asset.md)

### <a name="image-repository-details"></a>Képtár részletei

A képtár részletei lapon **a** következő információkat kell meg adnia.

**Válassza ki a rendszerkép forrását:** Válassza Azure Container Registry **lehetőséget.**

**Azure-előfizetés azonosítója:** Adja meg annak az előfizetésnek az azonosítóját, ahol a rendszer jelentést ad az erőforrás-használatról, és a szolgáltatások Azure Container Registry tároló rendszerképét tartalmazó előfizetésért. Ezt az azonosítót a lap [Előfizetések](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) lapján Azure Portal.

**Azure-erőforráscsoport neve:** Adja [meg](../../azure-resource-manager/management/manage-resource-groups-portal.md) annak az erőforráscsoportnak a nevét, amely Azure Container Registry tároló rendszerképének nevét tartalmazza. Az erőforráscsoportnak elérhetőnek kell lennie az előfizetés-azonosítóban (fent). A nevet az Erőforráscsoportok lapon [találja](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) a Azure Portal.

**Azure Container Registry neve:** Adja meg a tároló [rendszerképét Azure Container Registry](../../container-registry/container-registry-intro.md) tároló rendszerképét tartalmazó tároló nevét. A tároló-beállításjegyzéknek jelen kell lennie a korábban megadott Azure-erőforráscsoportban. Csak a beállításjegyzék nevét adja meg, a bejelentkezési kiszolgáló teljes nevét ne. Ne azurecr.io **a** névből. A tárolóregisztráló nevét a tárolóregisztrálók oldalán [találja a](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerRegistry%2Fregistries) Azure Portal.

**Rendszergazdai felhasználónév a Azure Container Registry:** Adja [](../../container-registry/container-registry-authentication.md#admin-account)meg a tároló rendszerképét tartalmazó Azure Container Registry fiókhoz társított rendszergazdai felhasználónevet). A felhasználónév és a jelszó szükséges annak biztosításához, hogy a vállalat hozzáférjen a regisztrációs adatbázishoz. A rendszergazdai felhasználónév és jelszó  lekért értékhez állítsa a rendszergazda által engedélyezett **tulajdonságot True** (Igaz) értékre az Azure Command-Line Interface (CLI) használatával. Ha szeretné, a **Rendszergazdai felhasználó beállítását is** **beállíthatja az** Engedélyezés Azure Portal.

:::image type="content" source="media/example-iot-update-container-registry.png" alt-text="A Tároló-beállításjegyzék frissítése párbeszédpanelt mutatja be.":::

#### <a name="call-out-description"></a>A hívás leírása

1. Rendszergazdai felhasználó

<br>**Jelszó a Azure Container Registry:** Adja meg a fiókhoz társított és a tároló rendszerképével Azure Container Registry rendszergazdai felhasználónév jelszavát. A felhasználónév és a jelszó szükséges annak biztosításához, hogy a vállalat hozzáférjen a regisztrációs adatbázishoz. A jelszót a portálról Azure Portal hozzáférési kulcsok Container Registry az Azure CLI használatával  >   a [show paranccsal.](/cli/azure/acr/credential#az_acr_credential_show)

:::image type="content" source="media/example-iot-access-keys.png" alt-text="A hozzáférési kulcs képernyőjét mutatja be a Azure Portal.":::

#### <a name="call-out-descriptions"></a>A felhívások leírása

1. Elérési kulcs
2. Felhasználónév
3. Jelszó

**Az adattár neve a** Azure Container Registry. Adja meg a rendszerképet Azure Container Registry tárház nevét. A rendszerkép regisztrációs adatbázisba való leküldésekor meg kell adnia az adattár nevét. Az adattár nevét az Adattárak lap [Container Registry](https://azure.microsoft.com/services/container-registry/)  >  **találhatja meg.** További információ: [Tároló-beállításjegyzék-adattárak megtekintése](../../container-registry/container-registry-repositories.md)a Azure Portal. A név beállítása után az már nem módosítható. A fiókban minden ajánlathoz egyedi nevet használjon.

> [!NOTE]
> Az Edge-modultanúsításhoz nem Azure Container Registry titkosított tanúsítvány. Azure Container Registry titkosítás engedélyezése nélkül kell létrehozni.

### <a name="image-tags-for-new-versions-of-your-offer"></a>Rendszerképcímkék az ajánlat új verzióihoz

Az ügyfeleknek automatikusan le kell tudni szerezniük a frissítéseket a Azure Marketplace amikor Ön közzétesz egy frissítést. Ha nem kívánják frissíteni, akkor a rendszerkép egy adott verzióján kell maradniuk. Ezt úgy teheti meg, hogy minden alkalommal új képcímkéket ad hozzá, amikor frissíti a rendszerképet.

**Képcímke.** Ennek a mezőnek tartalmaznia kell egy **legújabb** címkét, amely a rendszerkép legújabb verziójára mutat az összes támogatott platformon. Tartalmaznia kell egy verziócímkét is (például xx.xx.xx-xx-től kezdve, ahol az xx egy szám). Az ügyfeleknek [jegyzékcímkéket kell használniuk](https://github.com/estesp/manifest-tool) több platform megcélhoz. A jegyzékcímkék által hivatkozott összes címkét is hozzá kell adni, hogy feltöltjük őket. Minden jegyzékcímkének (a legújabb címke kivételével) X.Y- vagy X.Y.Z-vel kell kezdődnie, ahol az X, az Y és a Z egész szám. Ha például egy legújabb címke a 1.0.1-linux-x64, 1.0.1-linux-arm32 és 1.0.1-windows-arm32 címkékre mutat, akkor ezt a hat címkét kell hozzáadni ehhez a mezőhöz. A címkékkel és verziószámozással kapcsolatos részletekért lásd a IoT Edge [modul technikai eszközeinek előkészítését.](create-iot-edge-module-asset.md)

### <a name="default-deployment-settings-optional"></a>Alapértelmezett központi telepítési beállítások (nem kötelező)

Határozza meg a modul üzembe helyezésének leggyakoribb IoT Edge beállításait. Optimalizálhatja az ügyfelek üzemelő példányát, ha lehetővé IoT Edge, hogy az alapértelmezett beállításokkal elindítsa a modult.

**Alapértelmezett útvonalak:**. A IoT Edge Hub kezeli a modulok, a IoT Hub és az eszközök közötti kommunikációt. A modulok és a IoT Hub között beállíthatja az adatbemenet és -kimenet útvonalait, így rugalmasan küldhet olyan üzeneteket, ahová kell, anélkül, hogy további szolgáltatásokra lenne szükség az üzenetek feldolgozásához vagy további kód írásához. Az útvonalak név-érték párok használatával vannak összeépítve. Legfeljebb öt alapértelmezett útvonalnevet határozhat meg, amelyek akár 512 karakter hosszúak is lehet.

Győződjön meg arról, [](../../iot-edge/module-composition.md#declare-routes)hogy a megfelelő útvonalszintaxist használja az útvonal értékében (általában FROM/message/* INTO $upstream). Ez azt jelenti, hogy a modulok által küldött üzenetek a saját IoT Hub. A modulra való hivatkozáshoz használja az alapértelmezett modulnevét, amely az Ajánlat neve **lesz** szóközök vagy speciális karakterek nélkül. A még nem ismert modulokra való hivatkozáshoz használja a <FROM_MODULE_NAME> konvenciót, amely tudatja az ügyfelekkel, hogy frissíteniük kell ezt az információt. Az útvonalakkal kapcsolatos IoT Edge lásd: [Útvonalak deklarálása).](../../iot-edge/module-composition.md#declare-routes)

Ha például a ContosoModule modul a ContosoInput és a kimeneti adatok bemenetét figyeli a ContosoOutput-on, célszerű meghatározni a következő két alapértelmezett útvonalat:

- Név #1: ToContosoModule
- Érték #1: FROM /messages/modules/<FROM_MODULE_NAME>/outputs/* INTO BrokeredEndpoint("/modules/ContosoModule/inputs/ContosoInput")
- Név #2: FromContosoModuleToCloud
- Érték #2: FROM /messages/modules/ContonsoModule/outputs/ContosoOutput INTO $upstream

**Alapértelmezett ikermodul kívánt tulajdonságai.** A modul ikerdokumentuma egy JSON-dokumentum a IoT Hub, amely egy modulpéldány állapotinformációit tárolja, beleértve a kívánt tulajdonságokat is. A modul konfigurációjának vagy feltételeinek szinkronizálása a kívánt tulajdonságokkal és jelentett tulajdonságokkal együtt használatos. A megoldás háttérmodulja beállíthatja a kívánt tulajdonságokat, és a modul olvashatja őket. A modul változásértesítéseket is fogadhat a kívánt tulajdonságokban. A kívánt tulajdonságok legfeljebb öt név/érték pár használatával lesznek létrehozva, és minden alapértelmezett értéknek 512 karakternél kevesebbnek kell lennie. Legfeljebb öt kívánt név/érték ikertulajdonság definiálható. Az ikertulajdonságok értékeinek érvényes JSON-nak kell lennie, nem escape-escape-vel, tömbök nélkül, legfeljebb négy szintbe ágyazott hierarchiával. Olyan esetekben, amikor az alapértelmezett értékhez szükséges paraméternek nincs értelme (például egy ügyfél kiszolgálójának IP-címe), hozzáadhat egy paramétert alapértelmezett értékként. További információ az ikertulajdonságokról: [Kívánt tulajdonságok meghatározása vagy frissítése.](../../iot-edge/module-composition.md#define-or-update-desired-properties)

Ha például egy modul ikertulajdonságok használatával támogatja a dinamikusan konfigurálható frissítési sebességet, logikus a következő alapértelmezett ikertulajdonság meghatározása:

- Név #1: RefreshRate
- Érték #1: 60

**Alapértelmezett környezeti változók:**. A környezeti változók kiegészítő információkat biztosítanak egy modulhoz, amely segíti a konfigurációs folyamatot. A környezeti változók név/érték párok használatával vannak létrehozva. Minden alapértelmezett környezeti változó nevének és értékének 512 karakternél rövidebbnek kell lennie, és legfeljebb öt karakterből állhat. Ha egy alapértelmezett értékhez szükséges paraméternek nincs értelme (például egy ügyfél kiszolgálójának IP-címe), hozzáadhat egy paramétert alapértelmezett értékként.

Ha például egy modulnak el kell fogadnia a használati feltételeket az első lépések előtt, a következő környezeti változót definiálhatja:

- Név #1: ACCEPT_EULA
- Érték #1: Y

**Alapértelmezett tároló-létrehozási beállítások.** A tároló-létrehozási lehetőségek a IoT Edge Docker-tároló létrehozását irányítják. IoT Edge támogatja a Docker Engine API Tároló létrehozása beállításait. Az összes lehetőséget a Tárolók [listába sorolásnál láthatja.](https://docs.docker.com/engine/api/v1.30/#operation/ContainerList) A Beállítás létrehozása mezőnek érvényes JSON-nak, nem escape-karakterrel rendelkezőnek és 512 karakternél rövidebbnek kell lennie.

Ha például egy modul portkötést igényel, adja meg a következő létrehozási beállításokat:

"HostConfig":{"PortBindings":{"5012/tcp":[{"HostPort":"5012"}]}

## <a name="review-and-publish"></a>Felülvizsgálat és közzététel

Miután befejezte az ajánlat összes szükséges szakaszát, elküldheti felülvizsgálatra és közzétételre.

A portál jobb felső sarkában válassza az Áttekintés **és közzététel lehetőséget.**

A felülvizsgálati oldalon a közzététel állapota látható:

- Tekintse meg az ajánlat egyes szakaszának befejezési állapotát. Csak akkor tehet közzé közzétételt, ha az ajánlat minden szakasza befejezettként van megjelölve.
    - **Nincs elindítva** – A szakasz még nem indult el, és be kell fejeződni.
    - **Hiányos** – A szakasz olyan hibákat tartalmaz, amelyek kijavítása szükséges, vagy további információkra van szükség. Útmutatásért tekintse meg a dokumentum korábbi szakaszait.
    - **Complete** (Kész) – A szakasz minden szükséges adatot tartalmaz, és nincsenek hibák. Az ajánlat elküldését megelőzően az ajánlat minden szakaszának teljesnek kell lennie.
- Adja meg a tesztelési utasításokat a tanúsítási csapatnak, hogy az ajánlata megfelelően tesztelve legyen. Emellett adjon meg minden kiegészítő megjegyzéseket, amelyek hasznosak lehetnek az ajánlat megértéséhez.

Az ajánlat közzétételre való elküldését a Közzététel lehetőséget **választva küldheti el.**

E-mailt küldünk Önnek, amely tudatja, hogy az ajánlat előzetes verziója mikor válik elérhetővé felülvizsgálatra és jóváhagyásra. Az ajánlat nyilvános közzétételéhez kattintson a Partnerközpont **go-live (Élő adás) gombra.**

## <a name="next-steps"></a>Következő lépések

- [Meglévő ajánlat frissítése a kereskedelmi piactéren](update-existing-offer.md)
