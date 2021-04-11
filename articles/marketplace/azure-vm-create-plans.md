---
title: Csomagok létrehozása virtuálisgép-ajánlathoz az Azure Marketplace-en
description: Megtudhatja, hogyan hozhat létre terveket egy virtuálisgép-ajánlathoz az Azure Marketplace-en.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 04/01/2021
ms.openlocfilehash: b1111caf2eaab1114371cbfed1e929afcff20898
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2021
ms.locfileid: "106219365"
---
# <a name="how-to-create-plans-for-a-virtual-machine-offer"></a>Csomagok létrehozása virtuálisgép-ajánlathoz

A **terv áttekintése** lapon (válassza ki a partner Center bal oldali NAV menüjéből) különböző tervezési lehetőségeket biztosíthat ugyanazon az ajánlaton belül. Az ajánlathoz legalább egy csomagra (korábbi nevén SKU) van szükség, amely a bevételi közönség, az Azure-régió, a funkciók vagy a virtuálisgép-lemezképek esetében változhat.

Az egyes ajánlatokhoz legfeljebb 100 csomagot hozhat létre: ezek közül legfeljebb 45 lehet privát. További információ a [Microsoft kereskedelmi piactéren](private-offers.md)elérhető privát csomagokról.

A csomagok létrehozása után válassza a **terv áttekintés** fület a megjelenítéshez:

- Csomag neve
- Licencelési modellek
- Közönség (nyilvános vagy privát)
- Jelenlegi közzétételi állapot
- Elérhető műveletek

A **terv áttekintő** ablaktábláján elérhető műveletek a csomag aktuális állapotától függően változhatnak.

- Ha a terv állapota vázlat, válassza a **Piszkozat törlése** lehetőséget.
- Ha a terv állapota élőben van közzétéve, válassza az **eladási terv leállítása** vagy a **privát célközönség szinkronizálása** lehetőséget.

## <a name="create-a-new-plan"></a>Új csomag létrehozása

Válassza az **+ új csomag létrehozása** lehetőséget a felső részen.

Az **új terv** párbeszédpanelen adjon meg egy egyedi díjcsomag- **azonosítót** az ajánlat minden csomagjának. Ez az azonosító a termék webcímen lévő ügyfelek számára látható lesz. Csak kisbetűket és számokat, kötőjeleket vagy aláhúzásokat, illetve legfeljebb 50 karaktert használjon.

> [!NOTE]
> A terv azonosítója a **Létrehozás** gombra kattintva nem módosítható.

Adja meg a **csomag nevét**. Az ügyfelek akkor látják ezt a nevet, amikor döntik el, hogy melyik tervet kívánják kiválasztani az ajánlaton belül. Hozzon létre egy egyedi nevet, amely egyértelműen rámutat a csomagok közötti különbségekre. Például megadhatja a **Windows Servert** utólagos elszámolású, *BYOL*, *speciális* és *vállalati* csomagokkal.

Válassza a **Létrehozás** lehetőséget. Ekkor megnyílik a **terv beállítása** lap.

## <a name="plan-setup"></a>Csomag beállítása

Állítsa be a terv típusának magas szintű konfigurációját, adja meg, hogy az újrahasznosítsa-e egy másik csomag technikai konfigurációját, és azonosítsa azokat az Azure-régiókat, ahol a terv elérhető. Az itt megadott beállítások határozzák meg, hogy mely mezők jelenjenek meg ugyanazon csomag más ablaktábláján.

### <a name="reuse-technical-configuration"></a>Technikai konfiguráció újrafelhasználása

Ha több azonos típusú csomaggal rendelkezik, és a csomagok azonosak egymás között, akkor kiválaszthatja, hogy a **terv újrahasznosítja a technikai konfigurációt egy másik csomagból**. Ez a beállítás lehetővé teszi, hogy az ajánlathoz tartozó más csomagok valamelyikét kiválassza, és lehetővé teszi a technikai konfigurációjának újbóli használatát.

> [!NOTE]
> Ha egy másik csomag technikai konfigurációját használja, a teljes **technikai konfiguráció** lap eltűnik ebből a csomagból. A csomagra vonatkozó technikai konfiguráció részleteit, beleértve a jövőbeli frissítéseket is, a tervhez is használni fogjuk. Ez a beállítás a terv közzététele után nem módosítható.

### <a name="azure-regions"></a>Azure-régiók

A tervet legalább egy Azure-régióban elérhetővé kell tenni.

Válassza az **Azure Global** lehetőséget, hogy a tervet az összes olyan Azure-beli globális régióban elérhetővé tegye, amely kereskedelmi Piactéri integrációt is tartalmaz. További információ: a [földrajzi Elérhetőség és a pénznemek támogatása](marketplace-geo-availability-currencies.md).

Válassza a **Azure Government** lehetőséget, hogy a terv elérhető legyen a [Azure Government](../azure-government/documentation-government-welcome.md) régióban. Ez a régió szabályozott hozzáférést biztosít az Egyesült Államok szövetségi, állami, helyi vagy törzsi szerveinek ügyfeleinek, valamint az azok kiszolgálására jogosult partnereinknek. Ön, mint közzétevő, felelős a megfelelőségi ellenőrzésért, a biztonsági intézkedésekért és az ajánlott eljárásokhoz. A Azure Government fizikailag elkülönített adatközpontokat és hálózatokat használ (csak az USA-ban található).

Mielőtt közzéteszi a [Azure Government](../azure-government/documentation-government-manage-marketplace-partners.md), tesztelje és érvényesítse a tervet a környezetben, mert bizonyos végpontok eltérőek lehetnek. A csomag beállításához és teszteléséhez kérjen egy próbaverziós fiókot a [Microsoft Azure Government próbaverzió](https://azure.microsoft.com/global-infrastructure/government/request/) oldaláról.

> [!NOTE]
> Miután közzétette és elérhetővé tette a csomagot egy adott Azure-régióban, nem távolíthatja el a régiót.

### <a name="azure-government-certifications"></a>Azure Government minősítések

Ez a beállítás csak akkor látható, ha az előző szakaszban az Azure-régiót választotta **Azure Government** .

Azure Government szolgáltatások kezelik bizonyos kormányzati előírások és követelmények hatálya alá eső adatmennyiséget. Például: FedRAMP, NIST 800,171 (DIB), ITAR, IRS 1075, DoD L4 és CJIS. A programok minősítésének megismeréséhez akár 100 hivatkozást is megadhat, amely leírja azokat. Ezek lehetnek közvetlenül a programra mutató hivatkozások vagy a saját webhelyein való megfelelőség leírására mutató hivatkozások. Ezek a hivatkozások csak Azure Government ügyfelek számára láthatók.

Válassza a **Piszkozat mentése** lehetőséget, mielőtt továbblépne a bal oldali NAV-terv menü következő fülére, majd **tervezze** meg a listát.

## <a name="plan-listing"></a>Csomag listázása

Adja meg a csomag listázási részleteit. Ez a panel a konkrét információkat jeleníti meg, amelyek eltérőek lehetnek az azonos ajánlatban található többi csomagtól.

### <a name="plan-name"></a>Csomag neve

A rendszer automatikusan kitölti ezt a mezőt azzal a névvel, amelyet a tervének létrehozásakor adott meg. Ez a név jelenik meg az Azure Marketplace-en a csomag címeként. Legfeljebb 100 karakter hosszú lehet.

### <a name="plan-summary"></a>Csomag összegzése

Adja meg a csomag rövid összefoglalását, nem az ajánlatot. Ez az Összegzés legfeljebb 100 karakter hosszú lehet.

### <a name="plan-description"></a>Csomag leírása

Írja le, hogy mi teszi ezt a csomagot egyedivé, és ismertesse az ajánlaton belüli csomagok közötti különbségeket. Csak a csomag leírása, nem az ajánlat. A csomag leírása legfeljebb 2 000 karaktert tartalmazhat.

A bal oldali NAV-terv menü következő lapján a Tovább gombra kattintva válassza a **Piszkozat mentése** lehetőséget, **majd a díjszabást és a rendelkezésre állást**.

## <a name="pricing-and-availability"></a>Díjszabás és rendelkezésre állás

Ezen az ablaktáblán a következőket konfigurálja:

- Azok a piacok, ahol ez a csomag elérhető. Minden csomagnak legalább egy [piacon](marketplace-geo-availability-currencies.md)elérhetőnek kell lennie.
- Az óránkénti díj.
- Azt határozza meg, hogy a terv mindenki számára elérhető legyen-e, vagy csak bizonyos ügyfelek számára (privát közönség számára).

### <a name="markets"></a>Piacok

Minden csomagnak legalább egy piacon elérhetőnek kell lennie. Alapértelmezés szerint a legtöbb piac ki van választva. A lista szerkesztéséhez válassza a **piacok szerkesztése** lehetőséget, és jelölje be vagy törölje a jelölőnégyzeteket az egyes piaci helyekhez, ahol a tervnek (vagy nem) elérhetőnek kell lennie a vásárláshoz. A kiválasztott piacokon lévő felhasználók továbbra is üzembe helyezhetik az ajánlatot a ["csomag beállítása"](#plan-setup) szakaszban kiválasztott összes Azure-régióban.

Jelölje be a **csak a Microsoft által átutalt adók** kiválasztása lehetőséget, ha csak azokat az országokat/régiókat szeretné kijelölni, amelyekben a Microsoft az Ön nevében értékesítési és használati adót A Kínában való közzététel olyan csomagokra korlátozódik, amelyek *ingyenesek* vagy *saját licencek* (BYOL).

Ha már beállította a csomag árát az amerikai dollár (USD) pénznemben, és újabb piaci helyet ad hozzá, az új piac díját az aktuális árfolyamok alapján számítjuk ki. A közzététel előtt mindig tekintse át az egyes piacok árát. A módosítások mentése után tekintse át a díjszabást az **export Prices (xlsx)** lehetőség kiválasztásával.

Ha eltávolít egy piacot, az adott piacon aktív üzemelő példányokat használó ügyfelek nem hozhatnak létre új központi telepítéseket, és nem méretezhetők a meglévő telepítések. A meglévő központi telepítések nincsenek hatással a rendszerre.

A folytatáshoz válassza a **Mentés** lehetőséget.

### <a name="pricing"></a>Díjszabás

A **licencelési modell** esetében válassza a **használati alapú havi számlázott csomag** lehetőséget a csomag díjszabásának konfigurálásához, vagy **a saját licencét** , hogy az ügyfelek ezt a csomagot használják meglévő licenccel.

A használati alapú havi számlás csomag esetében használja a következő három árlista-beállítás egyikét:

- **/Mag** – az USD-alapú alapszintű díjszabást adja meg. A Microsoft a jelenlegi árfolyam alapján kiszámítja a díjszabást az alapméret szerint, és helyi pénznemre alakítja.
- **/Alapméret** – az USD-ben elérhető alapméretek díjszabása. A Microsoft kiszámítja a díjszabást, és helyi pénznemre konvertálja az aktuális árfolyam használatával.
- **Piaci és alapméret** szerint – az összes piac alapméretének díjszabását adja meg. Az árakat importálhatja egy táblázatból.

Adjon meg egy **alapszintű árat**, majd válassza a díj/ **alapméret** lehetőséget, hogy megjelenjen az ár/óra számítások táblázata.

> [!NOTE]
> Mentse a díjszabási változásokat, hogy engedélyezze a díjszabási adatexportálást. Miután közzétette a csomag egy piacának árát, később nem módosítható. Annak érdekében, hogy a közzététel előtt a díjak megfelelőek legyenek, exportálja a díjszabási táblázatot, és tekintse át az árakat az egyes piacokon.

### <a name="free-trial"></a>Ingyenes próba

Az ügyfeleknek egy-, három-vagy hat hónapos **ingyenes próbaverziót** is biztosíthat.

### <a name="plan-visibility"></a>Terv láthatósága

Megtervezheti, hogy az egyes tervek mindenki számára láthatók legyenek, vagy csak egy kijelölt közönség számára. Az Azure-előfizetési azonosítók használatával társíthat tagságokat ebben a korlátozott célközönségben.

**Nyilvános**: a terv mindenki számára megtekinthető.

**Privát**: a terv csak az előkiválasztott közönség számára látható. Miután közzétette saját csomagként, frissítheti a célközönséget, vagy megváltoztathatja nyilvánosra. Miután megnyitotta a csomagot, nyilvánosnak kell maradnia. Nem módosítható egy privát csomagra.

Rendelje hozzá azt a célközönséget, amely hozzáfér ehhez a privát csomaghoz az **Azure-előfizetés-azonosító** s használatával. Igény szerint az egyes hozzárendelt Azure-előfizetési AZONOSÍTÓk **leírását** is megadhatja. Akár 10 előfizetés-azonosítót is hozzáadhat manuálisan, akár 20 000-re, ha CSV-táblázatot importál. Az Azure-előfizetési azonosítók GUID azonosítóként jelennek meg, és minden betűnek kisbetűsnek kell lennie.

> [!NOTE]
> A privát vagy korlátozott célközönség nem azonos az **előnézeti ablaktáblán** meghatározott előnézeti célközönséggel. Az előzetes verzió célközönsége az Azure Marketplace-en való élő közzététel *előtt* elérheti az ajánlatát. Bár a privát célközönség választása csak egy adott csomagra vonatkozik, az előzetes verzió célközönsége az összes magán-és nyilvános csomagot megtekintheti érvényesítés céljából.

A privát ajánlatok nem támogatottak a Cloud Solution Provider program (CSP) viszonteladóján keresztül létesített Azure-előfizetések esetében.

### <a name="hide-plan"></a>Terv elrejtése

Ha a virtuális gépet csak közvetetten kell használni, ha egy másik megoldási sablonon vagy felügyelt alkalmazáson keresztül hivatkozik rá, jelölje be ezt a jelölőnégyzetet a virtuális gép közzétételéhez, de az olyan ügyfelektől, akik közvetlenül keresnek vagy böngészhetnek.

Bármely Azure-ügyfél a PowerShell vagy a parancssori felület használatával telepítheti az ajánlatot.  Ha azt szeretné, hogy az ajánlat korlátozott számú ügyfél számára legyen elérhető, akkor állítsa a tervet **privát** értékre. 

A rejtett csomagok nem hoznak elő előzetes verziójú hivatkozásokat. Ezeket a lépéseket azonban a [következő lépésekkel](azure-vm-create-faq.md#how-do-i-test-a-hidden-preview-image)ellenőrizheti. 

Válassza a **Piszkozat mentése** lehetőséget, mielőtt továbblépne a bal oldali NAV-terv menü következő fülére, a **technikai konfigurációban**.

## <a name="technical-configuration"></a>Technikai konfiguráció

Adja meg a csomaghoz tartozó képeket és egyéb technikai tulajdonságokat.

> [!NOTE]
> Ez a lap nem jelenik meg, ha úgy konfigurálta ezt a csomagot, hogy a csomagok egy másikból való újrafelhasználását a **terv beállítása** lapon.

### <a name="operating-system"></a>Operációs rendszer

Válassza ki a **Windows** vagy **Linux** operációs rendszer családját.

Válassza ki a Windows **kiadási** vagy linuxos **gyártót**.

Adja meg az operációs rendszer egy **felhasználóbarát nevét** . Ez a név jelenik meg az ügyfelek számára.

### <a name="recommended-vm-sizes"></a>Ajánlott virtuálisgép-méretek

Válassza ki a hivatkozást, amely legfeljebb hat ajánlott virtuálisgép-méretet választ ki az Azure Marketplace-en való megjelenítéshez.

### <a name="open-ports"></a>Portok megnyitása

Nyisson meg nyilvános vagy privát portot egy telepített virtuális gépen.

### <a name="properties"></a>Tulajdonságok

Válassza ki, hogy a virtuális gép támogatja-e a **gyorsított hálózatkezelést**. Részletekért lásd a [gyorsított hálózatkezelést](https://go.microsoft.com/fwlink/?linkid=2124513)ismertető témakört.

### <a name="generations"></a>Generációk

A virtuális gép létrehozása határozza meg az általa használt virtuális hardvert. Az ügyfél igényei alapján közzétehet egy 1. generációs virtuális gépet, 2. generációs virtuális gépet vagy mindkettőt.

1. Új ajánlat létrehozásakor válasszon egy **generációs típust** , és adja meg a kért adatokat:

    :::image type="content" source="./media/create-vm/azure-vm-generations-image-details-1.png" alt-text="A partner Center létrehozási részletei szakaszának nézete.":::

2. Ha másik generációt szeretne hozzáadni egy csomaghoz, válassza a **Létrehozás hozzáadása**... lehetőséget.

    :::image type="content" source="./media/create-vm/azure-vm-generations-add.png" alt-text="A &quot;generáció hozzáadása&quot; hivatkozás nézete.":::

    ... adja meg a kért adatokat:

    :::image type="content" source="./media/create-vm/azure-vm-generations-image-details-3.png" alt-text="A generációs részletek ablakának nézete.":::

<!--    The **Generation ID** you choose will be visible to customers in places such as product URLs and ARM templates (if applicable). Use only lowercase, alphanumeric characters, dashes, or underscores; it cannot be modified once published.
-->
3. Ha olyan meglévő virtuális gépet szeretne frissíteni, amelynek 1. generációja már közzé van téve, szerkessze a részleteket a **technikai konfiguráció** lapon.

Ha többet szeretne megtudni az 1. és 2. generációs képességek közötti különbségekről, tekintse meg a [2. generációs virtuális gépek támogatása az Azure](../virtual-machines/generation-2.md)-ban című témakört.

### <a name="vm-images"></a>VM-lemezképek

Adja meg a lemez verziószámát és a virtuálisgép-lemezképek megosztott elérési aláírásának (SAS) URI-JÁT. Minden virtuálisgép-rendszerképhez akár 16 adatlemezt is hozzáadhat. Egy adott beadványban csak egy új rendszerkép-verziót adjon meg. A rendszerkép közzététele után nem szerkesztheti, de törölheti is. Egy verzió törlése megakadályozza, hogy az új és a meglévő felhasználók is üzembe lehessen helyezni a törölt verzió új példányát.

Ez a két kötelező mező a fenti előző képen látható:

- **Lemez verziója**: a megadott lemezkép verziója.
- **Operációs rendszer VHD-hivatkozása**: az Azure Storage-fiókjának helye az operációs rendszer VHD-je számára. A SAS URI beszerzésével kapcsolatos további információkért lásd: [a megosztott hozzáférési aláírás URI-ja](azure-vm-get-sas-uri.md)a virtuálisgép-rendszerképhez.

Az adatlemezek (válassza az **adatlemez hozzáadása (legfeljebb 16)** lehetőséget) az Azure Storage-fiókokban tárolt VHD közös hozzáférésű URI-azonosítók is. Egy csomagban való beküldéshez csak egy képet adjon hozzá.

A használt operációs rendszertől függetlenül csak a megoldás által igényelt adatlemezek minimális számát adja hozzá. Az üzembe helyezés során az ügyfelek nem tudják eltávolítani a rendszerkép részét képező lemezeket, de mindig hozzáadhatnak lemezeket az üzembe helyezés során vagy azt követően is.

Válassza a **Piszkozat mentése** lehetőséget, majd kattintson a bal felső sarokban található **← terv áttekintése** lehetőségre az imént létrehozott terv megtekintéséhez.

A virtuálisgép-rendszerkép közzététele után törölheti a rendszerképet az Azure Storage-ból.

## <a name="next-steps"></a>Következő lépések

- [Viszonteladás CSP-ken keresztül](azure-vm-create-resell-csp.md)
