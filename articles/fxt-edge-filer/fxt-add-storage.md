---
title: 'Oktatóanyag: tár hozzáadása Azure FXT Edge Filer-fürthöz'
description: A háttérbeli tárolás és az ügyfélre vonatkozó pseudonamespace konfigurálása az Azure FXT Edge Filer-hoz
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.openlocfilehash: 7d10c6c1ce440b2ffe964dc78379ef3ab108e78e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92217524"
---
# <a name="tutorial-add-back-end-storage-and-configure-the-virtual-namespace"></a>Oktatóanyag: háttérbeli tároló hozzáadása és a virtuális névtér konfigurálása

Ez az oktatóanyag azt ismerteti, hogyan adhat hozzá háttérbeli tárhelyet a gyorsítótárhoz, és hogyan állíthatja be az ügyfél felé irányuló virtuális fájlrendszert.

A fürt csatlakozik a háttér-tárolási rendszerekhez az adatügyfelekre vonatkozó kérések eléréséhez, valamint a változások végleges tárolására a gyorsítótárban.

A névtér az ügyfélhez kapcsolódó pszeudo fájlrendszer, amely lehetővé teszi, hogy az ügyféloldali munkafolyamatok módosítása nélkül cserélje ki a háttérbeli tárolást.

Ebben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Háttérbeli tároló hozzáadása az Azure FXT Edge Filer-fürthöz
> * Az ügyfél felé irányuló elérési út definiálása a tároláshoz

## <a name="about-back-end-storage"></a>A háttérbeli tárterület ismertetése

Az Azure FXT Edge Filer-fürt egy *alapszintű Filer* -definíciót használ a háttérbeli tárolási rendszer a FXT-fürthöz való csatolásához.

Az Azure FXT Edge Filer számos népszerű NAS hardveres rendszerrel kompatibilis, és üres tárolókat használhat az Azure Blob vagy más felhőalapú tárolóból.

A Felhőbeli tárolók számára üresnek kell lennie, ha az FXT operációs rendszer teljes mértékben képes kezelni a felhőalapú tároló kötetén lévő összes adatmennyiséget. A meglévő adatait áthelyezheti a Felhőbeli tárolóba, miután a tárolót alapszintű Filer-ként hozzáadja a fürthöz.

A Vezérlőpult segítségével adjon hozzá egy Core Filer-t a rendszerhez.

> [!NOTE]
>
> Ha az Amazon AWS vagy a Google Cloud Storage szolgáltatást szeretné használni, telepítenie kell egy FlashCloud<sup>TM</sup> -szolgáltatás licencét. Forduljon a Microsoft képviselőjéhez egy licenckulcs esetében, majd kövesse az örökölt konfigurációs útmutató utasításait a [szolgáltatási licencek hozzáadásához vagy eltávolításához](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/install_licenses.html#install-licenses).
>
> Az Azure Blob Storage támogatását az Azure FXT Edge Filer szoftverlicenc tartalmazza.

Az alapszintű Filer-elemek hozzáadásával kapcsolatos részletesebb információkért olvassa el a következő témakört a fürtkonfiguráció útmutatójában:

* A Core Filer hozzáadásával és előkészítésével kapcsolatos további információkért olvassa el a következőt: [Working with Core filers](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/core_filer_overview.html#core-filer-overview).
* A részletes előfeltételek és lépésenkénti útmutatásért olvassa el a következő cikkeket:

  * [Új NAS Core Filer hozzáadása](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_nas.html#create-core-filer-nas)
  * [Új Cloud Core Filer hozzáadása](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html#create-core-filer-cloud)

Az alapszintű Filer hozzáadása után frissítheti a beállításait az alapszintű Filer részletek beállításai lapon.

## <a name="add-a-core-filer"></a>Alapvető Filer hozzáadása

Definiáljon egy alapvető Filer-t  az alapszintű **Filer**  >  **Manage Core filers** beállítások lap létrehozás gombjára kattintva.

![Kattintson a Create (létrehozás) gombra az alapszintű filers lista felett a Core filers kezelése lapon.](media/fxt-cluster-config/create-core-filer-button.png)

Az **új alapszintű Filer hozzáadása** varázsló végigvezeti a háttér-tárterületre mutató alapszintű Filer létrehozásának folyamatán. A fürtkonfiguráció útmutatója részletesen ismerteti a folyamatot, amely különbözik az NFS/NAS Storage és a Felhőbeli tárolás esetében (a hivatkozások a következők).

Az alfeladatok a következők:

* Adja meg a Core Filer (NAS vagy felhő) típusát

  ![A hardveres NAS új Core Filer varázsló első lapja. A "Cloud Core Filer" beállítás le van tiltva, és a hiányzó licenccel kapcsolatos hibaüzenet jelenik meg.](media/fxt-cluster-config/new-nas-1.png)

* Állítsa be a fő Filer nevét. Válasszon olyan nevet, amely segít a fürt rendszergazdái számára az általa képviselt tárolási rendszer megismerésében.

* A NAS Core filers esetében adja meg a teljes tartománynevet (FQDN) vagy IP-címet. A teljes TARTOMÁNYNEVEt minden alapvető Filer esetében ajánlott megadni, és az SMB-hozzáféréshez szükséges.

* Válassza ki a gyorsítótár-házirendet – a varázsló második lapja felsorolja az új Core Filer elérhető gyorsítótár-házirendjeit. Részletekért olvassa el a [fürt konfigurációs útmutatójának gyorsítótár-házirendek című szakaszát](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_manage_cache_policies.html).

  ![Egy hardveres NAS új Core Filer varázsló második lapja; Megnyílik a gyorsítótár-házirend legördülő menüje, amely több letiltott lehetőséget és három érvényes gyorsítótárazási házirend-beállítást (megkerülés, olvasási gyorsítótárazás és olvasási/írási gyorsítótárazás) mutat be.](media/fxt-cluster-config/new-nas-choose-cache-policy.png)

* A felhőalapú tároláshoz meg kell adnia a Cloud Service-t és a hozzáférési hitelesítő adatokat más paraméterek között. Részletekért olvassa el a [Cloud Service és a Protocol](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html#cloud-service-and-protocol) című témakört a fürt konfigurációs útmutatójában.

  ![A Cloud Core Filer információi az új Core Filer varázslóban](media/fxt-cluster-config/new-core-filer-cloud3.png)
  
  Ha a fürthöz már hozzáadta a Felhőbeli hozzáférési hitelesítő adatokat, azok megjelennek a listában. Frissítse és adja hozzá a hitelesítő adatokat a **fürt**  >  **Felhőbeli hitelesítő adatok** beállításai lapon.

Miután kitöltötte a varázsló összes szükséges beállítását, kattintson a **Filer hozzáadása** gombra a módosítás elküldéséhez.

Néhány pillanat elteltével a Storage rendszer megjelenik az **irányítópult** Core filers listán, és az alapvető Filer-beállítások oldalain keresztül érhető el.

![Core Filer "szélroham-NAS" a Core filers beállítások kezelése lapon, a kibontott Filer-részletek nézettel](media/fxt-cluster-config/core-filer-in-manage-page.png)

A képernyőképen található alapszintű Filer hiányzik egy VServer. Össze kell kapcsolni a Core Filer-t egy VServer, és létre kell hoznia egy csomópontot, hogy az ügyfelek hozzáférhessenek a tárolóhoz. Az alábbi lépéseket a [névtér konfigurálása](#configure-the-namespace)című témakör ismerteti.

## <a name="configure-the-namespace"></a>A névtér konfigurálása

Az Azure FXT Edge Filer-fürt létrehoz egy *fürtözött névtér* nevű virtuális fájlrendszert, amely leegyszerűsíti az ügyfelek hozzáférését a különböző háttérrendszer-rendszereken tárolt adatszolgáltatásokhoz. Mivel az ügyfelek virtuális elérési úttal kérik a fájlokat, a tárolási rendszerek hozzáadhatók vagy lecserélhetők anélkül, hogy módosítani kellene az ügyfél-munkafolyamatot.

A fürt névtere is lehetővé teszi, hogy a Felhőbeli és a NAS-alapú tárolási rendszereket egy hasonló adatstruktúrában is bemutassa.

A fürt vservers fenntartják a névteret, és tartalmat szolgálnak ki az ügyfeleknek. A fürt névterének létrehozása két lépésből áll:

1. VServer létrehozása
1. Csomópontok beállítása a háttérbeli tárolórendszer és az ügyfél felé irányuló fájlrendszer elérési útjai között

### <a name="create-a-vserver"></a>VServer létrehozása

A VServers olyan virtuális fájlkiszolgálók, amelyek azt szabályozzák, hogy az adatforgalom hogyan áramlik az ügyfél és a fürt alapvető Filer-fájljai között:

* VServers gazdagép-ügyfél felé irányuló IP-címek
* VServers hozza létre a névteret, és definiálja azokat a csomópontokat, amelyek leképezik az ügyfélre irányuló virtuális könyvtár struktúráját a háttérbeli tárolón való exportálásra
* A VServers szabályozza a fájl-hozzáférési vezérlőket, beleértve az alapszintű Filer exportálási házirendeket és a felhasználói hitelesítési rendszereket
* A VServers SMB-infrastruktúrát biztosít

A fürt VServer konfigurálásának megkezdése előtt olvassa el a csatolt dokumentációt, és forduljon a Microsoft képviselőjéhez a névtér-és vservers megismerése érdekében. VLAN-ok használata esetén [hozza létre őket](fxt-configure-network.md#adjust-network-settings) a VServer létrehozása előtt.

A fürtkonfiguráció-útmutató ezen részei segítséget nyújtanak a FXT VServer és a globális névtér-funkciók megismerésében:

* [VServers létrehozása és használata](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#creating-and-working-with-vservers)
* [Globális névtér használata](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html)
* [VServer létrehozása](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vserver_manage.html#creating-a-vserver)

Szüksége van legalább egy VServer a fürthöz.

Új VServer létrehozásához a következő információk szükségesek:

* A VServer beállítani kívánt név

* Az ügyfél felé irányuló IP-címek tartománya, amelyet a VServer fog kezelni

  A VServer létrehozásakor a folytonos IP-címek egyetlen tartományát kell megadnia. Később további címeket is hozzáadhat az **ügyfélhez kapcsolódó hálózati** beállítások oldal használatával.

* Ha a hálózata VLAN-okat tartalmaz, melyik VLAN-t használja ehhez a VServer

  >  Hozzon létre egy új VServer a VServer **kezelése VServers** beállítások lapján. Részletekért olvassa el a [VServer létrehozásával](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vserver_manage.html#creating-a-vserver) kapcsolatos témakört a fürt konfigurációs útmutatójában.

![új VServer létrehozásának előugró ablaka](media/fxt-cluster-config/new-vserver.png)

### <a name="create-a-junction"></a>Elágazás létrehozása

A *csomópont* egy háttérbeli tárolási útvonalat képez le az ügyfél által látható névtérhez.

Ezzel a rendszerrel leegyszerűsítheti az ügyfél csatlakoztatási pontjaiban használt elérési utat, és zökkenőmentesen méretezheti a kapacitást, mivel az egyik virtuális elérési út több alapszintű Filer tárolóból is képes tárolni.

![Új csatlakozási pont hozzáadása varázsló lapja a kitöltött beállításokkal](media/fxt-cluster-config/add-junction-full.png)

A névtér-összekapcsolás létrehozásával kapcsolatos részletes információkért tekintse meg a [ **VServer**  >  **névteret**](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_namespace.html) a fürtkonfiguráció útmutatójában.

![A VServer > névtér beállításai oldal, amely egy elágazás részleteit jeleníti meg](media/fxt-cluster-config/namespace-populated.png)

## <a name="configure-export-rules"></a>Exportálási szabályok konfigurálása

A VServer és a Core Filer együttes használata után testre kell szabnia az exportálási szabályokat és az exportálási házirendeket, amelyek azt szabályozzák, hogy az ügyfelek hogyan férhetnek hozzá a fájlokhoz az alapvető Filer-exportálásokban.

Először a VServer-   >  **exportálási szabályok** lapon új szabályokat adhat hozzá, módosíthatja az alapértelmezett házirendet, vagy létrehozhatja a saját egyéni exportálási szabályzatát.

Másodszor, a **VServer**  >  -**exportálási házirendek** lapon alkalmazza a testreszabott szabályzatot az alapvető Filer exportálására, amikor a VServer keresztül éri el.

A részletekért tekintse meg a következő témakört: az [alapvető Filer-exportálásokhoz való hozzáférés szabályozása](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/export_rules_overview.html) című cikk.

## <a name="next-steps"></a>Következő lépések

Miután hozzáadta a tárolót, és konfigurálta az ügyfélhez kapcsolódó névteret, fejezze be a fürt kezdeti telepítését: 

> [!div class="nextstepaction"]
> [A fürt hálózati beállításainak konfigurálása](fxt-configure-network.md)
