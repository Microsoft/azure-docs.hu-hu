---
title: Azure Monitor munkafüzetek hivatkozásának műveletei
description: A Azure Monitor-munkafüzetekben található kapcsolati műveletek használata
ms.topic: conceptual
ms.date: 01/07/2021
author: lgayhardt
ms.author: lagayhar
ms.openlocfilehash: 2d1abe6d77dd4293dc70a07f3b8fd5d9da78d01e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100616038"
---
# <a name="link-actions"></a>Hivatkozásműveletek

A kapcsolati műveletek a munkafüzet hivatkozás lépésein vagy a [rácsok](../visualize/workbooks-grid-visualizations.md), [címek](../visualize/workbooks-tile-visualizations.md)vagy [diagramok](../visualize/workbooks-graph-visualizations.md)oszlopainak beállításain keresztül érhetők el.

## <a name="general-link-actions"></a>Általános kapcsolási műveletek

| Művelet csatolása | Művelet kattintáskor |
|:------------- |:-------------|
| `Generic Details` | Megjeleníti a sortulajdonság-környezet nézet sorainak értékeit. |
| `Cell Details` | Megjeleníti a cella értékét egy tulajdonság rácsának nézetében. Hasznos, ha a cella tartalmaz egy dinamikus típust (például a JSON-t a kérelem tulajdonságaival, például a helytel, a szerepkör-példánnyal stb.). |
| `Url` | A cella értékének egy érvényes HTTP URL-címnek kell lennie, és a cella egy hivatkozás, amely egy új lapon nyitja meg az URL-címet.|

## <a name="application-insights"></a>Application Insights

| Művelet csatolása | Művelet kattintáskor |
|:------------- |:-------------|
| `Custom Event Details` | Megnyitja a Application Insights keresés részleteit az egyéni eseményazonosító ( `itemId` ) alapján a cellában. |
| `* Details` | Az egyéni esemény részleteihez hasonlóan, a függőségek, a kivételek, az oldalletöltések, a kérések és a Nyomkövetések kivételével. |
| `Custom Event User Flows` | Megnyitja a cellában az egyéni esemény nevében kimutatott Application Insights Felhasználókövetési élményt. |
| `* User Flows` | Az egyéni eseményekhez hasonlóan Felhasználókövetés a kivételek, az oldalletöltések és a kérelmek kivételével. |
| `User Timeline` | Megnyitja a felhasználói idővonalat a felhasználói AZONOSÍTÓval ( `user_Id` ) a cellában. |
| `Session Timeline` | Megnyitja a cella értékének Application Insights keresési élményét (például az "ABC" szöveg keresése, ahol az ABC a cella értéke). |

`*` a fenti táblázathoz tartozó helyettesítő karaktert jelöli.

## <a name="azure-resource"></a>Azure-erőforrás

| Művelet csatolása | Művelet kattintáskor |
|:------------- |:-------------|
| `ARM Deployment` | Azure Resource Manager-sablon üzembe helyezése.  Ha ez az elem be van jelölve, további mezők jelennek meg, amelyek lehetővé teszik, hogy a szerző konfigurálja a megnyíló Azure Resource Manager sablont, a sablon paramétereit stb. [lásd: Azure Resource Manager központi telepítési hivatkozás beállításai](#azure-resource-manager-deployment-link-settings). |
| `Create Alert Rule` | Riasztási szabályt hoz létre egy erőforráshoz.  |
| `Custom View` | Megnyit egy egyéni nézetet. Ha ez az elem be van jelölve, további mezők jelennek meg, hogy a szerző konfigurálja a nézet kiterjesztését, a nézet nevét és a nézet megnyitásához használt paramétereket. [Lásd: egyéni nézet](#custom-view-link-settings). |
| `Metrics` | Megnyit egy mérőszám nézetet.  |
| `Resource overview` | Nyissa meg az erőforrás nézetét a portálon a cella erőforrás-azonosító értéke alapján. A szerző olyan értéket is beállíthat `submenu` , amely egy adott menüelemet nyit meg az erőforrás nézetben. |
| `Workbook (template)` | Nyisson meg egy munkafüzet-sablont.  Ha ez az elem be van jelölve, további mezők jelennek meg, amelyek lehetővé teszik a szerző számára, hogy megnyissa a sablont, stb.  |

## <a name="link-settings"></a>Hivatkozás beállításai

A hivatkozás-megjelenítő használatakor a következő beállítások érhetők el:

![A hivatkozás beállításainak képernyőképe](./media/workbooks-link-actions/link-settings.png)

| Beállítás | Magyarázat |
|:------------- |:-------------|
| `View to open` | Lehetővé teszi a szerző számára a fentiekben felsorolt műveletek egyikének kiválasztását. |
| `Menu item` | Ha az "erőforrás-áttekintés" lehetőség van kiválasztva, a megnyitáshoz az erőforrás áttekintésében található menüpont szerepel. Ez felhasználható a riasztások vagy a tevékenységi naplók megnyitására az erőforrás "áttekintés" helyett. Az egyes Azure-beli menüelemek értékei eltérőek `Resourcetype` .|
| `Link label` | Ha meg van adva, akkor ez az érték jelenik meg a Grid oszlopban. Ha ez az érték nincs megadva, akkor a cella értéke jelenik meg. Ha azt szeretné, hogy egy másik érték jelenjen meg, például egy hő vagy ikon, ne használja a `Link` megjelenítőt, hanem használja a megfelelő megjelenítőt, és válassza a `Make this item a link` lehetőséget. |
| `Open link in Context Blade` | Ha meg van adva, a hivatkozás megjelenik az ablak jobb oldalán, és nem teljes nézetként nyílik meg. |

A kapcsoló használata esetén `Make this item a link` a következő beállítások érhetők el:

| Beállítás | Magyarázat |
|:------------- |:-------------|
| `Link value comes from` | Ha egy cellát megjelenít egy hivatkozással, ez a mező határozza meg, hogy a hivatkozásban milyen "hivatkozás" érték legyen használatban, így a szerző kiválaszthatja a rács többi oszlopának legördülő menüjét. Előfordulhat például, hogy a cella hő érték, de a hivatkozásra kattintva megnyithatja az erőforrás-AZONOSÍTÓhoz tartozó erőforrás-áttekintést a sorban. Ebben az esetben a hivatkozás értékét úgy kell beállítani, hogy a `Resource Id` mezőből származik.
| `View to open` | ugyanaz, mint a fenti. |
| `Menu item` | ugyanaz, mint a fenti. |
| `Open link in Context Blade` | ugyanaz, mint a fenti. |

## <a name="azure-resource-manager-deployment-link-settings"></a>Azure Resource Manager központi telepítési hivatkozás beállításai

Ha a kiválasztott hivatkozás típusa `ARM Deployment` a szerző, további beállításokat kell megadnia Azure Resource Manager központi telepítés megnyitásához. A konfigurációknak két fő lapja van.

### <a name="template-settings"></a>Sablon beállításai

Ez a szakasz határozza meg, hogy a sablon honnan származnak, és milyen paramétereket kell használni a Azure Resource Manager központi telepítésének futtatásához.

| Forrás | Magyarázat |
|:------------- |:-------------|
|`Resource group id comes from` | Az erőforrás-azonosító az üzembe helyezett erőforrások kezelésére szolgál. Az előfizetés az üzembe helyezett erőforrások és költségek kezelésére szolgál. Az erőforráscsoportok, például a mappák, az összes erőforrás rendszerezésére és kezelésére szolgálnak. Ha ez az érték nincs megadva, a telepítés sikertelen lesz. Válassza a következőt:,,, `Cell` `Column` `Static Value` vagy `Parameter` a [forrásban](#link-sources).|
|`ARM template URI from` | Maga a Azure Resource Manager-sablon URI-ja. A sablon URI azonosítójának elérhetőnek kell lennie a sablont telepítő felhasználók számára. Válassza a következőt:,,, `Cell` `Column` `Parameter` vagy `Static Value`  a [forrásban](#link-sources). A kezdők számára tekintse meg a gyors üzembe helyezési [sablonokat](https://azure.microsoft.com/resources/templates/).|
|`ARM Template Parameters` | Ez a szakasz a fent definiált sablon URI-ja által használt sablon-paramétereket határozza meg. Ezeket a paramétereket fogja használni a sablon üzembe helyezéséhez a Futtatás lapon. A rács tartalmaz egy kibontott eszköztár gombot, amely a sablon URI-ja alapján megadott nevekkel segíti a paraméterek kitöltését, és statikus üres értékekre állítja azt. Ez a beállítás csak akkor használható, ha nincsenek paraméterek a rácsban, és a sablon URI-ja be van állítva. Az alsó szakasz egy előnézet, amely a paraméter kimenetét hasonlítja. Válassza a frissítés lehetőséget az előnézet aktuális módosításokkal való frissítéséhez. A paraméterek jellemzően értékek, míg a hivatkozások olyanok, amelyek a felhasználó által elérhetővé tett kulcstartó-titkokra mutatnak. <br/><br/> **Sablon megjelenítője – korlátozás** – nem jeleníti meg helyesen a hivatkozási paramétereket, és null/értékként jelenik meg, így a felhasználók nem tudják helyesen telepíteni a hivatkozási paramétereket a sablon megjelenítője lapról.|

![Képernyőkép Azure Resource Manager sablon beállításairól](./media/workbooks-link-actions/template-settings.png)

### <a name="ux-settings"></a>UX-beállítások

Ez a szakasz azt konfigurálja, hogy a felhasználók mit látnak a Azure Resource Manager központi telepítésének futtatása előtt.

| Forrás | Magyarázat |
|:------------- |:-------------|
|`Title from` | A Futtatás nézetben használt cím. Válassza a következőt:,,, `Cell` `Column` `Parameter` vagy `Static Value` a [forrásban](#link-sources).|
|`Description from` | Ez a Markdown-szöveg, amely hasznos leírást nyújt a felhasználóknak a sablon üzembe helyezéséhez. Válassza a következőt:,,, `Cell` `Column` `Parameter` vagy `Static Value`  a [forrásban](#link-sources). <br/><br/> **Megjegyzés:** Ha `Static Value` be van jelölve, egy többsoros szövegmező jelenik meg. Ebben a szövegmezőben a következő paranccsal oldhatók fel paraméterek: `{paramName}` . Az oszlopokat paraméterként is kezelheti, ha `_column` az oszlop nevét (például `{columnName_column}` ) adja hozzá. Az alábbi ábrán az oszlopra hivatkozhatunk `VMName` írással `{VMName_column}` . A kettőspont utáni érték a [formázó paraméter](../visualize/workbooks-parameters.md#parameter-options), ebben az esetben ez a következő: `value` .|
|`Run button text from` | A Futtatás (végrehajtás) gombon használt címke a Azure Resource Manager sablon üzembe helyezéséhez. A felhasználók a Azure Resource Manager sablon üzembe helyezésének megkezdéséhez választanak.|

![Képernyőkép a Azure Resource Manager UX-beállításokról](./media/workbooks-link-actions/ux-settings.png)

Miután beállította ezeket a konfigurációkat, a felhasználók a hivatkozásra kattintva megnyitják a nézetet a UX beállításokban ismertetett UX-sel. Ha a felhasználó kiválasztja `Run button text from` , egy Azure Resource Manager sablont fog telepíteni a [sablon beállításaiból](#template-settings)származó értékek alapján. A sablon megtekintése lapon megnyílik a sablon megjelenítője lap, amely megvizsgálja a sablont és a paramétereket a telepítés előtt.

![Képernyőkép a Run Azure Resource Manager nézetről](./media/workbooks-link-actions/run-tab.png)

## <a name="custom-view-link-settings"></a>Egyéni nézet hivatkozásának beállításai

Ezzel megnyithatja az Azure Portal egyéni nézeteit. Ellenőrizze az összes konfigurációt és beállítást. A helytelen értékek hibát okoznak a portálon, vagy nem tudják megfelelően megnyitni a nézeteket. Két módon konfigurálhatja a beállításokat a vagy a használatával `Form` `URL` .

> [!NOTE]
> A menüvel rendelkező nézetek nem nyithatók meg a környezet lapon. Ha egy menüt tartalmazó nézet úgy van konfigurálva, hogy a környezet lapon megnyíljon, akkor a hivatkozás kiválasztásakor a rendszer nem jeleníti meg a kontextus fület.

### <a name="form"></a>Űrlap

| Forrás | Magyarázat |
|:------------- |:-------------|
|`Extension name` | A nézet nevét tároló bővítmény neve.|
|`View name` | A megnyitni kívánt nézet neve.|

#### <a name="view-inputs"></a>Bemenetek megtekintése

A bemenetek, a rácsok és a JSON két típusa van. `Grid`Az egyszerű kulcs és érték lap bemenetekhez használható, vagy válassza a `JSON` beágyazott JSON-bevitel megadását.

- Rács
    - `Parameter Name`: A View bemeneti paraméter neve.
    - `Parameter Comes From`: Hol kell származnia a View paraméter értéke. Válassza a következőt:,,, `Cell` `Column` `Parameter` vagy `Static Value` a [forrásban](#link-sources).
    > [!NOTE]
    > Ha be `Static Value` van jelölve, a paraméterek a szövegmezőben a zárójelek hivatkozás használatával oldhatók fel `{paramName}` . Az oszlopokat paraméterek oszlopaiként lehet kezelni úgy, hogy hozzáfűzi `_column` az oszlop nevét, például: `{columnName_column}` .

    - `Parameter Value`: attól függően `Parameter Comes From` , hogy ez az elérhető paraméterek, oszlopok vagy statikus értékek legördülő listája lesz.

    ![Képernyőkép az oszlop szerkesztése beállításról az egyéni nézet beállításainak megjelenítése az űrlapról.](./media/workbooks-link-actions/custom-tab-settings.png)
- JSON
    - Adja meg a TAB bemenetét JSON formátumban a szerkesztőben. A `Grid` metódushoz, a paraméterekhez és az oszlopokhoz hasonlóan hivatkozhat a `{paramName}` for paraméterek és `{columnName_column}` az oszlopok esetében is. A elem kiválasztásával `Show JSON Sample` megjeleníti az összes megoldott paraméter és oszlop felhasználójának várt kimenetét a nézet bemenetéhez.

    ![Képernyőfelvétel: az egyéni nézet beállításainak megjelenítése a JSON-on.](./media/workbooks-link-actions/custom-tab-json.png)

### <a name="url"></a>URL-cím

Illesszen be egy portál URL-címét, amely tartalmazza a kiterjesztést, a nézet nevét és a nézet megnyitásához szükséges összes bemenetet. A kiválasztása után `Initialize Settings` feltölti a `Form` szerzőt a megtekintési bemenetek hozzáadására/módosítására/eltávolítására.

![Képernyőfelvétel: oszlop beállítása egyéni nézet beállításainak megjelenítése az URL-címről. ](./media/workbooks-link-actions/custom-tab-settings-url.png)

## <a name="workbook-template-link-settings"></a>Munkafüzet (sablon) hivatkozásának beállításai

Ha a kiválasztott hivatkozás típusa `Workbook (Template)` , a szerzőnek további beállításokat kell megadnia a megfelelő munkafüzet-sablon megnyitásához. Az alábbi beállításokkal megtudhatja, hogy a rács hogyan fogja megtalálni a megfelelő értéket az egyes beállításokhoz.

| Beállítás | Magyarázat |
|:------------- |:-------------|
| `Workbook owner Resource Id` | Ez a munkafüzet "tulajdonosa" nevű Azure-erőforrás erőforrás-azonosítója. Általában ez egy Application Insights erőforrás vagy egy Log Analytics munkaterület. A Azure Monitor belül ez lehet a literális karakterlánc is `"Azure Monitor"` . A munkafüzet mentésekor a munkafüzet a következőhöz lesz társítva:. |
| `Workbook resources` | A munkafüzetben használt alapértelmezett erőforrást megadó Azure-erőforrás-azonosítók tömbje. Ha például a megnyitott sablon megjeleníti a virtuális gépek metrikáit, itt az értékek a virtuális gépek erőforrás-azonosítói lesznek.  Sokszor a tulajdonos és az erőforrások ugyanazokra a beállításokra vannak beállítva. |
| `Template Id` | A megnyitni kívánt sablon AZONOSÍTÓjának meghatározása. Ha ez egy közösségi sablon a katalógusból (a leggyakoribb eset), akkor a sablon elérési útját előtagként `Community-` , a `Community-Workbooks/Performance/Apdex` `Workbooks/Performance/Apdex` sablonhoz hasonlóan. Ha ez egy mentett munkafüzetre/sablonra mutató hivatkozás, akkor az adott elem teljes Azure-erőforrás-azonosítója. |
| `Workbook Type` | Válassza ki a megnyitni kívánt sablon típusát. A leggyakoribb esetek a vagy a `Default` `Workbook` kapcsoló használatával használják az aktuális munkafüzetben lévő értéket. |
| `Gallery Type` | Ez határozza meg azt a katalógus-típust, amelyet a megnyíló sablon "Gallery" nézetében fog megjelenni. A leggyakoribb esetek a vagy a `Default` `Workbook` kapcsoló használatával használják az aktuális munkafüzetben lévő értéket. |
|`Location comes from` | A Location (hely) mezőt meg kell adni, ha egy adott munkafüzet-erőforrást nyit meg. Ha a hely nincs megadva, a munkafüzet tartalmának megkeresése sokkal lassabb. Ha ismeri a helyet, határozza meg. Ha nem ismeri a helyet, vagy olyan sablont nyit meg, amely nem rendelkezik adott hellyel, akkor hagyja ezt a mezőt "default" értékre.|
|`Pass specific parameters to template` | Válassza ki, hogy meghatározott paramétereket adjon át a sablonnak. Ha be van jelölve, csak a megadott paraméterek lesznek átadva a sablonnak, a rendszer az aktuális munkafüzet összes paraméterét átadja a sablonnak, és ebben az esetben a paraméter *nevének* azonosnak kell lennie mindkét munkafüzetben a paraméter értékének működéséhez.|
|`Workbook Template Parameters` | Ez a szakasz a megcélzott sablonnak átadott paramétereket határozza meg. A névnek egyeznie kell a megcélzott sablonban szereplő paraméter nevével. Válassza ki az értéket a,, `Cell` `Column` `Parameter` és elemből `Static Value` . A név és az érték nem lehet üres, hogy a paramétert átadja a célként megadott sablonnak.|

A fenti beállítások mindegyike esetében a szerzőnek ki kell választania, hogy a csatolt munkafüzetben lévő érték honnan származik. Lásd: [hivatkozások forrása](#link-sources)

A munkafüzet hivatkozásának megnyitásakor az új munkafüzet nézet a fenti beállítások alapján megadott összes érték átadásra kerül.

![Képernyőfelvétel a munkafüzet hivatkozásának beállításairól](./media/workbooks-link-actions/workbook-link-settings.png)

![Képernyőfelvétel a munkafüzet-sablon paramétereinek beállításairól](./media/workbooks-link-actions/workbook-template-link-settings-parameter.png)

## <a name="link-sources"></a>Források összekapcsolása

| Forrás | Magyarázat |
|:------------- |:-------------|
| `Cell` | Ez a cella értékét fogja használni a rácsban a hivatkozás értékeként. |
| `Column` | Ha be van jelölve, egy másik mező jelenik meg, amely lehetővé teszi, hogy a szerző válasszon egy másik oszlopot a rácsban.  A sorhoz tartozó oszlop értéke a hivatkozási értékben lesz felhasználva. Ez általában a rács minden egyes sora számára lehetővé teszi egy másik sablon megnyitását, a mező beállításával `Template Id` `column` vagy a különböző erőforrásokhoz tartozó munkafüzet-sablon megnyitásával, ha a mező olyan `Workbook resources` oszlopra van beállítva, amely egy Azure-erőforrás-azonosítót tartalmaz |
| `Parameter` | Ha be van jelölve, egy másik mező jelenik meg, amely lehetővé teszi, hogy a szerző válasszon egy paramétert. A paraméter értéke az értékre lesz felhasználva, amikor a hivatkozásra kattint |
| `Static value` | Ha bejelöli ezt a beállítást, egy másik mező jelenik meg, amely lehetővé teszi a szerző számára a csatolt munkafüzetben használt statikus érték megadását. Ezt általában akkor érdemes használni, ha a rács összes sora ugyanazt az értéket fogja használni egy mezőnél. |
| `Step` | Használja a munkafüzet aktuális lépésében beállított értéket. Ez gyakori a lekérdezési és mérőszámok lépéseiben, hogy a csatolt munkafüzetben lévő munkafüzet-erőforrásokat a *lekérdezés/mérőszámok lépésben* használt, nem pedig az aktuális munkafüzethez lehessen beállítani. |
| `Workbook` | Használja az aktuális munkafüzetben beállított értéket. |
| `Default` | Használja az alapértelmezett értéket, amelyet a rendszer akkor használ, ha nincs megadva érték. Ez gyakori a katalógus típusához, ahol az alapértelmezett katalógust a tulajdonosi erőforrás típusa állítja be |

## <a name="next-steps"></a>Következő lépések

- A munkafüzet erőforrásaihoz való hozzáférés [szabályozása](../visualize/workbooks-access-control.md) és megosztása.
- Megtudhatja, hogyan használhatja [a csoportokat a munkafüzetekben](../visualize/workbooks-groups.md).