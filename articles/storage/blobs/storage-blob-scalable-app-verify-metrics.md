---
title: Átviteli sebességgel és késéssel kapcsolatos mérőszámok ellenőrzése tárfiókok esetében az Azure Portalon | Microsoft Docs
description: Megismerkedhet a tárfiókok átviteli sebességével és késésével kapcsolatos mérőszámok ellenőrzésével a portálon.
author: roygara
ms.service: storage
ms.topic: tutorial
ms.date: 02/20/2018
ms.author: rogarana
ms.subservice: blobs
ms.openlocfilehash: 1bec42dabde64fed8126e47e39f19536b28014f1
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101714833"
---
# <a name="verify-throughput-and-latency-metrics-for-a-storage-account"></a>Átviteli sebességgel és késéssel kapcsolatos mérőszámok ellenőrzése tárfiókok esetében

Ez az oktatóanyag egy sorozat negyedik, utolsó része. Az előző oktatóanyagban megismerhette nagy mennyiségű véletlenszerű adat fel- és letöltésének módját egy Azure Storage-fiókba vagy fiókból. Az oktatóanyag a mérőszámok használatát mutatja be az átviteli sebesség és a késés megtekintéséhez az Azure Portalon.

A sorozat negyedik részében az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Diagramok konfigurálása az Azure Portalon
> * Átviteli sebességgel és késéssel kapcsolatos mérőszámok ellenőrzése

Az [Azure Storage mérőszámai](./monitor-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) az Azure Monitor használatával adnak egységes képet a tárfiók teljesítményéről és rendelkezésre állásáról.

## <a name="configure-metrics"></a>Mérőszámok konfigurálása

Navigáljon a **metrikák** elemre a Storage-fiók **Beállítások** területén.

Válassza a Blob lehetőséget az **ALSZOLGÁLTATÁS** legördülő listáról.

A **METRIKA** területen válassza ki a következő táblázatban található mérőszámok egyikét:

Az alábbi mérőszámok segítségével képet kaphat az alkalmazás késésével és átviteli sebességével kapcsolatban. A portálon konfigurált mérőszámok egyperces átlagértékek. Ha egy tranzakció egy percen belül fejeződött be, akkor a percben megadott érték az átlag felére csökken. Az alkalmazásban a fel- és letöltési műveletek időzítve vannak, és a rendszer a kimenetek között megjeleníti a fájlok fel- és letöltéséhez szükséges tényleges időt. Ezek az információk és a portál mérőszámai együttesen teljes körűen értelmezhetővé teszik az átviteli sebességet.

|Metric|Definíció|
|---|---|
|**Sikeres kérések végpontok közötti késése**|A tárolási szolgáltatás vagy a megadott API-művelet számára elküldött sikeres kérések végpontok közötti késésének átlaga. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|
|**Sikerességi kiszolgáló késése**|Az Azure Storage által sikeresen feldolgozott kérések átlagos feldolgozási ideje. Ez az érték nem tartalmazza a SuccessE2ELatency paraméterben megadott hálózati késleltetést. |
|**Tranzakciók**|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és sikertelen kérések, valamint a hibára futott kérések számát tartalmazza. Ebben a példában a blokkméret 100 MB-ra lett beállítva. Ebben az esetben minden 100 MB-os blokk tranzakciónak minősül.|
|**Bejövő forgalom**|A bejövő adatok mennyisége. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja. |
|**Kimenő forgalom**|A kimenő adatok mennyisége. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat. |

Válassza az **Idő** elem melletti **Elmúlt 24 óra (Automatikus)** lehetőséget. Az **Idő részletesség** esetében válassza az **Utolsó óra** és a **Perc** lehetőséget, majd kattintson az **Alkalmaz** gombra.

![Tárfiókok mérőszámai](./media/storage-blob-scalable-app-verify-metrics/figure1.png)

A diagramokhoz több mérőszám is társítható, ekkor azonban a rendszer letiltja a dimenziók szerinti csoportosítás lehetőségét.

## <a name="dimensions"></a>Dimenziók

A [Dimenziók](./monitor-blob-storage-reference.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#metrics-dimensions) segítségével részletesebben megismerhetők a diagramok, és részletesebb információk nyerhetők ki belőlük. A különböző metrikák eltérő dimenziókkal rendelkeznek. Az egyik rendelkezésre álló dimenzió az **API-név** dimenzió. Ez a dimenzió külön API-hívásokra osztja szét a diagramot. Az első alábbi képen egy tárfiókhoz tartozó összes tranzakció diagramjára látható példa. A második képen ugyanez a diagram látható, ezúttal viszont a kiválasztott API-név dimenzióval együtt. Ahogy látható, a listában szereplő tranzakciók mindegyike további részleteket biztosít az API-név által indított meghívások számával kapcsolatban.

![Tárfiókok mérőszámai – dimenzió nélküli tranzakciók](./media/storage-blob-scalable-app-verify-metrics/transactionsnodimensions.png)

![Tárfiókok mérőszámai – tranzakciók](./media/storage-blob-scalable-app-verify-metrics/transactions.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot, a virtuális gépet és az összes kapcsolódó erőforrást. Ehhez válassza ki a virtuális gép erőforráscsoportját, és kattintson a Törlés elemre.

## <a name="next-steps"></a>Következő lépések

A sorozat negyedik részében megismerkedett a példamegoldáshoz tartozó mérőszámok megtekintésével, például a következőkkel:

> [!div class="checklist"]
> * Diagramok konfigurálása az Azure Portalon
> * Átviteli sebességgel és késéssel kapcsolatos mérőszámok ellenőrzése

Kövesse ezt a hivatkozást az előre felépített tárolóminták megtekintéséhez.

> [!div class="nextstepaction"]
> [Azure Storage-szkriptminták](storage-samples-blobs-cli.md)

[previous-tutorial]: storage-blob-scalable-app-download-files.md