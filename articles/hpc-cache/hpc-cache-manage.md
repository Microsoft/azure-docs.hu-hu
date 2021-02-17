---
title: Az Azure HPC cache kezelése és frissítése
description: Az Azure HPC cache kezelése és frissítése az Azure Portal vagy az Azure CLI használatával
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 08/31/2020
ms.author: v-erkel
ms.openlocfilehash: 40d091782f37a32b75659cd274e49798dbe527cb
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100590792"
---
# <a name="manage-your-cache"></a>A gyorsítótár kezelése

A gyorsítótár Áttekintés lapja a Azure Portal megjeleníti a projekt részleteit, a gyorsítótár állapotát és a gyorsítótár alapszintű statisztikáit. Emellett a gyorsítótár leállításához vagy elindításához, a gyorsítótár törléséhez, az adatürítéshez és a szoftver frissítéséhez szükséges vezérlőkkel is rendelkezik.

Ez a cikk azt is ismerteti, hogyan végezheti el ezeket az alapvető feladatokat az Azure CLI-vel.

Az Áttekintés lap megnyitásához válassza ki a gyorsítótár-erőforrást a Azure Portal. Töltse be például a **minden erőforrás** lapot, és kattintson a gyorsítótár nevére.

![Az Azure HPC cache-példány áttekintési oldalának képernyőképe](media/hpc-cache-overview.png)

A lap tetején található gombok segítenek a gyorsítótár kezelésében:

* **Indítás** és [**Leállítás**](#stop-the-cache) – gyorsítótár-művelet folytatása vagy felfüggesztése
* [**Ürítés**](#flush-cached-data) – módosított adatot ír a tárolási célokba
* [**Frissítés**](#upgrade-cache-software) – frissíti a gyorsítótár szoftverét
* [**Diagnosztika gyűjtése**](#collect-diagnostics) – hibakeresési adatok feltöltése
* **Frissítés** – újratölti az Áttekintés oldalt
* [**Delete (Törlés**](#delete-the-cache) ) – véglegesen elpusztítja a gyorsítótárat

Az alábbi lehetőségekről itt olvashat bővebben.

Az alábbi képre kattintva megtekintheti a gyorsítótár-kezelési feladatokat bemutató [videót](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) .

[![videó miniatűrje: Azure HPC cache: Manage (kattintson ide a videó oldalának megtekintéséhez)](media/video-5-manage.png)](https://azure.microsoft.com/resources/videos/managing-hpc-cache/)

## <a name="stop-the-cache"></a>A gyorsítótár leállítása

A gyorsítótár leállításával csökkentheti a költségeket az inaktív időszakokban. Nem számítunk fel időt a gyorsítótár leállítási idejére, de a gyorsítótár lefoglalt lemezes tárterületét kell fizetnie. (A részletekért tekintse meg a [díjszabási](https://aka.ms/hpc-cache-pricing) oldalt.)

A leállított gyorsítótár nem válaszol az ügyfelek kéréseire. A gyorsítótár leállítása előtt le kell választania az ügyfeleket.

### <a name="portal"></a>[Portál](#tab/azure-portal)

A **Leállítás** gomb felfüggeszti az aktív gyorsítótárat. A **Leállítás** gomb akkor érhető el, ha a gyorsítótár állapota **kifogástalan** vagy **csökkentett teljesítményű**.

![képernyőkép a leállítást lefedő gombokról és egy előugró üzenetről, amely leírja a leállítási műveletet, és azt kérdezi, hogy folytatja? Igen (alapértelmezett) és nincs gomb](media/stop-cache.png)

Miután rákattintott az Igen gombra a gyorsítótár leállításának megerősítéséhez, a gyorsítótár automatikusan kiüríti a tartalmát a tárolási célokhoz. Ez a folyamat hosszabb időt is igénybe vehet, de gondoskodik az adatkonzisztenciaről. Végül a gyorsítótár állapota **Leállítva** értékre változik.

A leállított gyorsítótár újraaktiválásához kattintson a **Start** gombra. Nincs szükség jóváhagyásra.

![képernyőkép a legfontosabb gombokról a Start Kiemelt](media/start-cache.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Állítsa be az Azure CLI-t az Azure HPC cache-hez](./az-cli-prerequisites.md).

A gyorsítótár ideiglenes felfüggesztése az az [HPC-cache stop](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-stop) paranccsal. Ez a művelet csak akkor érvényes, ha a gyorsítótár állapota **kifogástalan** vagy **csökkentett teljesítményű**.

A gyorsítótár a leállítás előtt automatikusan üríti a tartalmat a tárolási célokba. Ez a folyamat hosszabb időt is igénybe vehet, de gondoskodik az adatkonzisztenciaről.

Ha a művelet befejeződött, a gyorsítótár állapota **Leállítva** értékre változik.

Aktiválja újra a leállított gyorsítótárat az [az HPC-cache Start](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-start)paranccsal.

A Start vagy a stop parancs kiadása után a parancssorban egy "futó" állapotjelző üzenet jelenik meg, amíg a művelet be nem fejeződik.

```azurecli
$ az hpc-cache start --name doc-cache0629
 - Running ..
```

Befejezésekor az üzenet a "kész" értékre frissül, és megjeleníti a visszatérési kódokat és egyéb információkat.

```azurecli
$ az hpc-cache start --name doc-cache0629
{- Finished ..
  "endTime": "2020-07-01T18:46:43.6862478+00:00",
  "name": "c48d320f-f5f5-40ab-8b25-0ac065984f62",
  "properties": {
    "output": "success"
  },
  "startTime": "2020-07-01T18:40:28.5468983+00:00",
  "status": "Succeeded"
}
```

---

## <a name="flush-cached-data"></a>Gyorsítótárazott adattárolás ürítése

Az áttekintő lap **kiürítés** gombja azt jelzi, hogy a gyorsítótár azonnal írni tudja a gyorsítótárban tárolt összes módosult, a háttérbeli tárolási célokat. A gyorsítótár rendszeres módon menti az adatok tárolási célhelyeit, ezért ezt manuálisan nem kell elvégezni, ha nem szeretné, hogy a háttérrendszer naprakész legyen. Előfordulhat például, hogy a **kiürítést** a tárolási pillanatkép elkészítése vagy az adathalmaz méretének ellenőrzése előtt használja.

> [!NOTE]
> A kiürítési folyamat során a gyorsítótár nem tudja kiszolgálni az ügyfelek kérelmeit. A gyorsítótár-hozzáférés fel van függesztve, és a művelet befejeződése után folytatódik.

Ha elindítja a gyorsítótár kiürítési műveletét, a gyorsítótár leáll az ügyfelek kéréseinek fogadása után, és a gyorsítótár állapota az Áttekintés oldalon a **Kiürítésre** változik.

A gyorsítótárban tárolt adattárolók a megfelelő tárolási célokba lesznek mentve. Attól függően, hogy mennyi adatra van szükség a kiürítéshez, a folyamat eltarthat néhány percig, vagy akár egy óráig is.

Miután az összes adatmentést a tárolási célokba menti, a gyorsítótár automatikusan elindul az ügyfelek kéréseinek megkezdése után. A gyorsítótár állapota **kifogástalanra** vált.

### <a name="portal"></a>[Portál](#tab/azure-portal)

A gyorsítótár kiürítéséhez kattintson a **kiürítés** gombra, majd a művelet megerősítéséhez kattintson az **Igen** gombra.

![képernyőkép a felső gombokról és a flush kiemeléséről, valamint egy előugró üzenet, amely leírja a kiürítési műveletet, és megkérdezi, hogy szeretné-e folytatni? Igen (alapértelmezett) és nincs gomb](media/hpc-cache-flush.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Állítsa be az Azure CLI-t az Azure HPC cache-hez](./az-cli-prerequisites.md).

Az [az HPC-cache flush](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-flush) paranccsal kényszerítheti a gyorsítótárat, hogy az összes módosult adattal írja a tárolási célokat.

Példa:

```azurecli
$ az hpc-cache flush --name doc-cache0629 --resource-group doc-rg
 - Running ..
```

A kiürítés befejeződése után a rendszer sikeres üzenetet küld.

```azurecli
{- Finished ..
  "endTime": "2020-07-09T17:26:13.9371983+00:00",
  "name": "c22f8e12-fcf0-49e5-b897-6a6e579b6489",
  "properties": {
    "output": "success"
  },
  "startTime": "2020-07-09T17:25:21.4278297+00:00",
  "status": "Succeeded"
}
$
```

---

## <a name="upgrade-cache-software"></a>A cache szoftver frissítése

Ha elérhetővé vált egy új szoftververzió, a **frissítés** gomb aktívvá válik. A szoftver frissítésével kapcsolatos üzenet a lap tetején is megjelenik.

![képernyőfelvétel a gombok felső soráról a frissítés gomb engedélyezésével](media/hpc-cache-upgrade-button.png)

A szoftverfrissítés során az ügyfél-hozzáférés nem szakad meg, a gyorsítótár teljesítménye azonban lelassul. Tervezze meg a szoftver frissítését a használaton kívüli órákban vagy tervezett karbantartási időszakban.

A szoftverfrissítés több órát is igénybe vehet. A magasabb átviteli sebességgel konfigurált gyorsítótárak hosszabb ideig tartanak, mint a kisebb csúcsérték-értékekkel rendelkező gyorsítótárak.

Ha egy szoftverfrissítés elérhető, akkor a rendszer hetente vagy manuálisan alkalmazza azt. A befejezési dátum a frissítési üzenetben jelenik meg. Ha ez idő alatt nem végez frissítést, az Azure automatikusan alkalmazza a frissítést a gyorsítótárba. Az automatikus frissítés ütemezése nem konfigurálható. Ha aggódik a gyorsítótár teljesítményére gyakorolt hatás miatt, a szoftvert saját kezűleg kell frissítenie az időtartam lejárta előtt.

Ha a rendszer leállítja a gyorsítótárat a befejezési dátum után, a gyorsítótár a következő indításakor automatikusan frissíti a szoftvert. (Előfordulhat, hogy a frissítés nem indul el azonnal, de az első órában indul el.)

### <a name="portal"></a>[Portál](#tab/azure-portal)

A szoftverfrissítés megkezdéséhez kattintson a **frissítés** gombra. A gyorsítótár állapota a **frissítésig** változik, amíg a művelet be nem fejeződik.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Állítsa be az Azure CLI-t az Azure HPC cache-hez](./az-cli-prerequisites.md).

Az Azure CLI-ben a gyorsítótár állapotáról szóló jelentés végén új szoftver-információk szerepelnek. (Az [az HPC-cache show](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-show) paranccsal ellenőrizhető.) Keresse meg a "upgradeStatus" karakterláncot az üzenetben.

Az az [HPC-cache upgrade-firmware](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-upgrade-firmware) paranccsal alkalmazza a frissítést, ha van ilyen.

Ha nincs elérhető frissítés, a műveletnek nincs hatása.

Ez a példa a gyorsítótár állapotát jeleníti meg (nincs elérhető frissítés) és a frissítés – belső vezérlőprogram parancs eredményét.

```azurecli
$ az hpc-cache show --name doc-cache0629
{
  "cacheSizeGb": 3072,
  "health": {
    "state": "Healthy",
    "statusDescription": "The cache is in Running state"
  },

<...>

  "tags": null,
  "type": "Microsoft.StorageCache/caches",
  "upgradeStatus": {
    "currentFirmwareVersion": "5.3.61",
    "firmwareUpdateDeadline": "0001-01-01T00:00:00+00:00",
    "firmwareUpdateStatus": "unavailable",
    "lastFirmwareUpdate": "2020-06-29T22:18:32.004822+00:00",
    "pendingFirmwareVersion": null
  }
}
$ az hpc-cache upgrade-firmware --name doc-cache0629
$
```

---

## <a name="collect-diagnostics"></a>Diagnosztikai információk gyűjtése

A **diagnosztika összegyűjtése** gomb manuálisan elindítja a rendszerinformációk gyűjtésének és a Microsoft-szolgáltatásba való feltöltésének folyamatát, és támogatja a hibaelhárítást. A gyorsítótár automatikusan gyűjti és feltölti ugyanazokat a diagnosztikai adatokat, ha súlyos gyorsítótárazási probléma lép fel.

Akkor használja ezt a vezérlőt, ha a Microsoft szolgáltatás és a támogatás kéri.

Miután rákattintott a gombra, kattintson az **Igen** gombra a feltöltés megerősítéséhez.

![képernyőkép: "a diagnosztikai gyűjtemény elindítása" előugró ablak megerősítő üzenete. Az alapértelmezett "yes" gomb ki van emelve.](media/diagnostics-confirm.png)

## <a name="delete-the-cache"></a>A gyorsítótár törlése

A **Törlés** gomb megsemmisíti a gyorsítótárat. Ha töröl egy gyorsítótárat, a rendszer minden erőforrását megsemmisíti, és többé nem számít fel fiókra vonatkozó díjat.

A tárolási célokként használt háttérbeli tárolási kötetek nem érintik a gyorsítótár törlésekor. Később hozzáadhatja őket egy későbbi gyorsítótárhoz, vagy elvégezheti őket külön leszereléssel.

> [!NOTE]
> Az Azure HPC-gyorsítótár nem ír automatikusan módosított adatokból a gyorsítótárból a háttér-tárolási rendszerbe a gyorsítótár törlése előtt.
>
> Annak érdekében, hogy a gyorsítótárban lévő összes adattal a hosszú távú tárolásra legyen írva, [a törlés előtt állítsa le a gyorsítótárat](#stop-the-cache) . Győződjön meg arról, hogy az állapot **leállt** a törlés előtt.

### <a name="portal"></a>[Portál](#tab/azure-portal)

A gyorsítótár leállítása után kattintson a **Törlés** gombra a gyorsítótár végleges eltávolításához.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Állítsa be az Azure CLI-t az Azure HPC cache-hez](./az-cli-prerequisites.md).

A gyorsítótár végleges eltávolításához használja az Azure CLI-parancsot az [HPC-cache delete](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-delete) paranccsal.

Példa:
```azurecli
$ az hpc-cache delete --name doc-cache0629
 - Running ..

<...>

{- Finished ..
  "endTime": "2020-07-09T22:24:35.1605019+00:00",
  "name": "7d3cd0ba-11b3-4180-8298-d9cafc9f22c1",
  "startTime": "2020-07-09T22:13:32.0732892+00:00",
  "status": "Succeeded"
}
$
```

---

## <a name="cache-metrics-and-monitoring"></a>Gyorsítótár-metrikák és-figyelés

Az Áttekintés oldalon néhány alapszintű gyorsítótár-statisztika – a gyorsítótárazási sebesség, a másodpercenkénti műveletek és a késés – grafikonok láthatók.

![képernyőfelvétel a fent említett statisztikai adatokat bemutató három vonalas gráfról](media/hpc-cache-overview-stats.png)

Ezek a diagramok az Azure beépített monitorozási és elemzési eszközeinek részét képezik. A portál oldalsávjának **figyelés** fejléce alatt további eszközök és riasztások érhetők el. További információt az [Azure monitoring dokumentációjának](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-in-the-azure-portal)portál szakasza tartalmaz.

## <a name="next-steps"></a>Következő lépések

* További információ az [Azure mérőszámok és statisztikai eszközökről](../azure-monitor/index.yml)
* Segítség kérése [Az Azure HPC cache](hpc-cache-support-ticket.md) -hez
