---
title: Alkalmazások kezelése és Azure HPC Cache
description: Adatok kezelése és frissítése Azure HPC Cache az Azure Portal Azure CLI használatával
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/08/2021
ms.author: v-erkel
ms.openlocfilehash: a831aa7b2f3b0d438d9db8fefa3d26428fea3680
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107862596"
---
# <a name="manage-your-cache"></a>A gyorsítótár kezelése

A gyorsítótár áttekintési lapja Azure Portal a projekt részleteit, a gyorsítótár állapotát és a gyorsítótár alapvető statisztikáit. Vezérlőkkel is rendelkezik a gyorsítótár leállítására vagy elindítani, a gyorsítótár törlésére, az adatok hosszú távú tárolóba való kiürítésére és a szoftverek frissítésére.

Ez a cikk azt is bemutatja, hogyan kell elvégezni ezeket az alapszintű feladatokat az Azure CLI-val.

Az áttekintési oldal megnyitásához válassza ki a gyorsítótár-erőforrást a Azure Portal. Töltse be például a **Minden erőforrás lapot,** és kattintson a gyorsítótár nevére.

![képernyőkép egy Azure HPC Cache áttekintési oldalának képernyőképe](media/hpc-cache-overview.png)

Az oldal tetején található gombok segíthetnek a gyorsítótár kezelésében:

* **Indítás** és [**leállítás**](#stop-the-cache) – Folytatja vagy felfüggeszti a gyorsítótár-műveletet
* [**Kiürítés**](#flush-cached-data) – A módosított adatokat tároló célokra írja
* [**Frissítés**](#upgrade-cache-software) – Frissíti a gyorsítótárszoftvert
* [**Diagnosztika gyűjtése**](#collect-diagnostics) – Hibakeresési információk feltöltése
* **Frissítés** – Újra betölti az áttekintő oldalt
* [**Törlés**](#delete-the-cache) – Véglegesen megsemmisíti a gyorsítótárat

Ezekről a lehetőségekről alább olvashat bővebben.

Kattintson az alábbi képre [a](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) gyorsítótár-kezelési feladatokat bemutató videó megtekintése érdekében.

[![video thumbnail: Azure HPC Cache: Kezelés (kattintson ide a videó oldalának megjelenítéséhez)](media/video-5-manage.png)](https://azure.microsoft.com/resources/videos/managing-hpc-cache/)

## <a name="stop-the-cache"></a>A gyorsítótár leállítása

A gyorsítótár leállításával csökkentheti a költségeket inaktív időszakban. A gyorsítótár leállított üzemidejére nem számítunk fel díjat, a gyorsítótár lefoglalt lemezes tárterülete azonban díj ellenében van felszámállítva. (Részletekért tekintse meg [a](https://aka.ms/hpc-cache-pricing) díjszabási oldalt.)

A leállított gyorsítótár nem válaszol az ügyfélkérésekre. A gyorsítótár leállítása előtt leválasztani kell az ügyfeleket.

### <a name="portal"></a>[Portál](#tab/azure-portal)

A **Leállítás** gomb felfüggeszti az aktív gyorsítótárat. A **Leállítás** gomb akkor érhető el, ha a gyorsítótár állapota **Kifogástalan** vagy **Csökkentett teljesítményű.**

![képernyőkép a felső gombokról a Leállítás kiemeléssel és a leállítási műveletet leíró előugró üzenettel, és a "folytatni szeretné?" kérdést Igen (alapértelmezett) és Nem gombokkal](media/stop-cache.png)

Miután az Igen gombra kattint a gyorsítótár leállításának megerősítéséhez, a gyorsítótár automatikusan kiüríti annak tartalmát a céltárolókba. Ez a folyamat némi időt is el fog venni, de biztosítja az adatkonzisztenciát. Végül a gyorsítótár állapota Leállítva **állapotra változik.**

A leállított gyorsítótár újraaktiválhoz kattintson az Indítás **gombra.** Nincs szükség megerősítésre.

![a felső gombok képernyőképe a Kiemelt Indítás gombra kattintva](media/start-cache.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Az Azure CLI beállítása a Azure HPC Cache.](./az-cli-prerequisites.md)

Ideiglenesen függessze fel a gyorsítótárat [az az hpc-cache stop paranccsal.](/cli/azure/hpc-cache#az_hpc_cache_stop) Ez a művelet csak akkor érvényes, ha a gyorsítótár állapota **Kifogástalan** vagy **Csökkentett teljesítményű.**

A gyorsítótár a leállítás előtt automatikusan kiüríti a tartalmát a céltárolókra. Ez a folyamat némi időt is el fog venni, de biztosítja az adatkonzisztenciát.

Ha a művelet befejeződött, a gyorsítótár állapota Leállítva **állapotra változik.**

A leállított gyorsítótár újraaktiválható [az az hpc-cache start val.](/cli/azure/hpc-cache#az_hpc_cache_start)

Amikor kiállítja a start vagy stop parancsot, a parancssor "Fut" állapotüzenetet küld, amíg a művelet be nem fejeződik.

```azurecli
$ az hpc-cache start --name doc-cache0629
 - Running ..
```

A befejezéskor az üzenet "Finished" (Kész) lesz, és megjeleníti a visszatérési kódokat és egyéb információkat.

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

## <a name="flush-cached-data"></a>Gyorsítótárazott adatok kiürítése

Az **áttekintési** oldal Kiürítés gombja arra utasítja a gyorsítótárat, hogy azonnal írja a gyorsítótárban tárolt összes módosított adatot a háttérbeli tároló célokra. A gyorsítótár rendszeresen menti az adatokat a céltárolókba, ezért ezt nem szükséges manuálisan elvégezni, hacsak nem szeretné, hogy a háttértárrendszer naprakész legyen. Használhatja például a Flush (Kiürítés) használhatja a **tároló** pillanatképének készítése vagy az adatkészlet méretének ellenőrzése előtt.

> [!NOTE]
> A kiürítési folyamat során a gyorsítótár nem tudja kiszolgálni az ügyfélkéréseket. A gyorsítótár-hozzáférés fel van függesztve, és a művelet befejezése után folytatódik.

Amikor elindítja a gyorsítótár kiürítési műveletét, a gyorsítótár nem fogadja el az ügyfélkéréseket, és az áttekintő lapon a gyorsítótár állapota **Kiürítés állapotra változik.**

A rendszer a gyorsítótárban lévő adatokat a megfelelő tároló célokra menti. Attól függően, hogy mennyi adatot kell kiüríteni, a folyamat eltarthat néhány percig vagy több mint egy óráig.

Miután az összes adat tárolóhelyre mentve van, a gyorsítótár automatikusan elkezdi ismét az ügyfélkéréseket. A gyorsítótár állapota Kifogástalan **lesz.**

### <a name="portal"></a>[Portál](#tab/azure-portal)

A gyorsítótár ürítéshez kattintson a **Kiürítés** gombra, majd kattintson az **Igen gombra** a művelet megerősítéséhez.

![képernyőkép a felső gombokról, kiemelt Flush gombra, valamint egy előugró üzenettel, amely leírja a kiürítési műveletet, és megkérdezi, hogy folytatja-e a műveletet. Igen (alapértelmezett) és Nem gombokkal](media/hpc-cache-flush.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Az Azure CLI beállítása a Azure HPC Cache.](./az-cli-prerequisites.md)

Az [az hpc-cache flush használatával](/cli/azure/hpc-cache#az_hpc_cache_flush) kényszerítse a gyorsítótárat arra, hogy minden módosított adatot a tárolási célokra írjon.

Példa:

```azurecli
$ az hpc-cache flush --name doc-cache0629 --resource-group doc-rg
 - Running ..
```

Amikor a kiürítés befejeződik, a rendszer sikert ad vissza.

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

## <a name="upgrade-cache-software"></a>Gyorsítótárszoftver frissítése

Ha új szoftververzió érhető el, a **Frissítés** gomb aktívvá válik. A lap tetején egy üzenet is megjelenik a szoftver frissítéséről.

![képernyőfelvétel a felső gombsorról, engedélyezve a Frissítés gombbal](media/hpc-cache-upgrade-button.png)

Az ügyfél-hozzáférés nem szakad meg a szoftverfrissítés során, de a gyorsítótár teljesítménye lassú. Tervezze meg a szoftverek frissítését nem csúcsidőszakban vagy tervezett karbantartási időszakban.

A szoftverfrissítés több órát is igénybe vehet. A nagyobb átviteli sebességre konfigurált gyorsítótárak frissítése hosszabb időt igénybe, mint a kisebb csúcsteljesítményű gyorsítótárak.

Ha elérhetővé válik egy szoftverfrissítés, akkor egy vagy több héttel manuálisan alkalmazhatja azt. A záró dátum megjelenik a frissítési üzenetben. Ha ez idő alatt nem frissít, az Azure automatikusan alkalmazza a frissítést a gyorsítótárra. Az automatikus frissítés időzítése nem konfigurálható. Ha aggódik a gyorsítótár teljesítményére gyakorolt hatás miatt, az időszak lejárta előtt saját maga frissítse a szoftvert.

Ha a gyorsítótár le van állítva, amikor a záró dátum véget ér, a gyorsítótár automatikusan frissíti a szoftvert a következő gombra való töltéskor. (Előfordulhat, hogy a frissítés nem indul el azonnal, de az első órában kezdődik.)

### <a name="portal"></a>[Portál](#tab/azure-portal)

Kattintson **a Frissítés gombra** a szoftverfrissítés megkezdéséhez. A gyorsítótár állapota Frissítésre **változik,** amíg a művelet be nem fejeződik.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Az Azure CLI beállítása a Azure HPC Cache.](./az-cli-prerequisites.md)

Az Azure CLI-ről a gyorsítótár állapotjelentésének végén új szoftverinformációk szerepelnek. (Az [ellenőrzéshez használja az az hpc-cache show](/cli/azure/hpc-cache#az_hpc_cache_show) adatokat.) Keresse meg az "upgradeStatus" sztringet az üzenetben.

Ha van ilyen, az [az hpc-cache upgrade-firmware](/cli/azure/hpc-cache#az_hpc_cache_upgrade-firmware) használatával alkalmazhatja a frissítést.

Ha nem érhető el frissítés, ennek a műveletnek nincs hatása.

Ez a példa bemutatja a gyorsítótár állapotát (nincs elérhető frissítés) és az upgrade-firmware parancs eredményeit.

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

A **Diagnosztika gyűjtése gomb** manuálisan elindítja a rendszerinformációk gyűjtését, majd feltöltését a Microsoft szolgáltatásba és a hibaelhárítási támogatásba. A gyorsítótár automatikusan gyűjti és feltölti ugyanezeket a diagnosztikai adatokat, ha súlyos gyorsítótár-probléma merül fel.

Akkor használja ezt a vezérlőt, ha a Microsoft szolgáltatás és a támogatási szolgálat ezt kéri.

Miután a gombra kattintott, kattintson az **Igen gombra** a feltöltés megerősítéséhez.

![képernyőkép a diagnosztikai gyűjtemény indítását megerősítő előugró üzenetről. Az alapértelmezett "igen" gomb ki van emelve.](media/diagnostics-confirm.png)

## <a name="delete-the-cache"></a>A gyorsítótár törlése

A **Törlés** gomb megsemmisíti a gyorsítótárat. Gyorsítótár törlésekor a rendszer az összes erőforrását megsemmisíti, és nem számít fel további díjakat.

A tároló célokként használt háttértárköteteket ez nem érinti a gyorsítótár törlésekor. Később hozzáadhatja őket egy jövőbeli gyorsítótárhoz, vagy leszerelheti őket külön.

> [!NOTE]
> Azure HPC Cache nem ír automatikusan módosított adatokat a gyorsítótárból a háttértárrendszerekbe a gyorsítótár törlése előtt.
>
> Annak érdekében, hogy a gyorsítótárban lévő összes adat [](#stop-the-cache) hosszú távú tárolóba legyen írva, a törlés előtt állítsa le a gyorsítótárat. A törlés előtt ellenőrizze, hogy a **Leállítva** állapotot jeleníti-e meg.

### <a name="portal"></a>[Portál](#tab/azure-portal)

A gyorsítótár leállítása után kattintson a **Törlés gombra** a gyorsítótár végleges eltávolításához.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Az Azure CLI beállítása a Azure HPC Cache.](./az-cli-prerequisites.md)

A gyorsítótár végleges eltávolításához használja [az az hpc-cache delete Azure CLI-parancsot.](/cli/azure/hpc-cache#az_hpc_cache_delete)

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

## <a name="cache-metrics-and-monitoring"></a>Gyorsítótárazási metrikák és monitorozás

Az áttekintő oldal néhány alapszintű gyorsítótár-statisztika grafikonját jeleníti meg– a gyorsítótár átviteli sebességét, a másodpercenkénti műveleteket és a késést.

![képernyőkép három vonaldiagramról, amely egy minta-gyorsítótár fent említett statisztikáit mutatja](media/hpc-cache-overview-stats.png)

Ezek a diagramok az Azure beépített monitorozási és elemzési eszközeinek részei. További eszközök és riasztások érhetők el a portál oldalsávjának **Figyelés** fejléce alatt található oldalakon. További információt az Azure Monitoring dokumentációjának portál szakaszában [talál.](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-in-the-azure-portal)

## <a name="view-warnings"></a>Figyelmeztetések megtekintése

Ha a gyorsítótár állapota nem megfelelő, ellenőrizze a **Figyelmeztetések** oldalt. Ezen az oldalon a gyorsítótárszoftver értesítései segíthetnek megérteni annak állapotát.

Ezek az értesítések nem jelennek meg a tevékenységnaplóban, mert nem az Azure Portal. Ezek gyakran az Ön által megadott egyéni beállításokhoz vannak társítva.

Az itt látható figyelmeztetések a következők lehetnek:

* A gyorsítótár nem tudja elérni az NTP-kiszolgálót
* A gyorsítótár nem tudta letölteni a kiterjesztett csoportok felhasználónevének adatait
* Az egyéni DNS-beállítások megváltoztak a tárolóhelyen

![képernyőkép a Figyelés > figyelmeztetések lapról, amely azt az üzenetet mutatja, hogy a kiterjesztett csoportok felhasználónevei nem tölthetők le](media/warnings-page.png)

## <a name="next-steps"></a>Következő lépések

* További információ az [Azure metrika- és statisztikai eszközeiről](../azure-monitor/index.yml)
* Segítség [a Azure HPC Cache](hpc-cache-support-ticket.md)
