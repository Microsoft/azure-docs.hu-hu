---
title: Azure NetApp Files kötetek régiók közötti replikációja | Microsoft Docs
description: Leírja, hogy mi Azure NetApp Files régiók közötti replikáció, a támogatott régió párok, a szolgáltatási szint célkitűzései, az adattartósság és a cost modell.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/23/2020
ms.author: b-juche
ms.openlocfilehash: 4df96f1c508df22e98a9c611ed04fecf6734970a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91278294"
---
# <a name="cross-region-replication-of-azure-netapp-files-volumes"></a>Azure NetApp Files kötetek régiók közötti replikációja

A Azure NetApp Files replikációs funkció adatvédelmet biztosít a régiók közötti kötetek replikálásával. Az adatok aszinkron módon replikálhatók egy adott régióban lévő Azure NetApp Files kötetről egy másik Azure NetApp Files kötetre (cél) egy másik régióban.  Ez a funkció lehetővé teszi a kritikus fontosságú alkalmazások feladatátvételét a régióra kiterjedő leállás vagy katasztrófa esetén.

> [!IMPORTANT]
> A régiók közötti replikációs szolgáltatás jelenleg nyilvános előzetes verzióban érhető el. Be kell küldenie egy várólistára vonatkozó kérelmet a szolgáltatás eléréséhez a [Azure NetApp Files régiók közötti replikációs várólista-küldési lapon](https://aka.ms/anfcrrpreviewsignup). Várjon egy hivatalos visszaigazoló e-mailt a Azure NetApp Files csapattól a régiók közötti replikációs szolgáltatás használata előtt.

## <a name="supported-region-pairs"></a>Támogatott régió párok

A Azure NetApp Files kötet replikációja jelenleg a következő rögzített régió párokban érhető el:  

* USA nyugati régiója és USA keleti régiója
* USA 2. nyugati régiója és USA keleti régiója 
* USA déli középső régiója és USA középső régiója 
* USA déli középső régiója és USA keleti régiója
* USA déli középső régiója és USA 2. keleti régiója 
* USA 2. keleti régiója és az USA középső régiója 
* Észak-Európa és Nyugat-Európa
* Egyesült Királyság déli régiója és Egyesült Királyság nyugati régiója
* Kelet-Ausztrália és Délkelet-Ausztrália
* Közép-Kanada és Kelet-Kanada
* Kelet-japán és Nyugat-Japán
* Egyesült Királyság déli régiója és Középnyugat-Németország
* Délkelet-Ázsia és Kelet-Ausztrália
* Közép-India és Dél-India

## <a name="service-level-objectives"></a>Szolgáltatási szintű célkitűzések

A helyreállítási pontok célkitűzései (RPO) vagy a maximálisan elfogadható adatvesztés a replikálási ütemterv kétszerese.  A ténylegesen megfigyelt RPO az adatkészletek teljes méretétől, a változási aránytól, az adatátvitelek százalékos arányának és az átvitelhez elérhető replikációs sávszélességtől függően változhatnak.   

* A 10 perces replikálási ütemterv esetében a maximális RPO 20 perc.  
* Az óránkénti replikálási ütemterv esetében a maximális RPO két óra.  
* A napi replikálási ütemterv esetében a maximális RPO két nap.  

A helyreállítási időre vonatkozó célkitűzést (RTO) vagy a maximálisan tolerálható üzleti alkalmazások leállását az alkalmazás bevezetésének és a második helyen található adatokhoz való hozzáférésének tényezői határozzák meg. A RTO tárolási része, amellyel a rendszer megszakítja a társítási kapcsolatot a célként megadott kötet aktiválásához, valamint olvasási és írási adathozzáférést biztosít a második helyen, egy percen belül el kell végeznie.

## <a name="cost-model-for-cross-region-replication"></a>Cost Model a régiók közötti replikáláshoz  

Azure NetApp Files régiók közötti replikáció esetén csak a replikált adatmennyiségért kell fizetnie. Nincs beállítási díj vagy minimális használati díj. A replikációs díj a kezdeti replikálási konfiguráció során kiválasztott *cél* kötet replikációs gyakoriságán és régióján alapul. További információért tekintse meg a [Azure NetApp Files díjszabási](https://azure.microsoft.com/pricing/details/netapp/) oldalát.  

A normál Azure NetApp Files tárolási kapacitás díja a replikálási célként megadott kötetre (más néven *adatvédelmi* kötetre) vonatkozik. 

### <a name="pricing-examples"></a>Díjszabási példák

Az adott hónapban számlázott régiók közötti replikálási összeg az adott hónapban a régiók közötti replikálási szolgáltatáson keresztül replikált adatmennyiségen alapul. A replikált adatmennyiséget a GiB-ban mérjük. Ez a két régió között replikált adatok összegét jelöli a forrás köteteiről a cél kötetekre történő rendszeres replikálások során, valamint a cél kötetekről a forrás kötetekre irányuló összes újraszinkronizálási replikáció során.

#### <a name="example-1-month-1-baseline-replication-and-incremental-replications"></a>1. példa: 1. hónap alapkonfigurációs replikálás és növekményes replikáció

A következő helyzetekben feltételezzük:

* A *forrás* mennyisége a Azure NetApp Files *Premium* szolgáltatási szintjéből származik. A kötet kvótájának mérete 1000 GiB, a kötet mérete pedig 500 GiB a hónap első napjának elején. A kötet az *USA déli középső* régiójában található.
* A *cél* mennyisége a Azure NetApp Files *standard* szintű szolgáltatási szintből származik. Az *USA 2. keleti* régiójában található.
* A fenti két kötet között egy *óránkénti* , régiók közötti replikációt konfigurált. Ezért a replikálás díja $0,12/GiB.
* Az egyszerűség kedvéért tegyük fel, hogy a forrásoldali kötethez óránként állandó 0,5-GiB adatváltozás van, de az összes felhasznált mennyiség mérete nem nő (a 500 GiB-on marad). 

A kezdeti beállítás után az alapterv-replikáció azonnal megtörténik.  

* Az alapkonfiguráció replikálásakor replikált adatmennyiség: `500 GiB`
* Alapterv replikálási díjai: `500 GiB * $0.12 = $60`

Az alapterv replikálását követően a rendszer csak a megváltozott blokkokat replikálja. Ezért a következő növekményes replikálások óránként csak 0,5 GiB-t replikálnak.

* A növekményes replikációk között replikált adatmennyiség egy 30 napos hónapra: `0.5 GiB * 24 hours * 30 days = 360 GiB`
* Növekményes replikációs díjak: `360 GiB * $0.12 = $43.2`

Az 1. hónap végéig a teljes régiók közötti replikálási díj a következő:  

*  A régiók közötti replikálási díj összesen 1. hónap: `$60 + $43.2 = $103.2`

A normál Azure NetApp Files tárolási kapacitás díja a célként megadott kötetre vonatkozik. A cél kötet azonban olyan tárolási szintet is használhat, amely eltér a forrás mennyiségi szintjétől (és olcsóbb).

#### <a name="example-2-month-2-incremental-replications-and-resync-replications"></a>2. példa: a 2. hónap növekményes replikációja és a replikáció újraszinkronizálása  

Tegyük fel, hogy rendelkezik egy forrásoldali kötettel, egy célként megadott kötettel és egy, a két beállítás közötti replikációs kapcsolattal az 1. példában leírtak szerint. A második hónap 29 napja (egy 30 napos hónap) az óránkénti replikálás a várt módon történt.

* A növekményes replikáció során replikált adatmennyiség 29 napig: `0.5 GiB * 24 hours * 29 days = 348 GiB`

Tegyük fel, hogy a hónap utolsó napján nem tervezett leállás történt a forrásoldali régióban, és a feladatátvételt a cél kötetre. 2 óra elteltével a rendszer visszaállította a forrás régiót, és a cél kötetről a forrás kötetre újraszinkronizálta a replikálást. A 2 óra alatt 0,8 GiB adatváltozás történt a célként megadott köteten, és a forráshoz való Újraszinkronizálás szükséges.

* A rendszeres replikáció során replikált adatmennyiség összege az elmúlt nap 22 órájában: `0.5 GiB * 22 hours = 11 GiB`
* Az egyik újraszinkronizálási replikáció során replikált adatmennyiség: `0.8 GiB`

Ezért a 2. hónap végéig a teljes régiók közötti replikálási díj a következő:  

* A 2. hónap összes régiók közötti replikálási díja: `(348 GiB + 11 GiB + 0.8 GiB) * $0.12 = $43.18`

A 2. hónapra vonatkozó normál Azure NetApp Files tárolási kapacitás díja a cél kötetre vonatkozik.

## <a name="next-steps"></a>Következő lépések
* [A régiók közötti replikáció használatára vonatkozó követelmények és szempontok](cross-region-replication-requirements-considerations.md)
* [Replikációs társviszony létrehozása](cross-region-replication-create-peering.md)
* [Replikációs kapcsolat állapotának megjelenítése](cross-region-replication-display-health-status.md)
* [Vészhelyreállítás kezelése](cross-region-replication-manage-disaster-recovery.md)
* [Kötet replikációs metrikái](azure-netapp-files-metrics.md#replication)
* [Régiók közötti replikáció hibaelhárítása](troubleshoot-cross-region-replication.md)


