---
title: Azure Service Fabric rendszerkép-tárolóhoz tartozó kapcsolatok karakterlánca
description: Ismerje meg a rendszerkép-tároló kapcsolati karakterláncát, beleértve annak használatát és alkalmazásait egy Service Fabric-fürthöz.
author: alexwun
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: alexwun
ms.openlocfilehash: 8fc0239dd18fc7071823a129a7dbc4f102023d66
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "86246197"
---
# <a name="understand-the-imagestoreconnectionstring-setting"></a>Az ImageStoreConnectionString beállítás ismertetése

Néhány dokumentációban röviden megemlítjük egy "ImageStoreConnectionString" paraméter létezését anélkül, hogy leírja, hogy mit jelent valójában. Ha pedig egy olyan cikket használ, mint például az [alkalmazások telepítése és eltávolítása a PowerShell használatával][10], úgy tűnik, hogy minden, ami a cél fürt fürt jegyzékfájljában látható módon másolja vagy beilleszti az értéket. Így a beállításnak fürtön kell lennie, de ha a [Azure Portalon][11]keresztül hoz létre fürtöt, nincs lehetőség a beállítás konfigurálására, és mindig "Fabric: lemezképtárolóba". Mi a célja ennek a beállításnak?

![Fürt jegyzékfájlja][img_cm]

A Service Fabric számos különböző csapat számára indult el platformként a belső Microsoft-fogyasztáshoz, így az informatikai részleg néhány aspektusa rendkívül testreszabható – a "lemezképtároló" az egyik ilyen aspektus. Lényegében a lemezképtároló egy csatlakoztatható adattár az alkalmazáscsomag tárolásához. Ha az alkalmazás a fürt egyik csomópontjára van telepítve, a csomópont letölti az alkalmazáscsomag tartalmát a lemezképtároló. A ImageStoreConnectionString olyan beállítás, amely tartalmazza az ügyfelek és a csomópontok összes szükséges adatát, hogy megkeresse az adott fürthöz tartozó megfelelő lemezképtároló.

Jelenleg háromféle lemezképtároló-szolgáltató létezik, és a hozzájuk tartozó kapcsolatok sztringje a következő:

1. Lemezképtároló szolgáltatás: "Fabric: Lemezképtárolóba"

2. Fájlrendszer: "fájl: [fájlrendszer elérési útja]"

3. Azure Storage: "xstore: DefaultEndpointsProtocol = https; AccountName = [...]; AccountKey = [...]; Container = [...] "

Az éles üzemben használt szolgáltató típusa a lemezképtároló szolgáltatás, amely egy állapot-nyilvántartó megőrzött rendszerszolgáltatás, amely Service Fabric Explorerból látható. 

![lemezképtároló szolgáltatás][img_is]

A lemezképtároló a fürtön belüli rendszerszolgáltatásban való üzemeltetése kiküszöböli a csomagok tárházának külső függőségeit, és nagyobb mértékben szabályozza a tárterületet. A lemezképtároló jövőbeli fejlesztései valószínűleg először a lemezképtároló-szolgáltatót célozzák meg, ha nem kizárólag. A lemezképtároló szolgáltató kapcsolati karakterlánca nem rendelkezik egyedi információval, mivel az ügyfél már csatlakoztatva van a célként megadott fürthöz. Az ügyfélnek csak azt kell tudnia, hogy a rendszerszolgáltatást célzó protokollokat kell használni.

A rendszer a lemezképtároló szolgáltatás helyett a fájlrendszer-szolgáltatót használja a helyi egyablakos fürtökhöz a fejlesztés során, hogy a fürt valamivel gyorsabban beindítsa a fürtöt. A különbség általában kicsi, de a fejlesztés során a legtöbb ember számára hasznos optimalizálás. A másik tárolási szolgáltatói típussal rendelkező helyi egyablakos fürtök is üzembe helyezhetők, de ez általában nem indokolja, mert a fejlesztés/tesztelés munkafolyamat a szolgáltatótól függetlenül változatlan marad. Az Azure Storage-szolgáltató csak a lemezképtároló szolgáltató bevezetése előtt üzembe helyezett régi fürtök korábbi támogatásához létezik.

Továbbá, nem a fájlrendszer-szolgáltatót, sem az Azure Storage-szolgáltatót több fürt közötti lemezképtároló megosztására kell használni – ez a fürt konfigurációs információinak sérülését eredményezi, mivel az egyes fürtök ütköző adatmennyiséget írhatnak a lemezképtároló. Ha több fürt között szeretné megosztani a kiépített alkalmazáscsomag-csomagokat, használja helyette a [sfpkg][12] -fájlokat, amelyek a letöltési URI-val bármely külső tárolóba feltölthetők.

Így amíg a ImageStoreConnectionString konfigurálható, csak az alapértelmezett beállítást használja. Az Azure-ban a Visual studión keresztül történő közzétételkor a paraméter automatikusan be lesz állítva. Az Azure-ban üzemeltetett fürtök programozott telepítéséhez a kapcsolódási sztring mindig "Fabric: Lemezképtárolóba". Habár kétségei vannak, az értéke mindig ellenőrizhető a fürt jegyzékfájljának a [PowerShell](/powershell/module/servicefabric/get-servicefabricclustermanifest), a [.net](/previous-versions/azure/reference/mt161375(v=azure.100))vagy a [Rest](/rest/api/servicefabric/get-a-cluster-manifest)használatával történő beolvasásával. A helyszíni teszt-és üzemi fürtöket mindig úgy kell konfigurálni, hogy az lemezképtároló szolgáltatót is használják.

### <a name="next-steps"></a>Következő lépések
[Alkalmazások telepítése és eltávolítása a PowerShell használatával][10]

<!--Image references-->
[img_is]: ./media/service-fabric-image-store-connection-string/image_store_service.png
[img_cm]: ./media/service-fabric-image-store-connection-string/cluster_manifest.png

[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-cluster-creation-via-portal.md
[12]: service-fabric-package-apps.md#create-an-sfpkg
