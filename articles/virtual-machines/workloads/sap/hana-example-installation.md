---
title: A HANA telepítése SAP HANAon az Azure-ban (nagyméretű példányok) | Microsoft Docs
description: A HANA telepítése SAP HANAon az Azure-ban (nagyméretű példányok).
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: hermannd
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 59ea41254c16f32f19a67e7811177a4667a85e7d
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101666704"
---
# <a name="install-hana-on-sap-hana-on-azure-large-instances"></a>A HANA telepítése SAP HANAon az Azure-ban (nagyméretű példányok)

Ha a HANA-t SAP HANA szeretné telepíteni az Azure-ban (nagyméretű példányok), először a következőket kell tennie:
- A Microsoft számára minden olyan adattal megadható, amelyet egy SAP HANA nagyméretű példányon kíván üzembe helyezni.
- A Microsoft SAP HANA nagyméretű példányát kapja meg.
- Létre kell hoznia egy Azure-beli virtuális hálózatot, amely a helyszíni hálózathoz csatlakozik.
- A HANA nagyméretű példányok ExpressRoute áramkörét ugyanahhoz az Azure-beli virtuális hálózathoz kell kapcsolni.
- Olyan Azure-beli virtuális gépet telepít, amelyet a HANA nagyméretű példányaihoz tartozó Jump boxként használ.
- Győződjön meg arról, hogy a Jump Box-ból a nagyméretű HANA-példány egységéhez tud csatlakozni, és fordítva.
- Győződjön meg arról, hogy az összes szükséges csomag és javítás telepítve van-e.
- Olvassa el az SAP-megjegyzéseket és a HANA-telepítésre vonatkozó dokumentációt az Ön által használt operációs rendszeren. Győződjön meg arról, hogy az operációs rendszer kiadásában a választott HANA-kiadás támogatott.

A következő szakasz egy példát mutat be a HANA telepítési csomagjainak a Jump Box virtuális gépre való letöltésére. Ebben az esetben az operációs rendszer a Windows.

## <a name="download-the-sap-hana-installation-bits"></a>A SAP HANA telepítési BITS letöltése
A HANA nagyméretű példányok egysége nem kapcsolódik közvetlenül az internethez. A telepítési csomagokat nem lehet közvetlenül az SAP-ból a HANA nagyméretű példány virtuális gépre letölteni. Ehelyett letölti a csomagokat a Jump Box virtuális gépre.

Szüksége van egy SAP S-User vagy más felhasználóra, amely lehetővé teszi az SAP piactér elérését.

1. Jelentkezzen be, és lépjen az [SAP Service Marketplace](https://support.sap.com/en/index.html)webhelyre. Válassza a **Szoftvertelepítés letöltése**  >  **és**  >  **a frissítés ABC-index alapján** lehetőséget. Ezután válassza **a H – SAP HANA platform kiadása**  >  **SAP HANA platform Edition 2,0**-  >  **telepítés** lehetőséget. Töltse le az alábbi képernyőképen látható fájlokat.

   ![Képernyőkép a letölteni kívánt fájlokról](./media/hana-installation/image16_download_hana.PNG)

2. Ebben a példában a SAP HANA 2,0 telepítési csomagokat töltöttük le. Az Azure Jump Box virtuális gépen bontsa ki az önkicsomagoló archívumokat a könyvtárba az alábbi ábrán látható módon.

   ![Képernyőkép az önkicsomagoló archívumról](./media/hana-installation/image17_extract_hana.PNG)

3. Az archívumok kibontásakor másolja a kinyeréssel létrehozott könyvtárat (ebben az esetben 51052030). Másolja a könyvtárat a HANA Large instance Unit/Hana/Shared kötetből egy létrehozott könyvtárba.

   > [!Important]
   > Ne másolja a telepítési csomagokat a gyökér vagy a rendszerindító logikai egységbe, mert a terület korlátozott, és más folyamatok is használni szeretnék.


## <a name="install-sap-hana-on-the-hana-large-instance-unit"></a>SAP HANA telepítése a HANA nagyméretű példány-egységen
SAP HANA telepítéséhez jelentkezzen be felhasználói gyökérként. A SAP HANA telepítéséhez csak a legfelső szintű engedélyek szükségesek. Engedélyek beállítása a/Hana/Shared. átmásolt könyvtárra

```
chmod –R 744 <Installation bits folder>
```

Ha a grafikus felhasználói felület beállításával szeretné SAP HANA telepíteni, a GTK2 csomagot a HANA Large-példányokra kell telepíteni. A következő parancs futtatásával ellenőrizhető, hogy telepítve van-e:

```
rpm –qa | grep gtk2
```

(A későbbi lépésekben a SAP HANA a telepítőt a grafikus felhasználói felülettel jelenítjük meg.)

Lépjen be a telepítési könyvtárba, és navigáljon a HDB_LCM_LINUX_X86_64 alkönyvtárba. 

A könyvtárból indítsa el a következőket:

```
./hdblcmgui 
```
Ezen a ponton olyan képernyőkép-sorozatot végez, amelyben megadja a telepítéshez szükséges adatmennyiséget. Ebben a példában a SAP HANA adatbázis-kiszolgálót és a SAP HANA ügyfél-összetevőket telepíti. Ezért a kiválasztás **SAP HANA adatbázis**.

![Képernyőfelvétel SAP HANA életciklus-kezelési képernyőről, SAP HANA-adatbázis kiválasztásával](./media/hana-installation/image18_hana_selection.PNG)

A következő képernyőn válassza az **új rendszer telepítése** lehetőséget.

![Képernyőfelvétel SAP HANA életciklus-kezelési képernyőről, az új rendszerek telepítése beállítással](./media/hana-installation/image19_select_new.PNG)

Ezután válasszon több további telepítendő összetevő közül.

![Képernyőkép a SAP HANA életciklus-kezelési képernyőről, a további összetevők listájával](./media/hana-installation/image20_select_components.PNG)

Itt választjuk ki a SAP HANA ügyfelet és a SAP HANA studiót. Egy Felskálázási példányt is telepítünk. Ezután válassza az **Egygazda rendszer** lehetőséget. 

![Képernyőkép a SAP HANA életciklus-kezelési képernyőről, amelyen egyetlen gazda rendszer van kiválasztva](./media/hana-installation/image21_single_host.PNG)

A következő lépésben adjon meg néhány adattal.

![Képernyőkép a SAP HANA életciklus-kezelési képernyőről, amely meghatározza a Rendszertulajdonságok mezőit](./media/hana-installation/image22_provide_sid.PNG)

> [!Important]
> A HANA rendszerazonosítóként (SID) meg kell adnia ugyanazt a biztonsági azonosítót, amelyet a Microsofthoz adott meg, amikor a HANA nagyméretű példány üzembe helyezését rendelte. Ha egy másik SID-t választ, a telepítés sikertelen lesz, mert a különböző köteteken hozzáférési jogosultsági problémák léptek fel.

A telepítési útvonalhoz használja a/Hana/Shared könyvtárat. A következő lépésben megadja a HANA-adatfájlok és a HANA-naplófájlok helyét.


![Képernyőkép a SAP HANA életciklus-kezelési képernyőről, az adat-és a naplózási terület mezővel](./media/hana-installation/image23_provide_log.PNG)

> [!Note]
> A Rendszertulajdonságok definiálása során megadott SID (két képernyő ago) meg kell egyeznie a csatlakoztatási pontok SID-azonosítójával. Ha eltérés van, lépjen vissza, és állítsa be a SID-t a csatlakoztatási pontokon lévő értékre.

A következő lépésben tekintse át az állomásnév nevét, és végül javítsa ki. 

![Képernyőkép a SAP HANA életciklus-kezelési képernyőről, a gazdagép nevével](./media/hana-installation/image24_review_host_name.PNG)

A következő lépésben le kell kérnie a Microsoft számára megadott, a HANA nagyméretű példány központi telepítésének elvégzése során kapott adatmennyiséget is. 

![Képernyőfelvétel a SAP HANA életciklus-felügyeletről](./media/hana-installation/image25_provide_guid.PNG)

> [!Important]
> Az egység központi telepítésének megrendeléséhez adja meg ugyanazt a rendszergazda **felhasználói azonosítót** és azonosítót a Microsoft számára megadott **felhasználói csoport** számára. Ellenkező esetben a SAP HANA telepítése nem sikerül a HANA nagyméretű példány-egységen.

A következő két képernyő nem jelenik meg itt. Lehetővé teszik a SAP HANA adatbázis rendszerfelhasználójának, valamint a sapadm-felhasználó jelszavának megadását. Az utóbbit a SAP HANA adatbázis-példányának részeként telepítendő SAP-gazdagép ügynöke használja.

A jelszó meghatározása után egy megerősítő képernyő jelenik meg. Győződjön meg a felsorolt összes adattal, és folytassa a telepítést. Elér egy folyamatjelző képernyőt, amely dokumentálja a telepítési folyamatot, például a következőt:

![Képernyőkép a SAP HANA életciklus-kezelési képernyőről, a telepítési folyamat indikátorai](./media/hana-installation/image27_show_progress.PNG)

A telepítés befejezésekor a következőhöz hasonló képernyőnek kell megjelennie:

![Képernyőkép a SAP HANA életciklus-kezelési képernyőről, amely a telepítés befejezését jelzi](./media/hana-installation/image28_install_finished.PNG)

A SAP HANA példánynak most már működnie kell, és használatra készen kell állnia. Csatlakoznia kell hozzá SAP HANA studióból. Győződjön meg arról is, hogy megkeresi és alkalmazza a legújabb frissítéseket.


## <a name="next-steps"></a>Következő lépések

- [Magas rendelkezésre állású és vész-helyreállítási SAP HANA Large Instances az Azure-ban](hana-overview-high-availability-disaster-recovery.md)

