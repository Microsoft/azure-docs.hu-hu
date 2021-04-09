---
title: A szolgáltatás felügyelete a portálon
titleSuffix: Azure Cognitive Search
description: Felügyelheti az Azure Cognitive Search szolgáltatást, amely egy üzemeltetett felhőalapú keresési szolgáltatás a Microsoft Azureon a Azure Portal használatával.
manager: nitinme
author: HeidiSteen
ms.author: heidist
tags: azure-portal
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/24/2020
ms.openlocfilehash: 814a5afbde548891a30d941365cdd71d227b4767
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101674396"
---
# <a name="service-administration-for-azure-cognitive-search-in-the-azure-portal"></a>Szolgáltatás-felügyelet az Azure Cognitive Search a Azure Portal

> [!div class="op_single_selector"]
>
> * [PowerShell](search-manage-powershell.md)
> * [Azure CLI](search-manage-azure-cli.md)
> * [REST API](/rest/api/searchmanagement/)
> * [.NET SDK](/dotnet/api/microsoft.azure.management.search)
> * [Portál](search-manage.md)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

Az Azure Cognitive Search egy teljes körűen felügyelt, felhőalapú keresési szolgáltatás, amellyel gazdag keresési élményt hozhat létre egyéni alkalmazásokba. Ez a cikk ismerteti azokat a szolgáltatás-felügyeleti feladatokat, amelyeket elvégezhet a [Azure Portalban](https://portal.azure.com) egy már üzembe helyezett keresési szolgáltatáshoz. A szolgáltatás felügyeletét könnyű megtervezni, a következő feladatokra korlátozódik:

* Győződjön meg arról, hogy a tároló a középső oldal **használati** hivatkozását használja.
* Vizsgálja meg a lekérdezési köteteket és a késést a középső oldal **figyelési** hivatkozásának használatával, valamint hogy a kérelmek szabályozása megtörtént-e.
* A hozzáférés kezelése a **kulcsok** oldal bal oldalán.
* Állítsa be a kapacitást a **Méretezés** lapon balra.

A portálon végrehajtott feladatokat a [felügyeleti API](/rest/api/searchmanagement/) -k és az [az. Search PowerShell-modul](search-manage-powershell.md)használatával is lehet programozott módon kezelni. A felügyeleti feladatok teljes mértékben képviseltetik magukat a portál és a programozott felületek között. Nincs olyan konkrét felügyeleti feladat, amely csak egy módozatban érhető el.

Az Azure Cognitive Search további Azure-szolgáltatásokat használ a további monitorozáshoz és felügyelethez. Önmagában az egyetlen keresési szolgáltatással tárolt adattartalom (indexek, indexelő és adatforrás-definíciók és egyéb objektumok). A portál oldalain jelentett mérőszámok a 30 napos időszakon belüli belső naplókból vannak kihúzva. A felhasználó által vezérelt naplózás és a további események esetében [Azure monitorra](../azure-monitor/index.yml)lesz szüksége. 

## <a name="fixed-service-properties"></a>Rögzített szolgáltatás tulajdonságai

A keresési szolgáltatás több aspektusa is meg van határozva, ha a szolgáltatás kiépítve lett, és később nem módosítható:

* Szolgáltatás neve (a szolgáltatás nem nevezhető át)
* Szolgáltatás helye (jelenleg nem helyezhető át egy érintetlen szolgáltatás egy másik régióba)
* A replika és a partíciók maximális száma (a szint, az alapszintű vagy a standard alapján meghatározva)

Ha az alapszintű és egy partícióval rendelkezik, és most már több partícióra van szüksége, [új szolgáltatást kell létrehoznia](search-create-service-portal.md) egy magasabb szintű szinten, és újra létre kell hoznia a tartalmat az új szolgáltatáson. 

## <a name="administrator-rights"></a>Rendszergazdai jogosultságok

A szolgáltatás kiépítés vagy leszerelése egy Azure-előfizetés rendszergazdája vagy egy társ-rendszergazda által végezhető el.

A végponthoz való hozzáférés tekintetében bárki hozzáférhet a szolgáltatás URL-címéhez, és egy API-kulcs hozzáfér a tartalomhoz. A kulcsokkal kapcsolatos további információkért lásd: [az API-kulcsok kezelése](search-security-api-keys.md).

* A szolgáltatáshoz csak olvasási hozzáférés van lekérdező jogosultság, amelyet általában egy ügyfélalkalmazás biztosít az URL-cím és a lekérdezési API-kulcs megadásával.
* Az írási és olvasási hozzáférés lehetővé teszi a kiszolgálói objektumok hozzáadását, törlését és módosítását, beleértve az API-kulcsokat, az indexeket, az indexelő, az adatforrásokat és az ütemterveket. Az olvasási és írási hozzáférés az URL-cím, egy rendszergazdai API-kulcs megadásával adható meg.

A szolgáltatás kiépítési berendezéséhez szükséges jogosultságokat a szerepkör-hozzárendelések biztosítják. Az Azure [szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../role-based-access-control/overview.md) az Azure-erőforrások kiépítésére [Azure Resource Manager](../azure-resource-manager/management/overview.md) épülő engedélyezési rendszer. 

Az Azure Cognitive Search kontextusában az [Azure szerepkör-hozzárendelések](search-security-rbac.md) határozzák meg, hogy ki végezhet el feladatokat, függetlenül attól, hogy a [portált](search-manage.md), a [PowerShellt](search-manage-powershell.md)vagy a [felügyeleti REST API-kat](/rest/api/searchmanagement/search-howto-management-rest-api)használják:

* Szolgáltatás létrehozása vagy törlése
* A szolgáltatás skálázása
* API-kulcsok törlése vagy újragenerálása
* Diagnosztikai naplózás engedélyezése (szolgáltatások létrehozása)
* Traffic Analytics engedélyezése (szolgáltatások létrehozása)

> [!TIP]
> Az Azure-ra kiterjedő mechanizmusok használatával zárolhatja az előfizetést vagy az erőforrást, így megakadályozhatja a keresési szolgáltatás véletlen vagy jogosulatlan törlését rendszergazdai jogosultságokkal rendelkező felhasználók számára. További információ: [erőforrások zárolása a váratlan törlés megakadályozása érdekében](../azure-resource-manager/management/lock-resources.md).

## <a name="logging-and-system-information"></a>Naplózási és rendszerinformációk

Az alapszintű és újabb verziók esetében a Microsoft minden Azure Cognitive Search-szolgáltatást az 99,9%-os rendelkezésre állást figyeli a szolgáltatói szerződések (SLA) esetében. Ha a szolgáltatás lassú, vagy az átviteli sebesség az SLA-küszöbérték alá esik, a támogatási csapatoknak tekintse át a rendelkezésre álló naplófájlokat, és foglalkozzon a probléma megoldásával.

Az Azure Cognitive Search az indexelési és lekérdezési tevékenységek gyűjtésére és tárolására [Azure monitor](../azure-monitor/index.yml) használ. A keresési szolgáltatás önmagában csak a tartalmát tárolja (indexek, indexelő definíciók, adatforrás-definíciók, készségkészlet-definíciók, szinonimák leképezései). A gyorsítótárazás és a naplózott adatok a szolgáltatáson kívül, gyakran egy Azure Storage-fiókban tárolódnak. Az indexelési és lekérdezési számítási feladatok naplózásával kapcsolatos további információkért lásd: [naplózási adatok gyűjtése és elemzése](search-monitor-logs.md).

A szolgáltatással kapcsolatos általános információk alapján az Azure Cognitive Search beépített szolgáltatásainak használatával a következő módokon szerezheti be az információkat:

* A szolgáltatás **áttekintése** lap értesítések, tulajdonságok és állapotüzenetek használatával.
* A [szolgáltatás tulajdonságainak beolvasása](/rest/api/searchmanagement/services)a [PowerShell](search-manage-powershell.md) vagy a [felügyeleti REST API](/rest/api/searchmanagement/) használatával. Nem található új információ vagy művelet a programozott rétegben. A felületek léteznek, így parancsfájlok írhatók.

## <a name="monitor-resource-usage"></a>Erőforrás-használat figyelése

Az irányítópulton az erőforrás-figyelés a szolgáltatás irányítópultján megjelenő információkra korlátozódik, és néhány mérőszámot, amelyet a szolgáltatás lekérdezésével érhet el. A szolgáltatás irányítópultjának használat szakaszában gyorsan meghatározhatja, hogy a partíciós erőforrások szintje megfelelő-e az alkalmazáshoz. Ha a naplózott eseményeket szeretné rögzíteni és megőrizni, külső erőforrásokat (például az Azure-figyelést) is kiépítheti. További információ: az [Azure Cognitive Search figyelése](search-monitor-usage.md).

A Search szolgáltatás REST API használatával a dokumentumok és indexek száma programozott módon is elvégezhető: 

* [Index statisztikáinak beolvasása](/rest/api/searchservice/Get-Index-Statistics)
* [Dokumentumok számlálása](/rest/api/searchservice/count-documents)

## <a name="disaster-recovery-and-service-outages"></a>Vész-helyreállítási és szolgáltatás-kimaradások

Bár az adatai megmentését is lehetővé teszi, az Azure Cognitive Search nem biztosítja a szolgáltatás azonnali feladatátvételét, ha a fürt vagy az adatközpont szintjén áramkimaradás történik. Ha egy fürt meghibásodik az adatközpontban, az operatív csapat felismeri és a szolgáltatás visszaállítását végzi. A szolgáltatás visszaállítása során állásidőt tapasztalhat, de a szolgáltatási kreditek igénylésével kompenzálhatja a szolgáltatás nem rendelkezésre állását a [szolgáltatói szerződés (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). 

Ha a Microsoft általi ellenőrzésen kívüli katasztrofális hibák esetén folyamatos szolgáltatásra van szükség, egy másik régióban is [kiépítheti a további](search-create-service-portal.md) szolgáltatásokat, és a Geo-replikációs stratégia megvalósításával biztosíthatja, hogy az indexek teljes mértékben redundánsak legyenek az összes szolgáltatásban.

Azok az ügyfelek, akik [Indexelő](search-indexer-overview.md) adatokat használnak az indexek feltöltéséhez és frissítéséhez, az azonos adatforrást használó geo-specifikus indexelő segítségével kezelhetik a vész-helyreállítást. A különböző régiókban található két szolgáltatás, amelyek mindegyike indexelt, indexelheti ugyanazt az adatforrást a Geo-redundancia eléréséhez. Ha olyan adatforrásokból származó indexelést is használ, amelyek földrajzilag redundánsak, vegye figyelembe, hogy az Azure Cognitive Search indexelő csak növekményes indexelést végezhetnek (új, módosított vagy törölt dokumentumokból származó frissítések egyesítése) az elsődleges replikából. Feladatátvételi esemény esetén ügyeljen arra, hogy az indexelő újra az új elsődleges replikára irányítsa. 

Ha nem használ indexelő funkciót, az alkalmazás kódjával párhuzamosan küldheti el az objektumokat és az adatait a különböző keresési szolgáltatásoknak. További információ: [teljesítmény és optimalizálás az Azure Cognitive Searchban](search-performance-optimization.md).

## <a name="backup-and-restore"></a>Biztonsági mentés és visszaállítás

Mivel az Azure Cognitive Search nem elsődleges adattárolási megoldás, nem biztosítunk formális mechanizmust az önkiszolgáló biztonsági mentéshez és visszaállításhoz. Az [Azure Cognitive Search .net minta](https://github.com/Azure-Samples/azure-search-dotnet-samples) -tárházban található **index-Backup-Restore** mintakód használatával azonban az index definícióját és a pillanatképet egy sor JSON-fájlra is felhasználhatja, majd ezekkel a fájlokkal visszaállíthatja az indexet, ha szükséges. Ez az eszköz az indexeket is át tudja helyezni a szolgáltatási szintek között.

Ellenkező esetben az index létrehozásához és feltöltéséhez használt alkalmazás kódja a de facto Visszaállítási lehetőség, ha tévedésből töröl egy indexet. Az indexek újraépítéséhez törölnie kell azt (feltéve, hogy létezik), újra létre kell hoznia az indexet a szolgáltatásban, majd újra kell töltenie az adatok elsődleges adattárból való beolvasásával.

## <a name="scale-up-or-down"></a>Vertikális fel- és leskálázás

Minden keresési szolgáltatás legalább egy replikával és egy partícióval indul el. Ha olyan platformra regisztrált, [amely támogatja a nagyobb kapacitást](search-limits-quotas-capacity.md), kattintson a bal oldali navigációs ablaktábla **Méretezés** elemére az erőforrás-használat módosításához.

Ha erőforráson keresztül ad hozzá kapacitást, a szolgáltatás automatikusan ezeket használja. Az Ön részéről nincs szükség további műveletre, de az új erőforrás hatásának megvalósulása némi késéssel jár. További erőforrások kiépítéséhez akár 15 percet is igénybe vehet.

### <a name="add-replicas"></a>Replikák hozzáadása

A lekérdezések másodpercenkénti száma (QPS) vagy a magas rendelkezésre állás elérése a replikák hozzáadásával történik. Minden replika egyetlen másolattal rendelkezik egy indexből, így a szolgáltatás lekérdezési kéréseinek kiszolgálásához még egy replika hozzáadására van lehetőség. A magas rendelkezésre álláshoz legalább 3 replika szükséges (lásd: a [kapacitás módosítása](search-capacity-planning.md) a részletekhez).

A több replikát tartalmazó keresési szolgáltatás a lekérdezési kérelmeket nagyobb számú indexnél tudja betölteni. A lekérdezési mennyiség szintje miatt a lekérdezési átviteli sebesség gyorsabb lesz, ha a kérés kiszolgálásához több példány is rendelkezésre áll az indexben. Ha a lekérdezés késését tapasztalja, pozitív hatással lehet a teljesítményre, ha a további replikák online állapotban vannak.

Bár a lekérdezési átviteli sebesség a replikák hozzáadásakor leáll, a replikák szolgáltatáshoz való hozzáadásakor nem lehet pontosan dupla vagy tripla. Az összes keresési alkalmazásra olyan külső tényezők vonatkoznak, amelyek hatással lehetnek a lekérdezési teljesítményre. Az összetett lekérdezések és a hálózati késés két tényező, amelyek hozzájárulnak a lekérdezési válaszidő változásaihoz.

### <a name="add-partitions"></a>Partíciók hozzáadása

Sokkal gyakoribb a replikák hozzáadása, de ha korlátozott a tárterület, hozzáadhat partíciókat, amelyekkel nagyobb kapacitást érhet el. A szolgáltatás kiépített szintje határozza meg, hogy a partíciók hozzáadhatók-e. Az alapszintű csomag egyetlen partíción van zárolva. A standard szintű csomagok és a fenti csomagok további partíciókat is támogatnak.

A partíciók 12 (konkrét, 1, 2, 3, 4, 6 vagy 12) osztóban lesznek hozzáadva. Ez egy horizontális skálázási összetevő. Az indexek 12 szegmensben jönnek létre, amelyek mindegyike 1 partíción tárolható, vagy egyenlően 2, 3, 4, 6 vagy 12 partícióra osztható (egy-egy szegmens/partíció).

### <a name="remove-replicas"></a>Replikák eltávolítása

A nagy mennyiségű lekérdezési kötetek után a csúszka segítségével csökkentheti a replikákat a keresési lekérdezés terhelésének normalizálása után (például az üdülési értékesítések felett). Az Ön részéről nincs szükség további lépésekre. A replikák számának csökkentése lemond az adatközpontban található virtuális gépekről. A lekérdezési és az adatfeldolgozási műveletek mostantól kevesebb virtuális gépen futnak, mint korábban. A minimális követelmény egy replika.

### <a name="remove-partitions"></a>Partíciók eltávolítása

A replikák eltávolításával szemben, amelyhez nincs szükség további erőfeszítésre, előfordulhat, hogy néhány teendője van, ha több tárterületet használ, mint amennyit csökkenteni lehet. Ha például a megoldás három partíciót használ, az egy vagy két partícióra való leépítés hibát eredményez, ha az új tárolóhely kisebb, mint az index üzemeltetéséhez szükséges. Az elvárásoknak megfelelően lehetőség van az indexek vagy dokumentumok törlésére egy társított indexen belül, hogy felszabadítsa a helyet, vagy megtartja a jelenlegi konfigurációt.

Nincs olyan észlelési módszer, amely közli, hogy mely indexek vannak tárolva egy adott partíción. Az egyes partíciók körülbelül 25 GB tárhelyet biztosítanak, ezért a tárterületet a szükséges partíciók számával kell csökkenteni. Ha egy partícióra kíván visszaállítani, akkor mind a 12 szegmensnek el kell férnie.

Ha segítségre van szüksége a jövőbeli tervezéssel kapcsolatban, érdemes lehet megtekinteni a tárterületet (az [indexek statisztikájának lekérése](/rest/api/searchservice/Get-Index-Statistics)használatával), hogy megtekintse, mennyit használt 

## <a name="next-steps"></a>Következő lépések

* Automatizálás a [PowerShell](search-manage-powershell.md) -lel vagy az [Azure CLI](search-manage-azure-cli.md) -vel

* [Teljesítmény-és optimalizálási](search-performance-optimization.md) technikák áttekintése

* A tartalom és a műveletek védelméhez szükséges [biztonsági funkciók](search-security-overview.md) áttekintése

* [Diagnosztikai naplózás](search-monitor-logs.md) engedélyezése a lekérdezési és indexelési számítási feladatok figyeléséhez