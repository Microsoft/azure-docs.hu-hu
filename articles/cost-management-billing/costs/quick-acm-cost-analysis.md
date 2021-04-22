---
title: Rövid útmutató – Az Azure-költségek feltérképezése költségelemzés használatával
description: Ez a rövid útmutató bemutatja, hogyan vizsgálhatja meg és elemezheti vállalata Azure-költségeit a Költségelemzés szolgáltatás használatával.
author: bandersmsft
ms.author: banders
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: micflan
ms.custom: contperf-fy21q2, devx-track-azurecli
ms.openlocfilehash: 4d0062258919de29750b644b2f8e12990e25098b
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107873207"
---
# <a name="quickstart-explore-and-analyze-costs-with-cost-analysis"></a>Gyorsútmutató: Költségek feltérképezése és elemzése költségelemzés használatával

Ahhoz, hogy érdemben szabályozhassa és optimalizálhassa Azure-költségeit, fontos, hogy tudja a vállalaton belül hol keletkeznek ezek a költségek. Emellett nem árt, ha tudja, mennyibe kerülnek az egyes szolgáltatások, és milyen környezeteket vagy rendszereket szolgálnak ki. A költségek teljes spektrumának ismerete elengedhetetlen a szervezet kiadásaiban fellépő mintázatok pontos megértéséhez. A kiadásoknál fellépő mintázatok segítségével költségszabályozó mechanizmusok, például költségkeretek léptethetők érvénybe.

Ebben a rövid útmutatóban költségelemzés használatával megvizsgálja és elemzi a szervezet költségeit. Az összesített költségeket szervezetekre lebontva jelenítheti meg, így jobban átláthatja, hogy az idő múlásával mely területeken keletkeznek költségei és felismerheti a kiadások trendjeit. Megtekintheti az egy adott időszak során felhalmozott költségeket is, így havi, negyedéves vagy akár éves költségtrendbecsléseket is készíthet a költségvetéshez viszonyítva. Költségvetés használatával könnyebb betartani a pénzügyi korlátozásokat. Ezen felül a költségvetés segít áttekinteni napi vagy havi szintű költségeit és elkülöníteni a rendhagyó kiadásokat. Az adatokat le is töltheti további elemzés vagy külső rendszerben való használat céljából.

Ezen rövid útmutató segítségével megtanulhatja a következőket:

- Költségek áttekintése a Költségelemzés használatával
- Költségnézetek testreszabása
- Költségelemzési adatok letöltése

## <a name="prerequisites"></a>Előfeltételek

A költségelemzés különböző Azure-fióktípusokat támogat. A támogatott fióktípusok teljes listáját lásd: [A Cost Management adatainak értelmezése](understand-cost-mgt-data.md). A költségadatok megtekintéséhez legalább olvasási jogosultsággal kell rendelkeznie az Azure-fiókjához.

További információért az Azure Cost Management adataihoz való hozzáférés hozzárendeléséről: [Adatokhoz való hozzáférés hozzárendelése](./assign-access-acm-data.md).

Új előfizetés esetén nem használhatja azonnal a Cost Management szolgáltatásait. Akár 48 órára is szükség lehet, hogy a Cost Management összes szolgáltatását használhassa.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

- Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="review-costs-in-cost-analysis"></a>Költségek áttekintése a Költségelemzés használatával

A költségek a költségelemzésben való áttekintéséhez nyissa meg a hatókört az Azure Portalon, majd válassza ki a **Költségelemzés** lehetőséget a menüben. Például lépjen az **Előfizetések** pontra, válasszon ki egy előfizetést a listában, majd válassza a **Költségelemzés** menüpontot. A **Hatókör** elemmel másik hatókörre válthat a költségelemzésben.

A választott hatókör az adatkonszolidációt és a költségadatokhoz való hozzáférés szabályozását határozza meg a költségkezelés során. A hatókörök használata során nem lehet egyszerre több hatókört is kiválasztani. Ehelyett egy magasabb szintű hatókört választ ki, amely alá több alacsonyabb szintű hatókör is tartozik, és szűrő alkalmazásával keresi meg a kívánt beágyazott hatóköröket. Ezt a megközelítést azért fontos megérteni, mert nem biztos, hogy minden személy rendelkezik hozzáféréssel egyetlen szülőhatókörhöz, amely több beágyazott hatókört is magába foglal.

Ha többet szeretne megtudni a költségelemzésről, tekintse meg [a Cost Management Azure Portalon történő használatával](https://www.youtube.com/watch?v=mfxysF-kTFA) foglalkozó videót. További videók megtekintéséhez látogasson el a [Cost Management YouTube-csatornájára](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/mfxysF-kTFA]

A kezdő költségelemzési nézet a következő területeket foglalja magába.

**Halmozott költség nézet**: A költségelemzés előre definiált nézete. Minden nézet tartalmaz dátumtartományra, részletességre, csoportosítási szempontra és szűrésre vonatkozó beállításokat. Az alapértelmezett nézet az aktuális számlázási időszakra vonatkozó halmozott költségeket mutatja, de átválthat a többi beépített nézetre.

**Tényleges költség**: Az aktuális hónap összes keletkező és a számlán megjelenő használati és vásárlási költségét mutatja.

**Előrejelzés**: A választott időszak összes előrevetített költségét jeleníti meg.

**Költségkeret**: A kijelölt hatókör tervezett költségkeretét jeleníti meg, amennyiben az elérhető.

**Halmozott részletesség**: Megjeleníti az összes összesített napi kiadást a számlázási időszak kezdetétől számítva. Miután számlázási fiókjához vagy előfizetéséhez létrehozta költségvetését, itt gyorsan, költségkeretéhez viszonyítottan áttekintheti kiadási trendjeit. A kurzort egy adott dátum felé helyezve megtekintheti az adott nap halmozott költségeit.

**Kimutatásdiagramok (fánkdiagramok)** : Dinamikus kimutatásokat tesznek lehetővé azzal, hogy a teljes költséget alapvető jellemzők alapján bontják le. Az aktuális hónap költségeit jelenítik meg a legnagyobbaktól a legkisebbekig. A kimutatásdiagramokat bármikor módosíthatja másik kimutatás kiválasztásával. A költségek alapértelmezetten a következő kategóriákba vannak sorolva: szolgáltatás (fogyasztásmérő kategóriája), hely (régió), valamint gyermekhatókör. Például a regisztrációs fiókok a számlázási fiókok alatt, az erőforráscsoportok az előfizetések alatt, az erőforrások pedig az erőforráscsoportok alatt jelennek meg.

![A költségelemzés kezdőnézete az Azure Portalon](./media/quick-acm-cost-analysis/cost-analysis-01.png)

### <a name="understand-forecast"></a>Az előrejelzés ismertetése

A legutóbbi használat alapján a költség-előrejelzések a kiválasztott időszak becsült költségeinek előrejelzését mutatják. Ha egy költségvetés be van állítva a Költségelemzésben, megtekintheti, hogy az előrejelzett költségek várhatóan meghaladják-e a költségvetési küszöbértéket. Az előrejelzési modell akár egy évre előrejelezni tudja a jövőbeli költségeket. Válassza ki a szűrőket a kiválasztott dimenzió részletes előrejeles költségének megtekintéséhez.

Az előrejelzési modell egy idősoros regressziós modellen alapul. Legalább 10 napnyi előzetes költség- és felhasználási adat szükséges a költségek pontos előrejelzéséhez. Egy adott időszakra vonatkozóan az előrejelzési modellnek az időszak hosszával megegyező mennyiségű betanítási adatra van szüksége. Például a három hónapos előrejelzéshez legalább három hónapnyi friss költség- és felhasználási adat szükséges.

## <a name="customize-cost-views"></a>Költségnézetek testreszabása

A költségelemzés négy beépített nézettel rendelkezik, amelyek a leggyakoribb célokra vannak optimalizálva:

Nézet | A következőkhöz hasonló kérdésekre ad választ:
--- | ---
Halmozott költség | Mennyit költöttem eddig ebben a hónapban? Belül maradok-e a költségkeretemen?
Napi költség | Növekedett a napi költségszint az elmúlt 30 napban?
Díj szolgáltatás szerint | Hogyan változott a havi használat a legutóbbi három számla szerint?
Költségek erőforrások szerint | Eddig a folyó hónapban melyik erőforrások kerültek a legtöbbe?
Számla részletei | Milyen díjak szerepeltek az utolsó számlán?

![Nézetválasztó az erre a hónapra vonatkozó kiválasztást bemutató példával](./media/quick-acm-cost-analysis/view-selector.png)

Azonban sok esetben ennél mélyebb elemzésre is szükség lehet. A testreszabást az oldal tetején a dátum kiválasztásánál kezdhetjük.

Alapértelmezetten a költségelemzés az aktuális hónap adatait jeleníti meg. A dátumválasztóval gyorsan válthat néhány általános dátumtartomány között. Ilyen például az elmúlt hét nap, a múlt hónap, az aktuális év vagy egy egyéni dátumtartomány. A használatalapú előfizetéseknél a számlázási időszakon alapuló, a naptári hónaptól független dátumtartományok is elérhetők, például az aktuális számlázási időszak vagy az utolsó számla. A menü tetején az **<ELŐZŐ** és a **KÖVETKEZŐ>** hivatkozásokkal léphet az előző vagy a következő időszakra. Például az **<ELŐZŐ** hivatkozásra kattintva az **előző 7 nap** helyett a **8–14 nappal ezelőtti**, majd a **15–21 nappal ezelőtti** tartomány jelenik meg. Ne feledje, hogy egyéni dátumtartomány kiválasztásakor akár egy egész évet is kiválaszthat (például január 1-től december 31-ig).

![Dátumválasztó az erre a hónapra vonatkozó kiválasztást bemutató példával](./media/quick-acm-cost-analysis/date-selector.png)

A költségelemzés alapértelmezetten a **Halmozott** költségeket jeleníti meg. A halmozott költségek az egyes napok költségét, valamint a megelőző napok költségeit foglalják magukba, tehát a napi költségek folyamatos halmozódásának nyomon követésére szolgálnak. A nézetet arra optimalizáltuk, hogy megvizsgálhassa, hogyan alakulnak költségei az adott időszakban a tervezett költségvetéshez képest.

Az előrejelzési diagram nézetén megállapíthatja, hogy fennáll-e a költségkeret-túllépés esélye. A potenciális költségkeret-túllépéseknél az előrevetített túlköltekezés pirossal jelenik meg. A diagramon ilyenkor egy figyelmeztető szimbólum is látható. Ha a mutatót a szimbólum fölé veszi, megjelenik a költségkeret-túllépés becsült dátuma.

![Lehetséges költségkeret-túllépést bemutató példa](./media/quick-acm-cost-analysis/budget-breach.png)

Emellett a **napi** nézet használatával az egyes napok költségeit is megtekintheti. A napi nézet nem jeleníti meg a növekedési trendeket. A nézet célja a szabálytalanságok, vagyis a kiugróan magas vagy alacsony értékek megjelenítése a napról napra jelentkező költségekben. Ha költségvetést is kiválasztott, a napi nézet a napi költségkeret becslését is megjeleníti.

Ha a napi költségei következetesen meghaladják a becsült napi költségkeretet, várhatóan a havi költségkeretét is túl fogja lépni. A becsült napi költségkeret egy olyan eszköz, amellyel könnyebben áttekintheti költségvetése kisebb alkotóelemeit. Ha napi költségei ingadozóak, a becsült napi költségkeret viszonya a havi költségkeretével kevésbé lesz pontos.

A legutóbbi költések napi nézete a költségek előrejelzésével így néz ki.
![Példa az aktuális hónap napi költségeit ábrázoló napi nézetre](./media/quick-acm-cost-analysis/daily-view.png)

Ha kikapcsolja a kiadások előrejelzését, a jövőbeli dátumok esetében nem látja a tervezett kiadásokat. Továbbá, ha a múltbeli időszakokra vonatkozó költségeket tekinti meg, a költség-előrejelzés nem jelenít meg költségeket.

Általánosságban elmondható, hogy a felhasznált erőforrásokra vonatkozó adatok vagy értesítések 8–12 órán belül jelennek meg.

**Csoportosítást** végezhet általános tulajdonságok alapján a költségek lebontásához és a legfontosabb közreműködők azonosításához. Az erőforráscímkék szerinti csoportosításhoz például válassza ki azt a címkekulcsot, amely alapján csoportosítani szeretne. A költségek az egyes címkeértékek szerint vannak lebontva egy külön szegmenssel az olyan erőforrások számára, amelyeken nincs alkalmazva az adott címke.

A legtöbb Azure-erőforrás támogatja a címkézést. Ugyanakkor néhány címke nem érhető el a Költségkezelés és számlázás területen. Továbbá az erőforráscsoport-címkék sem támogatottak. A címkék támogatása a címke erőforráson való alkalmazása *utáni* jelentett használatra vonatkozik. A címkék nem jelennek meg visszamenőlegesen a költségösszesítések esetében.

További információért a költségadatok láthatóságának Azure-címkeszabályzat használatával történő javításáról tekintse meg a [Címkeszabályzatok áttekintése az Azure Cost Managementtel](https://www.youtube.com/watch?v=nHQYcYGKuyw) című videót.

Íme egy nézet arról, hogyan alakultak az Azure-szolgáltatások költségei az aktuális hónapban.

![Csoportosított napi halmozott nézet az Azure-szolgáltatások múlt hónapi költségeivel](./media/quick-acm-cost-analysis/grouped-daily-accum-view.png)

Alapértelmezés szerint a költségelemzés az összes keletkező és számlán megjelenő használati és vásárlási költséget, más néven a **Tényleges költséget** mutatja. A tényleges költség ideális arra, hogy egyeztetni tudja a számlát. A költségekben jelentkező kiugró vásárlások mindazonáltal aggodalmakra adhatnak okot, ha nem szeretne rendellenes kiadásokat vagy a költségekben jelentkező egyéb változásokat látni. A foglalásvásárlási költségek által okozott kiugró csúcsok kisimításához váltson **amortizált költségekre**.

![Váltson a tényleges és az amortizált költség között, hogy megtekinthesse a teljes időszakra leosztott, illetve a foglalást használó erőforrásokhoz rendelt foglalásvásárlásokat](./media/quick-acm-cost-analysis/metric-picker.png)

Az amortizált költség nézete a foglalásvásárlási díjakat napi adagokra osztja, és ezeket egyenletesen elosztja a foglalási időszak teljes tartamára. Például ahelyett, hogy január 1-én kiugrana egy 365 dollár értékű vásárlás, január 1-től december 31-ig minden nap egy 1,00 dollár értékű vásárlás jelenik meg. Az alapvető amortizáción felül ezek a költségek a foglalást használó konkrét erőforrásokhoz vannak rendelve, és közöttük vannak szétosztva. Ha például az 1,00 dolláros napi díj két virtuális gép között oszlik meg, két 0,50 dolláros napi díj jelenik meg. Ha a foglalás egyik része az adott napon nincs felhasználva, a megfelelő virtuális gépnél egy 0,50 dolláros díj látható, egy másik 0,50 dolláros díj pedig `UnusedReservation` díjtípussal jelenik meg. A fel nem használt foglalási költségek csak akkor láthatók, ha az amortizált költségeket jeleníti meg.

Fontos megjegyezni, hogy a költségek megjelenítésének eltérései miatt a tényleges és az amortizált költség nézete eltérő végösszeget mutat. Általánosságban elmondható, hogy az amortizált költségek megtekintése esetén a foglalásvásárlást tartalmazó hónapok teljes költsége csökken, a foglalásvásárlásokat követő hónapok költségei pedig növekednek. Az amortizáció csak foglalásvásárlások esetén érhető el, és jelenleg az Azure Marketplace-beli vásárlásokra nem vonatkozik.

A következő képen az erőforráscsoportok nevei olvashatók. Csoportosíthat címkék alapján a teljes költség címkék szerinti megtekintéséhez, vagy használhatja a **Költség erőforrás szerint** nézetet egy adott erőforráshoz tartozó összes címke megtekintéséhez.

![A jelenlegi nézet összes adata az erőforráscsoport-nevek megjelenítésével](./media/quick-acm-cost-analysis/full-data-set.png)

Ha a költségeket egy adott attribútum szerint csoportosítja, csökkenő sorrendben megjelenik a tíz legfontosabb költségelem. Ha tíznél több ilyen elem van, akkor a kilenc legfontosabb költségelem mellett megjelenik egy **Egyéb** csoport is, amelyben az összes többi csoport együttesen szerepel. Címkék szerinti csoportosítás esetén egy **Címkézetlen** csoport jelenik meg azon költségekkel, amelyek nem rendelkeznek a címkekulccsal. Mindig a **Címkézetlen** csoport az utolsó, még akkor is, ha a címkézetlen költségek magasabbak a címkézett költségeknél. 10 vagy több címkeérték esetén a címkézetlen költségek az **Egyéb** csoport részei lesznek. Váltson táblanézetre, és módosítsa a részletességet **Nincs** értékre az összes érték ár szerinti csökkenő sorrendben való megjelenítéséhez.

A klasszikus virtuális gépek, hálózatkezelési és tárolási erőforrások nem osztanak meg részletes számlázási adatokat. Ezek a költségek csoportosításakor a **Klasszikus szolgáltatások** csoportba kerülnek.

A fő diagram alatti kimutatásdiagramok különböző csoportosításokat jelenítenek meg, így átfogó képet nyújtanak a teljes költségről a kiválasztott időszaknak és szűrőknek megfelelően. Válasszon ki egy tulajdonságot vagy címkét az összesített költségek bármely dimenzió alapján való megtekintéséhez.

![Példa kimutatásdiagramokra](./media/quick-acm-cost-analysis/pivot-charts.png)

Bármelyik nézet esetében megtekintheti a teljes adathalmazt. Az alkalmazott beállítások vagy szűrők hatással vannak a bemutatott adatokra. A teljes adathalmaz megtekintéséhez kattintson a **diagramtípus** listára, majd válassza ki a **Táblázat** nézetet.

![A jelenlegi nézet adatai táblázatos nézetben](./media/quick-acm-cost-analysis/chart-type-table-view.png)

## <a name="saving-and-sharing-customized-views"></a>Személyre szabott nézetek mentése és megosztása

A személyre szabott nézeteket a költségelemzés az Azure Portal irányítópultján való rögzítésével vagy a költségelemzésre mutató hivatkozás másolásával mentheti és oszthatja meg másokkal.

Ha többet szeretne megtudni arról, miként oszthatja meg a cégen vagy szervezeten belül a költségekkel kapcsolatos információkat a portál segítségével, tekintse meg a [nézetek Azure Cost Managementben történő megosztásával és mentésével](https://www.youtube.com/watch?v=kQkXXj-SmvQ) foglalkozó videót. További videók megtekintéséhez látogasson el a [Cost Management YouTube-csatornájára](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/kQkXXj-SmvQ]

A költségelemzés rögzítéséhez kattintson a rögzítési ikonra a jobb felső sarokban, vagy közvetlenül a(z) „<Subscription Name> | Költségelemzés” elem után. A költségelemzés rögzítése csak a fő diagramot vagy a táblanézetet fogja menteni. Az irányítópult megosztásával biztosítása másoknak is hozzáférést a csempéhez. A megosztás csak az irányítópult konfigurációját osztja meg, és nem biztosít hozzáférést a mögöttes adatokhoz. Ha nincs hozzáférése a költségekhez, de hozzáfér egy megosztott irányítópulthoz, a „hozzáférés megtagadva” üzenetet fog látni.

A költségelemzésre mutató hivatkozás megosztásához válassza az ablak felső részén lévő **Megosztás** lehetőséget. Egy egyéni URL-cím fog megjelenni, amely megnyitja az adott hatókörre vonatkozó nézetet. Ha nincs hozzáférése a költséghez, az URL-cím használatakor a "hozzáférés megtagadva" üzenet jelenik meg.

## <a name="download-usage-data"></a>Használati adatok letöltése

### <a name="portal"></a>[Portál](#tab/azure-portal)

Előfordulhat, hogy további elemzés céljából le kell töltenie az adatait, egyesíteni kell őket a saját adataival, vagy integrálnia kell őket a saját rendszereibe. A Cost Management több különböző lehetőséget kínál. Ha egy olyan gyors, részletes összegzésre van szüksége, mint amilyen a költségelemzésben is elérhető, hozza létre a szükséges nézetet. Ezt követően töltse le az **Exportálás**, majd az **Adatok letöltése CSV-fájlba** vagy az  **Adatok letöltése Excel-fájlba** lehetőség kiválasztásával. Az Excel-fájlba történő letöltés további információt nyújt a letöltés létrehozására használt nézetről, például a hatókörről, a lekérdezéskonfigurációról, a teljes összegről és a létrehozás dátumáról.

Ha a teljes, nem összesített adatkészletre van szüksége, töltse le a számlázási fiókból. Ezután a portál bal oldali navigációs paneljén található szolgáltatáslistából válassza ki a **Költségkezelés + Számlázás** lehetőséget. Válassza ki a számlázási fiókját, ha van. Lépjen a **Felhasználás + díjak** oldalra, majd kattintson egy számlázási időszak **Letöltés** ikonjára.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Először készítse elő a környezetet az Azure CLI-hez:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

A bejelentkezés után az [az costmanagement query](/cli/azure/costmanagement#az_costmanagement_query) paranccsal kérdezheti le az előfizetése az aktuális hónapra vonatkozó használati adatait:

```azurecli
az costmanagement query --timeframe MonthToDate --type Usage \
   --scope "subscriptions/00000000-0000-0000-0000-000000000000"
```

A **--dataset-filter** paraméterrel és egyéb paraméterekkel szűkítheti a lekérdezést:

```azurecli
az costmanagement query --timeframe MonthToDate --type Usage \
   --scope "subscriptions/00000000-0000-0000-0000-000000000000" \
   --dataset-filter "{\"and\":[{\"or\":[{\"dimension\":{\"name\":\"ResourceLocation\",\"operator\":\"In\",\"values\":[\"East US\",\"West Europe\"]}},{\"tag\":{\"name\":\"Environment\",\"operator\":\"In\",\"values\":[\"UAT\",\"Prod\"]}}]},{\"dimension\":{\"name\":\"ResourceGroup\",\"operator\":\"In\",\"values\":[\"API\"]}}]}"
```

A **--dataset-filter** paraméter JSON-sztringet vagy `@json-file` elemet vesz fel.

Az [az costmanagement export](/cli/azure/costmanagement/export) parancsokat is használhatja, hogy használati adatokat exportáljon egy Azure Storage-fiókba. Onnan töltheti le az adatokat.

1. Hozzon létre egy erőforráscsoportot, vagy használjon egy meglévőt. Erőforráscsoport létrehozásához futtassa az [az group create](/cli/azure/group#az_group_create) parancsot:

   ```azurecli
   az group create --name TreyNetwork --location "East US"
   ```

1. Hozzon létre egy tárfiókot az exportálások fogadásához, vagy használjon egy meglévő tárfiókot. Fiók létrehozásához használja az [az storage account create](/cli/azure/storage/account#az_storage_account_create) parancsot:

   ```azurecli
   az storage account create --resource-group TreyNetwork --name cmdemo
   ```

1. Futtassa az [az costmanagement export create](/cli/azure/costmanagement/export#az_costmanagement_export_create) parancsot az exportálás létrehozásához:

   ```azurecli
   az costmanagement export create --name DemoExport --type Usage \
   --scope "subscriptions/00000000-0000-0000-0000-000000000000" --storage-account-id cmdemo \
   --storage-container democontainer --timeframe MonthToDate --storage-directory demodirectory
   ```

---

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

- Ha korábban rögzített egy személyre szabott költségelemzési nézetet, de már nincs rá szüksége, az adott irányítópultra lépve törölheti a rögzített nézetet.
- Ha használati adatokat tartalmazó fájlokat töltött le, de nincs már szüksége rájuk, mindenképpen törölje őket.

## <a name="next-steps"></a>További lépések

Lépjen tovább az első oktatóanyaghoz, amely a költségvetés létrehozását és kezelését mutatja be.

> [!div class="nextstepaction"]
> [Költségvetések létrehozása és kezelése](tutorial-acm-create-budgets.md)
