---
title: Regisztrációs adatbázis teljesítményproblémáinak elhárítása
description: A beállításjegyzék teljesítményével kapcsolatos gyakori problémák tünetei, okai és megoldása
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 15129ebe1da2e52fac106a34863f609c440549ad
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92148410"
---
# <a name="troubleshoot-registry-performance"></a>Regisztrációs adatbázis teljesítményproblémáinak elhárítása

Ez a cikk segítséget nyújt az Azure Container Registry teljesítményével kapcsolatban felmerülő problémák elhárításában. 

## <a name="symptoms"></a>Hibajelenségek

A következők közül egyet vagy többet is tartalmazhat:

* A Docker CLI-vel a vártnál hosszabb időt vesz igénybe, vagy leküldheti a képeket
* A lemezképek szolgáltatáshoz, például az Azure Kubernetes szolgáltatáshoz való telepítése a vártnál hosszabb időt vesz igénybe
* A várt időn belül nem hajtható végre nagy számú egyidejű lekéréses vagy leküldéses művelet.
* A földrajzilag replikált beállításjegyzék lekéréses vagy leküldéses műveletei a vártnál hosszabb időt vesznek igénybe, vagy hiba esetén a leküldéses művelet meghiúsul `Error writing blob``Error writing manifest`

## <a name="causes"></a>Okok

* A hálózati kapcsolatok sebessége lelassíthatja a beállításjegyzék műveleteit – [megoldás](#check-expected-network-speed)
* A képréteg tömörítése vagy kinyerése lassú lehet az ügyfél- [megoldásnál](#check-client-hardware)  
* Elérte a beállításjegyzék szolgáltatási rétegében vagy a környezeti [megoldásban](#review-configured-limits) beállított korlátot
* Földrajzilag replikált beállításjegyzékének replikái vannak a közeli régiókban – [megoldás](#configure-geo-replicated-registry)
* Egy földrajzilag távoli beállításjegyzék-replikából húz át – [megoldás](#configure-dns-for-geo-replicated-registry)

Ha itt nem oldja meg a problémát, a további beállításokért lásd: [speciális hibaelhárítás](#advanced-troubleshooting) és további [lépések](#next-steps) .

## <a name="potential-solutions"></a>Lehetséges megoldások

### <a name="check-expected-network-speed"></a>A várt hálózati sebesség ellenõrzése

Ellenőrizze az Internet feltöltési és letöltési sebességét, vagy használjon olyan eszközt, mint a AzureSpeed, hogy tesztelje az Azure Blob Storage-ban tárolt [feltöltést](https://www.azurespeed.com/Azure/Uploadß) és [letöltést](https://www.azurespeed.com/Azure/Download) , amely a beállításjegyzék rendszerképének rétegeit tárolja.

Győződjön meg arról, hogy a képméret a maximálisan támogatott mérettel és a beállításjegyzék szolgáltatási szintjéhez támogatott letöltési vagy feltöltési sávszélességgel rendelkezik. Ha a beállításjegyzék alapszintű vagy standard szinten van, érdemes lehet frissíteni a teljesítmény javítására. 

A más szolgáltatásokhoz való rendszerképek központi telepítéséhez keresse meg azokat a régiókat, amelyeken a beállításjegyzék és a cél található. A teljesítmény javítása érdekében érdemes megkeresni a beállításjegyzék és a telepítési cél megkeresését ugyanabban vagy a hálózatban lévő régiókban.

Kapcsolódó hivatkozások:

* [Azure Container Registry szolgáltatási szintek](container-registry-skus.md)    
* [Container Registry – gyakori kérdések](container-registry-faq.md)
* [Teljesítmény-és méretezhetőségi célok az Azure Blob Storage](../storage/blobs/scalability-targets.md)

### <a name="check-client-hardware"></a>Ügyfél hardverének keresése

A Docker-ügyfél lemezének típusa és PROCESSZORa befolyásolhatja a képrétegek kinyerésének vagy tömörítésének sebességét az ügyfélen a lekéréses vagy leküldéses műveletek részeként. Például a merevlemez-meghajtón a réteg kinyerése hosszabb időt vesz igénybe, mint egy SSD-lemez. Hasonlítsa össze a lekéréses műveleteket az Azure Container registryből származó hasonló rendszerképekhez és egy nyilvános beállításjegyzékhez, például a Docker hub-hoz.

### <a name="review-configured-limits"></a>Konfigurált korlátok áttekintése

Ha egyszerre több vagy több rétegben lévő lemezképet küld vagy húz a beállításjegyzékbe, tekintse át a beállításjegyzék szolgáltatási szintjének támogatott ReadOps és WriteOps korlátozásait. Ha a beállításjegyzék alapszintű vagy standard csomaggal rendelkezik, érdemes lehet frissíteni a határértékek növelésére. Tekintse meg a hálózati sávszélesség-szabályozással kapcsolatos tudnivalókat is, amelyek számos párhuzamos műveletnél előfordulhatnak. 

Tekintse át a Docker-démon konfigurációját az egyes leküldéses és lekéréses műveletekhez tartozó maximális egyidejű feltöltésekhez vagy letöltésekhez az ügyfélen. Szükség esetén állítsa be a magasabb korlátot.

Mivel minden egyes képréteghez külön beállításjegyzékbeli olvasási vagy írási művelet szükséges, tekintse meg a képek rétegeinek számát. Megfontolandó stratégiák a képrétegek számának csökkentése érdekében.

Kapcsolódó hivatkozások:

* [Azure Container Registry szolgáltatási szintek](container-registry-skus.md)
* [dockerd](https://docs.docker.com/engine/reference/commandline/dockerd/)

### <a name="configure-geo-replicated-registry"></a>Földrajzilag replikált beállításjegyzék konfigurálása

Előfordulhat, hogy egy olyan Docker-ügyfél, amely egy, a Geo-replikált beállításjegyzékbe küld egy rendszerképet, lehetséges, hogy nem küldi le az összes képréteget és annak jegyzékfájlját egyetlen replikált Ez azért fordulhat elő, mert az Azure Traffic Manager a beállításjegyzék-kérelmeket a hálózatra legközelebb lévő replikált beállításjegyzékbe irányítja. Ha a beállításjegyzék két közeli replikációs régióval rendelkezik, a képrétegek és a jegyzékfájl kiterjeszthető a két helyre, a leküldéses művelet pedig meghiúsul, ha a jegyzékfájl érvényesítve van.

Ha a DNS-feloldást a legközelebbi replikára szeretné optimalizálni, amikor képeket küld, állítson be egy földrajzilag replikált beállításjegyzéket ugyanabban az Azure-régióban, mint a leküldéses műveletek forrása, vagy a legközelebbi régió, amikor az Azure-on kívül dolgozik.

A földrajzilag replikált beállításjegyzékkel végzett műveletekkel kapcsolatos hibák megoldásához átmenetileg letilthatja Traffic Manager útválasztást egy vagy több replikálásra.

Kapcsolódó hivatkozások:

* [Geo-replikálás Azure Container Registry](container-registry-geo-replication.md)

### <a name="configure-dns-for-geo-replicated-registry"></a>A DNS konfigurálása a földrajzilag replikált beállításjegyzékhez

Ha a földrajzilag replikált beállításjegyzékből lekéréses műveletek lassúak, a DNS-konfiguráció az ügyfélen egy földrajzilag távoli DNS-kiszolgálóra is feloldható. Ebben az esetben előfordulhat, hogy a Traffic Manager a DNS-kiszolgálóhoz, de az ügyféltől távol lévő replikához is útválasztási kérelmek érkeznek. Futtassa `nslookup` `dig` a (z) vagy (Linux rendszeren) eszközt, hogy meghatározza azt a replikát, Traffic Manager a beállításjegyzék-kérelmeket átirányítja. Például:

```console
nslookup myregistry.azurecr.io
```

Lehetséges megoldás egy szorosabb DNS-kiszolgáló konfigurálása.

Kapcsolódó hivatkozások:

* [Geo-replikálás Azure Container Registry](container-registry-geo-replication.md)
* [Leküldéses műveletek hibakeresése földrajzilag replikált beállításjegyzékekkel](container-registry-geo-replication.md#troubleshoot-push-operations-with-geo-replicated-registries)
* [Útválasztás ideiglenes letiltása a replikáláshoz](container-registry-geo-replication.md#temporarily-disable-routing-to-replication)
* [Traffic Manager GYIK](../traffic-manager/traffic-manager-faqs.md)

### <a name="advanced-troubleshooting"></a>Speciális hibaelhárítás

Ha a beállításjegyzék erőforrásainak engedélyei lehetővé teszik, [ellenőrizze a beállításjegyzék-környezet állapotát](container-registry-check-health.md). Ha a rendszer hibákat jelez, tekintse át a lehetséges megoldásokra [mutató hibaüzenetet](container-registry-health-error-reference.md) .

Ha a beállításjegyzékben engedélyezve van az [erőforrás-naplók gyűjtése](container-registry-diagnostics-audit-logs.md) , tekintse át a ContainterRegistryRepositoryEvents-naplót. Ez a napló olyan műveletek adatait tárolja, mint például a leküldéses vagy a lekéréses események. A [tárház szintű műveleti hibák](container-registry-diagnostics-audit-logs.md#repository-level-operation-failures)naplójának lekérdezése. 

Kapcsolódó hivatkozások:

* [Naplók a diagnosztika kiértékeléséhez és a naplózáshoz](container-registry-diagnostics-audit-logs.md)
* [Container Registry – gyakori kérdések](container-registry-faq.md)
* [Az Azure Container Registry ajánlott eljárásai](container-registry-best-practices.md)

## <a name="next-steps"></a>Következő lépések

Ha itt nem oldja meg a problémát, tekintse meg a következő beállításokat.

* A beállításjegyzék egyéb hibaelhárítási témakörei a következők:
  * [Beállításjegyzékbeli bejelentkezés – problémamegoldás](container-registry-troubleshoot-login.md)
  * [A beállításjegyzék hálózati problémáinak elhárítása](container-registry-troubleshoot-access.md)
* [Közösségi támogatási](https://azure.microsoft.com/support/community/) lehetőségek
* [Microsoft Q&A](/answers/products/)
* [Támogatási jegy megnyitása](https://azure.microsoft.com/support/create-ticket/)