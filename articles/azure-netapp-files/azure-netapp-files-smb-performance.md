---
title: A Azure NetApp Files SMB-teljesítményével kapcsolatos gyakori kérdések | Microsoft Docs
description: Válaszok a Azure NetApp Files SMB-teljesítményével kapcsolatos gyakori kérdésekre.
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
ms.date: 09/30/2020
ms.author: b-juche
ms.openlocfilehash: 9a07c6ae48cdca68a95db7770d90076eb8f10f95
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91929456"
---
# <a name="faqs-about-smb-performance-for-azure-netapp-files"></a>A Azure NetApp Files SMB-teljesítményével kapcsolatos gyakori kérdések

Ez a cikk a Azure NetApp Files SMB-teljesítményre vonatkozó ajánlott eljárásaival kapcsolatos gyakori kérdéseket (GYIK) válaszol.

## <a name="is-smb-multichannel-enabled-in-smb-shares"></a>Engedélyezve van-e a többcsatornás SMB az SMB-megosztásokban? 

Igen, a többcsatornás SMB alapértelmezés szerint engedélyezve van, a módosítás a 2020 január elején történik. A meglévő SMB-kötetek előtti összes SMB-megosztás engedélyezve lett, és az újonnan létrehozott kötetek a létrehozáskor is engedélyezve lesznek a szolgáltatással. 

A többcsatornás SMB funkció használatának kihasználása érdekében a funkció engedélyezése előtt létrehozott SMB-kapcsolatokat vissza kell állítani. A visszaállításhoz leválaszthatja és újracsatlakoztathatja az SMB-megosztást.

## <a name="is-rss-supported"></a>Támogatott az RSS?

Igen, Azure NetApp Files támogatja a fogadó oldali skálázást (RSS).

Ha engedélyezve van a többcsatornás SMB, a SMB3-ügyfél több TCP-kapcsolatot létesít a Azure NetApp Files SMB-kiszolgálóval egy olyan hálózati adapteren (NIC) keresztül, amely egyetlen RSS-kompatibilis. 

## <a name="which-windows-versions-support-smb-multichannel"></a>Mely Windows-verziók támogatják a többcsatornás SMB-t?

A Windows támogatja a többcsatornás SMB használatát, mivel a Windows 2012 lehetővé teszi a legjobb teljesítmény elérését.  A részletekért lásd: [többcsatornás SMB telepítése](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/dn610980(v%3Dws.11)) és [a többcsatornás SMB alapjai](/archive/blogs/josebda/the-basics-of-smb-multichannel-a-feature-of-windows-server-2012-and-smb-3-0) . 


## <a name="does-my-azure-virtual-machine-support-rss"></a>Támogatja az Azure-beli virtuális gépek az RSS-t?

Ha szeretné megtudni, hogy az Azure-beli virtuális gépek hálózati adapterei támogatják-e az RSS-t, futtassa az alábbi parancsot, `Get-SmbClientNetworkInterface` és ellenőrizze a mezőt `RSS Capable` : 

![Képernyőkép, amely az Azure virtuális gép RSS-kimenetét jeleníti meg.](../media/azure-netapp-files/azure-netapp-files-formance-rss-support.png)

## <a name="does-azure-netapp-files-support-smb-direct"></a>Azure NetApp Files támogatja az SMB Direct szolgáltatást?

Nem, Azure NetApp Files nem támogatja a közvetlen SMB-t. 

## <a name="what-is-the-benefit-of-smb-multichannel"></a>Milyen előnyökkel jár a többcsatornás SMB? 

A többcsatornás SMB szolgáltatás lehetővé teszi, hogy az SMB3-ügyfél kapcsolatok készletet hozzon létre egyetlen hálózati kártyán (NIC) vagy több hálózati adapteren keresztül, valamint hogy egyetlen SMB-munkamenetre vonatkozó kérelmeket küldjön. Ezzel szemben a tervezés, a SMB1 és a SMB2 megköveteli, hogy az ügyfél egy kapcsolatot hozzon létre, és az adott munkamenethez tartozó összes SMB-forgalmat elküldje a kapcsolaton keresztül. Ez az egyetlen kapcsolat korlátozza a protokollok teljes teljesítményét, amely egyetlen ügyfélről is elérhető.

## <a name="should-i-configure-multiple-nics-on-my-client-for-smb"></a>Több hálózati adaptert konfigurálok az SMB-ügyfélhez?

Nem. Az SMB-ügyfél megfelel az SMB-kiszolgáló által visszaadott hálózati adapterek számának.  Minden tárolási kötet egy és csak egy tárolási végpontból érhető el.  Ez azt jelenti, hogy a rendszer csak egy hálózati adaptert használ az adott SMB-kapcsolathoz.  

Az `Get-SmbClientNetworkInterace` alábbi kimenet azt mutatja, hogy a virtuális gépnek 2 hálózati adaptere van – 15 és 12.  Ahogy az az alábbi parancsban `Get-SmbMultichannelConnection` is látható, még ha két RSS-kompatibilis hálózati adapter van, akkor a rendszer csak a 12-es felületet használja az SMB-megosztással kapcsolatban; a 15-ös illesztőfelület nincs használatban.

![Az RSS-kompatibilis hálózati adapterek kimenetét bemutató képernyőképen.](../media/azure-netapp-files/azure-netapp-files-rss-capable-nics.png)

## <a name="is-nic-teaming-supported-in-azure"></a>Támogatott a hálózati adapterek összevonása az Azure-ban?

A hálózati adapterek összevonása az Azure-ban nem támogatott. Bár az Azure Virtual Machines szolgáltatásban több hálózati adapter is támogatott, a fizikai szerkezet helyett logikai értéknek kell lennie. Ezért nem biztosítanak hibatűrést.  Emellett az Azure-beli virtuális gépek számára elérhető sávszélesség a gép számára is kiszámítható, nem pedig egyedi hálózati adapterre.

## <a name="whats-the-performance-like-for-smb-multichannel"></a>Mi a teljesítmény, mint a többcsatornás SMB?

Az alábbi tesztek és grafikonok a többcsatornás SMB hatékonyságát mutatják be az Egypéldányos munkaterhelések esetében.

### <a name="random-io"></a>Véletlenszerű I/O  

Ha a többcsatornás SMB le van tiltva az ügyfélen, a Pure 4 KiB olvasási és írási tesztek a FIO és egy 40 GiB munkakészlettel lettek elvégezve.  Az SMB-megosztást leválasztották az egyes tesztek között, az SMB-ügyfélkapcsolatok számának és az RSS hálózati adapter beállításainál (,,, `1` `4` `8` `16` `set-SmbClientConfiguration -ConnectionCountPerRSSNetworkInterface <count>` ). A tesztek azt mutatják, hogy az alapértelmezett beállítás elegendő az I/O-igényes számítási feladatokhoz, és ez a `4` növekményes `8` `16` hatás. 

A parancs azt `netstat -na | findstr 445` igazolta, hogy további kapcsolatok jöttek-e a-ig és a értékre `1` `4` `8` `16` .  Minden teszt során négy CPU-magot teljes mértékben kihasználtak az SMB számára, ahogy azt a perfmon- `Per Processor Network Activity Cycles` statisztika megerősítette (ez a cikk nem tartalmazza.)

![A többcsatornás SMB-t véletlenszerű I/O-összehasonlítást bemutató diagram.](../media/azure-netapp-files/azure-netapp-files-random-io-tests.png)

Az Azure-beli virtuális gép nem befolyásolja az SMB-(vagy NFS-) tárolási I/O-korlátozásokat.  Ahogy az a következő ábrán is látható, a D32ds-példány típusa korlátozott 308 000 a gyorsítótárazott tárolás IOPS és 51 200 a nem gyorsítótárazott tárolási IOPS esetében.  A fenti gráf azonban jelentősen több I/O-t mutat az SMB protokollon keresztül.

![A véletlenszerű I/O-összehasonlítási tesztet bemutató diagram.](../media/azure-netapp-files/azure-netapp-files-random-io-tests-list.png)

### <a name="sequential-io"></a>Szekvenciális IO 

A korábban ismertetett véletlenszerű I/O-tesztekhez hasonló tesztek végrehajtása a 64-KiB szekvenciális I/O-vel történt. Bár az ügyfél-kapcsolati kapcsolatok száma az RSS-hálózati adapteren (a 4. után) nem volt észrevehető hatással a véletlenszerű I/O-műveletekre, ugyanez nem vonatkozik a szekvenciális I/O-ra. Ahogy az alábbi ábrán is látható, az egyes növekmények az olvasási sebesség megfelelő növekedésével vannak társítva. Az írási átviteli sebesség az Azure által az egyes példányok típusának és méretének megfelelő hálózati sávszélességre vonatkozó korlátozások miatt megmaradt. 

![Az átviteli sebesség tesztelésének összehasonlítását bemutató diagram.](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests.png)

Az Azure minden egyes virtuális gép típusára/méretére korlátozza a hálózati díjszabást. A ráta korlátja csak a kimenő forgalomra érvényes. A virtuális gépen lévő hálózati adapterek száma nem befolyásolja a számítógép számára elérhető sávszélesség teljes mennyiségét.  Az D32ds-példány típusa például egy 16 000 Mbps (2 000 MiB/s) hálózati korláttal rendelkezik.  Ahogy a fenti szekvenciális gráf mutatja, a korlát befolyásolja a kimenő forgalmat (írásokat), de a többcsatornás olvasásokat nem.

![Szekvenciális I/O összehasonlító tesztet bemutató diagram.](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests-list.png)

## <a name="what-performance-is-expected-with-a-single-instance-with-a-1-tb-dataset"></a>Milyen teljesítmény várható egy 1 TB-os adatkészlettel rendelkező egyetlen példánnyal?

Annak érdekében, hogy részletesebb képet kapjon a számítási feladatokhoz az olvasási/írási mixek használatával, az alábbi két diagram egyetlen, ultra szintű, 50 TB-os Felhőbeli kötet teljesítményét jeleníti meg 1 TB-os adatkészlettel és a 4. többcsatornás SMB-vel. A rendszer optimális IODepth használ, és rugalmas IO-(FIO-) paramétereket használ a hálózati sávszélesség () teljes használatának biztosításához `numjobs=16` .

A következő diagramon a 4k véletlenszerű I/O-műveletek eredményei láthatók, egyetlen virtuálisgép-példánnyal és olvasási/írási aránysal 10%-os időközökkel:

![A Windows 2019 standard _D32ds_v4 4K véletlenszerű i/o-tesztet bemutató diagram.](../media/azure-netapp-files/smb-performance-standard-4k-random-io.png)

A következő diagramon a szekvenciális I/O eredményei láthatók:

![A Windows 2019 standard _D32ds_v4 64 KB-os adatátviteli sebességet bemutató diagram.](../media/azure-netapp-files/smb-performance-standard-64k-throughput.png)

## <a name="what-performance-is-expected-when-scaling-out-using-5-vms-with-a-1-tb-dataset"></a>Milyen teljesítmény várható az 5 virtuális gép 1 TB-os adatkészlettel való horizontális felskálázásakor?

Ezek a tesztek 5 virtuális géppel ugyanazt a tesztelési környezetet használják, mint az egyetlen virtuális gép, és minden folyamat saját fájlra ír.

A következő diagramon a véletlenszerű I/O-műveletek eredményei láthatók:

![Diagram, amely a Windows 2019 standard _D32ds_v4 4K 5 példányú randio IO-tesztet jeleníti meg.](../media/azure-netapp-files/smb-performance-standard-4k-random-io-5-instances.png)

A következő diagramon a szekvenciális I/O eredményei láthatók:

![Diagram, amely a Windows 2019 standard _D32ds_v4 64 bites, 5 példányú szekvenciális átviteli sebességét jeleníti meg.](../media/azure-netapp-files/smb-performance-standard-64k-throughput-5-instances.png)

## <a name="how-do-you-monitor-hyper-v-ethernet-adapters-and-ensure-that-you-maximize-network-capacity"></a>Hogyan figyelheti a Hyper-V Ethernet-adaptereket, és biztosítja, hogy maximalizálja a hálózati kapacitást?  

A FIO-ben való teszteléshez használt egyik stratégia beállítása a következő: `numjobs=16` . Így az egyes feladatok 16 adott példányra vannak kiosztva a Microsoft Hyper-V hálózati adapter maximalizálása érdekében.

A Windows Teljesítményfigyelőben lévő egyes adapterek tevékenységeit a **teljesítményfigyelő > a teljesítményszámlálók hozzáadása > hálózati adapter > Microsoft Hyper-V hálózati adapter** lehetőségre kattintva ellenőrizheti.

![Képernyőfelvétel: a Teljesítményfigyelő hozzáadása számláló felület.](../media/azure-netapp-files/smb-performance-performance-monitor-add-counter.png)

Miután a köteteken fut az adatforgalom, a Windows Teljesítményfigyelőben figyelheti az adaptereket. Ha nem használja ezeket a 16 virtuális adaptereket, előfordulhat, hogy nem maximalizálja a hálózati sávszélesség kapacitását.

![A Teljesítményfigyelő kimenetét bemutató képernyőkép.](../media/azure-netapp-files/smb-performance-performance-monitor-output.png)

## <a name="is-accelerated-networking-recommended"></a>A gyorsított hálózatkezelés ajánlott?

A maximális teljesítmény érdekében ajánlott a [gyorsított hálózatkezelés](../virtual-network/create-vm-accelerated-networking-powershell.md) konfigurálása, ahol lehetséges. Tartsa szem előtt az alábbi szempontokat:  

* A Azure Portal a szolgáltatást támogató virtuális gépek számára alapértelmezés szerint lehetővé teszi a gyorsított hálózatkezelést.  Más üzembe helyezési módszerek, például a Ansible és a hasonló konfigurációs eszközök azonban nem.  A gyorsított hálózatkezelés engedélyezésének sikertelensége esetén akadály a gép teljesítményét.  
* Ha a gyorsított hálózatkezelés nincs engedélyezve a virtuális gép hálózati adapterén, mert a példány típusa vagy mérete nem támogatott, akkor továbbra is le lesz tiltva a nagyobb példányok típusainál. Ezekben az esetekben manuális beavatkozásra lesz szüksége.

## <a name="are-jumbo-frames-supported"></a>A Jumbo-keretek támogatottak?

Az Azure-beli virtuális gépek nem támogatják a Jumbo-kereteket.

## <a name="is-smb-signing-supported"></a>Támogatott-e az SMB-aláírás? 

Az SMB protokoll a fájl-és nyomtatómegosztás, valamint más hálózati műveletek, például a távoli Windows-felügyelet alapját biztosítja. Ha meg szeretné akadályozni, hogy az SMB-csomagokat az átvitel során módosító, nem a közepes támadásokkal szemben, az SMB protokoll támogatja az SMB-csomagok digitális aláírását. 

Az SMB-aláírás a Azure NetApp Files által támogatott összes SMB protokoll-verzió esetében támogatott. 

## <a name="what-is-the-performance-impact-of-smb-signing"></a>Milyen hatással van az SMB-aláírás teljesítményére?  

Az SMB-aláírás káros hatással van az SMB teljesítményére. A teljesítmény romlásának egyéb lehetséges okai között az egyes csomagok digitális aláírása további ügyféloldali PROCESSZORt használ, mivel az alábbi Perfmon-kimenet látható. Ebben az esetben a 0. alap a felelős az SMB-hez, beleértve az SMB-aláírást is.  Az előző szakaszban szereplő, nem többcsatornás szekvenciális olvasási adatátviteli számokkal való összehasonlítás azt mutatja, hogy az SMB-aláírás csökkenti a teljes átviteli sebességet a 875MiB/s-ről körülbelül 250MiB/s értékre. 

![Az SMB-aláírás teljesítményének hatását bemutató diagram.](../media/azure-netapp-files/azure-netapp-files-smb-signing-performance.png)


## <a name="next-steps"></a>Következő lépések  

- [Gyakori kérdések a Azure NetApp Files](azure-netapp-files-faqs.md)
- Tekintse [meg a Azure NetApp Files: felügyelt vállalati FÁJLMEGOSZTÁS SMB](https://cloud.netapp.com/hubfs/Resources/ANF%20SMB%20Quickstart%20doc%20-%2027-Aug-2019.pdf?__hstc=177456119.bb186880ac5cfbb6108d962fcef99615.1550595766408.1573471687088.1573477411104.328&__hssc=177456119.1.1573486285424&__hsfp=1115680788&hsCtaTracking=cd03aeb4-7f3a-4458-8680-1ddeae3f045e%7C5d5c041f-29b4-44c3-9096-b46a0a15b9b1) számítási feladatokhoz az SMB-fájlmegosztás és a Azure NetApp Files használatával című témakört.