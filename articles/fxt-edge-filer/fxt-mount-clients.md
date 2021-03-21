---
title: Ügyfelek csatlakoztatása a Microsoft Azure FXT Edge Filer-fürtön
description: Az Azure FXT Edge Filer Hybrid Storage cache csatlakoztatása az NFS-ügyfélszámítógépek számára
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: rohogue
ms.openlocfilehash: fa1f9da2c60aaf4c552916d16c266e984bf08892
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92340512"
---
# <a name="tutorial-mount-the-cluster"></a>Oktatóanyag: a fürt csatlakoztatása

Ez az oktatóanyag bemutatja, hogyan csatlakoztathat NFS-ügyfeleket az Azure FXT Edge Filer-fürthöz. Az ügyfelek csatlakoztatják a virtuális névtér elérési útját, amelyet a háttérbeli tároló hozzáadásakor rendelt hozzá.

Ez az oktatóanyag a következőket tanítja:

> [!div class="checklist"]
>
> * Stratégiák a terheléselosztási ügyfelek számára az ügyfél felé irányuló IP-címek tartománya között
> * Csatlakozási útvonal létrehozása az ügyfélhez kapcsolódó IP-címről és névtér-csomópontból
> * A csatlakoztatási parancsban használandó argumentumok

Ez az oktatóanyag körülbelül 45 percet vesz igénybe.

## <a name="steps-to-mount-the-cluster"></a>A fürt csatlakoztatásának lépései

Kövesse az alábbi lépéseket az ügyfélszámítógépek Azure FXT Edge Filer-fürthöz való összekapcsolásához.

1. Döntse el, hogyan egyenlítheti ki az ügyfelek forgalmát a fürtcsomópontok között. A részletekért olvassa el az [Egyenleg-ügyfél terhelését](#balance-client-load)alább.
1. A fürt IP-címének és csatlakozási útvonalának azonosítása a csatlakoztatáshoz.
1. Határozza meg a csatlakoztatáshoz tartozó ügyféloldali elérési utat.
1. Adja ki a [csatlakoztatási parancsot](#use-recommended-mount-command-options)a megfelelő argumentumokkal.

## <a name="balance-client-load"></a>Ügyfél terhelésének elosztása

Az ügyfelek kéréseinek a fürt összes csomópontja közötti egyensúlyának biztosításához az ügyfeleket az ügyfél által elérhető IP-címek teljes tartományához kell csatlakoztatnia. A feladat automatizálása többféleképpen is elvégezhető.

A fürt ciklikus DNS-terheléselosztásának megismeréséhez olvassa el [a DNS konfigurálása az Azure FXT Edge FILER-fürthöz](fxt-configure-network.md#configure-dns-for-load-balancing)című témakört. Ennek a módszernek a használatához olyan DNS-kiszolgálót kell fenntartania, amely nem szerepel ezekben a cikkekben.

A kis telepítések egyszerűbb módszere, ha egy parancsfájl használatával rendeli hozzá az IP-címeket az egész tartományhoz az ügyfél csatlakoztatási ideje alatt.

A nagyméretű vagy bonyolult rendszerek esetében más terheléselosztási módszerek is alkalmazhatók. Forduljon a Microsoft képviselőjéhez, vagy nyisson meg egy [támogatási kérést](fxt-support-ticket.md) segítségért. (Azure Load Balancer jelenleg *nem támogatott* az Azure FXT Edge Filer-vel.)

## <a name="create-the-mount-command"></a>A csatlakoztatási parancs létrehozása

Az ügyféltől a ``mount`` parancs leképezi a virtuális kiszolgálót (VServer) az Azure FXT Edge Filer-fürtön a helyi fájlrendszer egyik elérési útjára.

A formátum ``mount <FXT cluster path> <local path> {options}``

A csatlakoztatási parancsnak három eleme van:

* fürt elérési útja – az alábbiakban ismertetett IP-cím és névtér-csatlakozási útvonal kombinációja
* helyi elérési út – az ügyfél elérési útja
* csatlakoztatási parancs beállításai – (az [ajánlott csatlakoztatási parancs használata lehetőséggel](#use-recommended-mount-command-options)szerepel)

### <a name="create-the-cluster-path"></a>A fürt elérési útjának létrehozása

A fürt elérési útja a VServer *IP-cím* és a *névtér-elágazás* elérési útjának kombinációja. A névtér-összekapcsolás egy virtuális elérési út, amelyet [a tárolási rendszer hozzáadásakor](fxt-add-storage.md#create-a-junction)adott meg.

Ha például ``/fxt/files`` a névtér elérési útjaként használta, az ügyfelek csatlakoztatni *IP_address*:/FXT/Files a helyi csatlakoztatási ponthoz.

!["Új elágazás hozzáadása" párbeszédpanel a/avere/Files a névtér elérési útja mezőben](media/fxt-mount/fxt-junction-example.png)

Az IP-cím a VServer definiált ügyféloldali IP-címek egyike. Az ügyféloldali IP-címek tartományát két helyen találja a fürt Vezérlőpultján:

* **VServers** -tábla (irányítópult lap) –

  ![A Vezérlőpult irányítópult lapja, amely a gráf alatti adattábla VServer lapját jelöli, és az IP-cím szakasz a kör alakú](media/fxt-mount/fxt-ip-addresses-dashboard.png)

* **Ügyféloldali hálózati** beállítások lap –

  ![Beállítások > VServer > ügyfél hálózati konfigurációjának lapja, amely egy adott VServer tartozó táblázat címtartomány szakaszának körét tartalmazza](media/fxt-mount/fxt-ip-addresses-settings.png)

Egyesítse az IP-címet és a névtér elérési útját, hogy a fürt elérési útja a csatlakoztatási parancshoz legyen létrehozva.

Példa az ügyfél csatlakoztatására szolgáló parancsra: ``mount 10.0.0.12:/sd-access /mnt/fxt {options}``

### <a name="create-the-local-path"></a>A helyi elérési út létrehozása

A csatlakoztatási parancs helyi elérési útja. Megadhatja a virtuális névtér részeként használni kívánt elérésiút-struktúrát. Tervezze meg az ügyfél-munkafolyamathoz megfelelő névteret és helyi elérési utat.

További információ az ügyfél felé irányuló névtérről: a fürtkonfiguráció [áttekintése](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html).

Az elérési utakon kívül az egyes ügyfelek csatlakoztatásakor az alább ismertetett [csatlakoztatási parancsot](#use-recommended-mount-command-options) is adja meg.

### <a name="use-recommended-mount-command-options"></a>Ajánlott csatlakoztatási parancsok használata

A zökkenőmentes ügyfél-csatlakoztatás biztosításához adja át ezeket a beállításokat és argumentumokat a csatlakoztatási parancsban:

``mount -o hard,nointr,proto=tcp,mountproto=tcp,retry=30 ${VSERVER_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``

| Szükséges beállítások | Description |
--- | ---
``hard`` | Az Azure FXT Edge Filer-fürthöz való Soft mounts az alkalmazás hibáival és az esetleges adatvesztéssel van társítva.
``proto=netid`` | Ez a beállítás támogatja az NFS-hálózati hibák megfelelő kezelését.
``mountproto=netid`` | Ez a beállítás támogatja a hálózati hibák megfelelő kezelését a csatlakoztatási műveletekhez.
``retry=n`` | Állítsa be ``retry=30`` az átmeneti csatlakoztatási hibák elkerülését. (Az előtér-csatlakoztatásokban egy másik érték használata javasolt.)

| Előnyben részesített beállítások  | Description |
--- | ---
``nointr``            | Ha az ügyfelek az ezt a lehetőséget támogató régebbi operációsrendszer-kerneleket (mielőtt április 2008) használják, használja azt. A "intr" beállítás az alapértelmezett.

## <a name="next-steps"></a>Következő lépések

Az ügyfelek csatlakoztatása után tesztelheti a munkafolyamatot, és megkezdheti a fürt megkezdését.

Ha át kell helyeznie az adatot egy új Cloud Core Filer-ba, használja ki a gyorsítótárazási struktúrát a párhuzamos adatfeldolgozás használatával. Egyes stratégiákat a [vFXT-fürtbe való áthelyezéssel kapcsolatos](../avere-vfxt/avere-vfxt-data-ingest.md)témakörben talál. (A avere vFXT for Azure egy felhőalapú termék, amely az Azure FXT Edge Filer-hez hasonló gyorsítótárazási technológiát használ.)

Ha hardveres problémát kell elhárítani, olvassa el az [Azure FXT Edge-beli hardveres állapotának figyelése](fxt-monitor.md) című cikkét.