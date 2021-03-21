---
title: Azure Cosmos-tárolók és-adatbázisok létrehozása az autoskálázási módban.
description: Ismerje meg az előnyeit, használati eseteit, valamint az Azure Cosmos-adatbázisok és-tárolók kiépítését az autoscale módban.
author: kirillg
ms.author: kirillg
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/11/2020
ms.custom: seo-nov-2020
ms.openlocfilehash: ba0dd347c4ee2cb41b34c2fc34f1848a7295dc3a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97368664"
---
# <a name="create-azure-cosmos-containers-and-databases-with-autoscale-throughput"></a>Azure Cosmos-tárolók és-adatbázisok létrehozása az autoscale átviteli sebességgel
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB a standard (manuális) vagy az automatikus skálázási átviteli sebesség konfigurálható az adatbázisokon és a tárolókban is. Az automatikus méretezés kiépített átviteli sebessége Azure Cosmos DB lehetővé teszi az **adatbázis vagy tároló átviteli sebességének (ru/s) méretezését automatikusan és azonnal**. Az átviteli sebesség a használat alapján méretezhető, anélkül, hogy ez befolyásolná a munkaterhelés rendelkezésre állását, késését, átviteli sebességét vagy teljesítményét.

Az autoscale kiépített átviteli sebessége kiválóan alkalmas olyan kritikus fontosságú számítási feladatokhoz, amelyek változó vagy kiszámíthatatlan forgalmi mintákkal rendelkeznek, és nagy teljesítményű és méretezhető SLA-kat igényelnek. Ez a cikk az automatikus skálázással kiosztott átviteli sebesség előnyeit és használati eseteit ismerteti.

## <a name="benefits-of-autoscale"></a>Az autoscale előnyei

Az Azure Cosmos-adatbázisok és-tárolók, amelyek az autoscale kiosztott átviteli sebességgel vannak konfigurálva, a következő előnyöket nyújtják:

* **Egyszerű:** Az automatikus skálázás eltávolítja az RU/s felügyeletének összetettségét egyéni parancsfájlkezeléssel vagy a kapacitás manuális skálázásával. 

* **Skálázható:** Az adatbázisok és a tárolók szükség szerint automatikusan méretezhetik a kiépített átviteli sebességet. Az ügyfélkapcsolatok, az alkalmazások és a Azure Cosmos DB SLA-kat érintő hatás nem szakad meg.

* **Költséghatékony:** Az automatikus skálázás segítségével optimalizálhatja az RU/s használatát és a költséghatékonyságot, ha nincs használatban. Csak azon erőforrásokért kell fizetnie, amelyeket a számítási feladatokhoz óránként kell fizetni. Egy hónap összes órájában, ha az autoscale Max RU/s (Tmax) értéket állítja be, és a teljes mennyiséget az órák 66%-ában használja, akkor a rendszer az autoscale (méret) értékkel fogja menteni a Tmax. További információ: a [Standard (manuális) és az Automatikus méretezéssel kiépített átviteli sebesség kiválasztása](how-to-choose-offer.md) című cikk.

* **Magasan elérhető:** Az autoscale-t használó adatbázisok és tárolók ugyanazt a globálisan elosztott, hibatűrő, magas rendelkezésre állású Azure Cosmos DB háttérrendszer használatával biztosítják az adattartósságot és a magas rendelkezésre állást.

## <a name="use-cases-of-autoscale"></a>Az autoscale használatának esetei

Az autoscale használati esetei a következők:

* **Változó vagy kiszámíthatatlan számítási feladatok:** Ha a számítási feladatokhoz változó vagy előre nem látható tüskék tartoznak a használatban, az automatikus skálázás a használat alapján automatikusan fel-és leskálázást nyújt. Ilyenek például azok a kiskereskedelmi webhelyek, amelyek eltérő forgalmi mintákkal rendelkeznek a szezonális környezettől függően; IOT munkaterhelések, amelyek a nap folyamán különböző időpontokban vannak. olyan üzletági alkalmazások, amelyek a maximális kihasználtságot a havonta vagy évente több alkalommal látják el. Az autoscale használatával már nem kell manuálisan kiépíteni a maximális vagy az átlagos kapacitást. 

* **Új alkalmazások:** Ha új alkalmazást fejleszt, és nem biztos benne, hogy milyen átviteli sebességre (RU/s) van szüksége, az autoscale megkönnyíti az első lépéseket. Az automatikus skálázási belépési pont 400-4000 RU/s, a használat figyelése és a megfelelő RU/s meghatározása az idő múlásával.

* **Ritkán használt alkalmazások:** Ha olyan alkalmazással rendelkezik, amely egy nap, hét vagy hónap során többször is használatban van, például egy kis mennyiségű alkalmazás/Web/Blog webhely esetében, az autoscale megváltoztatja a maximális kihasználtságot, és lekicsinyíti a terhelést. 

* **Fejlesztési és tesztelési feladatok:** Ha Ön vagy csapata az Azure Cosmos-adatbázisokat és-tárolókat munkaidőben használja, de nem szükséges éjszakára vagy hétvégére használni, az automatikus méretezés a használat során minimálisra csökkenti a költségeket. 

* **Ütemezett üzemi munkaterhelések/lekérdezések:** Ha olyan ütemezett kérelmeket, műveleteket vagy lekérdezéseket hajt végre, amelyeket tétlen időszakok alatt szeretne futtatni, ezt egyszerűen megteheti az autoscale használatával. Ha futtatnia kell a munkaterhelést, az átviteli sebesség automatikusan a szükséges értékre lesz méretezve, és ezt követően le kell rövidíteni. 

Az ezekre a problémákra kiépített egyéni megoldás nem csupán nagy mennyiségű időt igényel, de az alkalmazás konfigurációjában vagy kódjában is komplexitást jelent. Az automatikus méretezés lehetővé teszi, hogy a fenti forgatókönyvek el legyenek távolítva a dobozból, és nincs szükség a kapacitás egyéni vagy manuális skálázására. 

## <a name="how-autoscale-provisioned-throughput-works"></a>Az autoscale kiépített átviteli sebesség működése

A tárolók és adatbázisok autoskálázással való konfigurálásakor meg kell adnia a maximális átviteli sebességet `Tmax` . Azure Cosmos DB méretezi az átviteli `T` sebességet `0.1*Tmax <= T <= Tmax` . Ha például a maximális átviteli sebességet 20 000 RU/s értékre állítja, az átviteli sebesség a 2000 és a 20 000 RU/s között lesz méretezhető. Mivel a skálázás automatikus és azonnali, a szolgáltatás bármikor felhasználható a `Tmax` késleltetés nélkül. 

Minden órában a legmagasabb átviteli sebességért kell fizetni, amelyet a `T` rendszer az órán belül felskálázással végez.

Az autoskálázás maximális átviteli sebességének belépési pontja `Tmax` 4000 ru/s értékkel kezdődik, amely 400-4000 ru/s-ig méretezhető. Megadhatja a `Tmax` 1000 ru/s növekményeket, és bármikor módosíthatja az értéket.  

## <a name="enable-autoscale-on-existing-resources"></a>Az autoskálázás engedélyezése meglévő erőforrásokon

A [Azure Portal](how-to-provision-autoscale-throughput.md#enable-autoscale-on-existing-database-or-container), [Azure Resource Manager sablon](how-to-provision-autoscale-throughput.md#azure-resource-manager), [CLI](how-to-provision-autoscale-throughput.md#azure-cli) vagy [PowerShell](how-to-provision-autoscale-throughput.md#azure-powershell) használatával engedélyezheti az autoskálázást egy meglévő adatbázison vagy tárolón. Az automatikus méretezés és a standard (manuális) kiépített átviteli sebesség között bármikor válthat. További információért tekintse meg ezt a [dokumentációt](autoscale-faq.md#how-does-the-migration-between-autoscale-and-standard-manual-provisioned-throughput-work) .

## <a name="throughput-and-storage-limits-for-autoscale"></a><a id="autoscale-limits"></a> Átviteli sebesség és tárolási korlátok az autoskálázáshoz

Bármely érték esetén `Tmax` az adatbázis vagy a tároló a teljes összeget tárolhatja `0.01 * Tmax GB` . Ennek elérésekor a maximális RU/s érték automatikusan növekedni fog a tárterület új értékének megfelelően, és ez nincs hatással az alkalmazásra. 

Ha például 50 000-es maximális RU/s értékkel kezdett (a skálázás 5000 és 50 000 RU/s között történik), akkor legfeljebb 500 GB adatot tárolhat. Ha túllépi az 500 GB-ot (például a tárterület 600 GB), az új maximális RU/s érték 60 000 lesz (a skálázás 6000 és 60 000 RU/s között történik).

Ha adatbázisszintű átviteli sebességét használ automatikus skálázással, akkor az első 25 tároló legfeljebb 4000 RU/s automatikus skálázású RU/s értéken osztozhat (a skálázás 400 és 4000 RU/s között történik), amíg a tárterület mérete nem haladja meg a 40 GB-ot. További információért tekintse meg ezt a [dokumentációt](autoscale-faq.md#can-i-change-the-max-rus-on-the-database-or-container) .

## <a name="comparison--containers-configured-with-manual-vs-autoscale-throughput"></a>Összehasonlítás – manuális és automatikus skálázási átviteli sebességgel konfigurált tárolók
További részletekért tekintse meg ezt a [dokumentációt](how-to-choose-offer.md) a standard (manuális) és az automatikus skálázási teljesítmény választásához.  

|| Standard (manuális) átviteli sebességgel rendelkező tárolók  | Tárolók az autoscale átviteli sebességgel |
|---------|---------|---------|
| **Kiosztott átviteli sebesség (RU/s)** | Manuálisan kiépítve. | Automatikusan és azonnal méretezhető a munkaterhelés-használati minták alapján. |
| **Kérelmek/műveletek korlátozása (429)**  | Előfordulhat, hogy a felhasználás meghaladja a kiosztott kapacitást. | Nem fog történni, ha az automatikusan beállított átviteli sebességi tartományon belül a RU/s-t használja.    |
| **Kapacitástervezés** |  A kapacitás megtervezése és a szükséges pontos átviteli sebesség kiépítése szükséges. |    A rendszer automatikusan gondoskodik a kapacitás megtervezéséről és a kapacitások kezeléséről. |
| **Díjszabás** | A manuálisan kiosztott RU/mp óradíjat kell fizetnie a [Standard (manuális) ru/s](https://azure.microsoft.com/pricing/details/cosmos-db/)óránkénti díjszabás alapján. | A legmagasabb RU/s esetében óránkénti fizetést kell fizetnie, a rendszer pedig az órán belül felskálázást. <br/><br/> Az egyszeri írási régió fiókjai esetében óradíjban kell fizetnie az RU/s esetében, az [autoscale ru/s](https://azure.microsoft.com/pricing/details/cosmos-db/)óradíjat használva. <br/><br/>Több írási régióval rendelkező fiókok esetében az autoskálázás díjmentes. Az óránkénti átviteli sebességért kell fizetnie, ugyanazzal a [többrégiós írási ru/s](https://azure.microsoft.com/pricing/details/cosmos-db/)óradíjat használva. |
| **Legmegfelelőbb a számítási feladatok típusaihoz** |  Kiszámítható és stabil számítási feladatok|   Kiszámíthatatlan és változó számítási feladatok  |

## <a name="next-steps"></a>Következő lépések

* Tekintse át az [autoscale – gyakori kérdések](autoscale-faq.md)című szakaszt.
* Megtudhatja, hogyan [választhat a manuális és az automatikus skálázási sebesség közül](how-to-choose-offer.md).
* Megtudhatja, hogyan építhet ki az [Azure Cosmos-adatbázison vagy-tárolón az adatméretezési sebességet](how-to-provision-autoscale-throughput.md).
* További információ a [particionálásról](partitioning-overview.md) Azure Cosmos db.


