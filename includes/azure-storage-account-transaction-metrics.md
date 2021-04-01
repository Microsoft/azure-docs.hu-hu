---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: ac91ef5a56fe234cba47b430b78e74ce81c9d504
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96536992"
---
Az Azure Storage a következő tranzakciós mérőszámokat biztosítja Azure Monitorban.

| Metric | Leírás |
| ------------------- | ----------------- |
| Tranzakciók | Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és sikertelen kérések, valamint a hibára futott kérések számát tartalmazza. <br/><br/> Egység: darabszám <br/> Összesítés típusa: összesen <br/> Alkalmazandó méretek: ResponseType, GeoType, ApiName és hitelesítés ([definíció](#metrics-dimensions))<br/> Érték példa: 1024 |
| Bejövő forgalom | A bejövő adatok mennyisége. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja. <br/><br/> Egység: bájtok <br/> Összesítés típusa: összesen <br/> Alkalmazandó méretek: GeoType, ApiName és hitelesítés ([definíció](#metrics-dimensions)) <br/> Érték példa: 1024 |
| Kimenő forgalom | A kimenő adatok mennyisége. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat. <br/><br/> Egység: bájtok <br/> Összesítés típusa: összesen <br/> Alkalmazandó méretek: GeoType, ApiName és hitelesítés ([definíció](#metrics-dimensions)) <br/> Érték példa: 1024 |
| SuccessServerLatency | Az Azure Storage által sikeresen feldolgozott kérések átlagos feldolgozási ideje. Ez az érték nem tartalmazza a SuccessE2ELatency paraméterben megadott hálózati késleltetést. <br/><br/> Egység: ezredmásodperc <br/> Összesítés típusa: átlag <br/> Alkalmazandó méretek: GeoType, ApiName és hitelesítés ([definíció](#metrics-dimensions)) <br/> Érték példa: 1024 |
| SuccessE2ELatency | A tárolási szolgáltatás vagy a megadott API-művelet számára elküldött sikeres kérések végpontok közötti késésének átlaga. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt. A SuccessE2ELatency és a SuccessServerLatency értékek közötti különbség a hálózat és az ügyfél által valószínűleg okozott késés.<br/><br/> Egység: ezredmásodperc <br/> Összesítés típusa: átlag <br/> Alkalmazandó méretek: GeoType, ApiName és hitelesítés ([definíció](#metrics-dimensions)) <br/> Érték példa: 1024 |
| Rendelkezésre állás | A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. A Rendelkezésre állás az összes számlázható kérelem értékének és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik. <br/><br/> Egység: százalék <br/> Összesítés típusa: átlag <br/> Alkalmazandó méretek: GeoType, ApiName és hitelesítés ([definíció](#metrics-dimensions)) <br/> Érték példa: 99,99 |
