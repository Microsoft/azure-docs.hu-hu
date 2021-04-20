---
title: Gyakori kérdések az SMB teljesítményéről Azure NetApp Files| Microsoft Docs
description: Válaszok az SMB teljesítményével kapcsolatos gyakori kérdésekre a Azure NetApp Files.
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
ms.openlocfilehash: 1ec58b056bd610773500c8ace1fb12d268b980e0
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726717"
---
# <a name="faqs-about-smb-performance-for-azure-netapp-files"></a>Gyakori kérdések az SMB teljesítményéről Azure NetApp Files

Ez a cikk az SMB teljesítményével kapcsolatos ajánlott eljárásokra vonatkozó gyakori kérdéseket (GYIK) ad Azure NetApp Files.

## <a name="is-smb-multichannel-enabled-in-smb-shares"></a>Engedélyezve van a többcsatornás SMB az SMB-megosztásban? 

Igen, a többcsatornás SMB alapértelmezés szerint engedélyezve van, ez a módosítás 2020 január elején lesz beállítva. Minden SMB-megosztás előzetesen meglévő SMB-köteten engedélyezve van a funkció, és minden újonnan létrehozott köteten engedélyezve lesz a szolgáltatás a létrehozáskor. 

A funkció engedélyezése előtt létrehozott SMB-kapcsolatot vissza kell állítani a többcsatornás SMB funkció használatához. Az alaphelyzetbe állításhoz bonthatja az SMB-megosztást, majd újracsatlakoztathatja.

## <a name="is-rss-supported"></a>Támogatott az RSS?

Igen, Azure NetApp Files támogatja a fogadási oldali skálázást (RSS).

Ha a többcsatornás SMB engedélyezve van, az SMB3-ügyfél több TCP-kapcsolatot létesít az Azure NetApp Files SMB-kiszolgálóval egy egyetlen RSS-kompatibilis hálózati adapteren (NIC) keresztül. 

## <a name="which-windows-versions-support-smb-multichannel"></a>Mely Windows-verziók támogatják a többcsatornás SMB-t?

A Windows 2012 óta támogatja a többcsatornás SMB-t a legjobb teljesítmény érdekében.  További [részletek: Deploy SMB Multichannel](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/dn610980(v%3Dws.11)) (Többcsatornás SMB üzembe helyezése) és [The basics of SMB Multichannel](/archive/blogs/josebda/the-basics-of-smb-multichannel-a-feature-of-windows-server-2012-and-smb-3-0) (A többcsatornás SMB alapjai). 


## <a name="does-my-azure-virtual-machine-support-rss"></a>Támogatja az Azure-beli virtuális gépem az RSS-t?

Annak ellenőrzéshez, hogy az Azure-beli virtuális gép hálózati vezérlői támogatják-e az RSS-t, futtassa a parancsot az alábbiak szerint, `Get-SmbClientNetworkInterface` és jelölje be a `RSS Capable` mezőt: 

![Az Azure-beli virtuális gép RSS-kimenetét megjelenítő képernyőkép.](../media/azure-netapp-files/azure-netapp-files-formance-rss-support.png)

## <a name="does-azure-netapp-files-support-smb-direct"></a>Támogatja Azure NetApp Files SMB Directet?

Nem, Azure NetApp Files nem támogatja a közvetlen SMB-t. 

## <a name="what-is-the-benefit-of-smb-multichannel"></a>Mi a többcsatornás SMB előnye? 

A többcsatornás SMB szolgáltatás lehetővé teszi az SMB3-ügyfél számára, hogy kapcsolatkészletet hozzon létre egyetlen hálózati adapteren (NIC) vagy több hálózati adapteren keresztül, és hogy egyetlen SMB-munkamenetre vonatkozó kéréseket küldjön velük. Ezzel szemben az SMB1 és az SMB2 kialakításához az ügyfélnek egyetlen kapcsolatot kell létesítenie, és az adott munkamenethez szükséges összes SMB-forgalmat ezen a kapcsolaton keresztül kell elküldenie. Ez az egyetlen kapcsolat korlátozza az egyetlen ügyféltől elérhető protokollteljesítményt.

## <a name="should-i-configure-multiple-nics-on-my-client-for-smb"></a>Több hálózati ic-t is konfigurálnom kell az ügyfélen az SMB-hez?

Nem. Az SMB-ügyfél meg fog egyezni az SMB-kiszolgáló által visszaadott hálózati adapterek számában.  Minden egyes tárolókötet egy és csak egy tárolási végpontról érhető el.  Ez azt jelenti, hogy egy adott SMB-kapcsolathoz csak egy hálózati adapter lesz használva.  

Az alábbi kimenetben látható, hogy a virtuális gép 2 hálózati adaptert `Get-SmbClientNetworkInterace` (-15 és 12) rendelkezik.  Ahogyan az alábbi parancs mutatja, annak ellenére, hogy két RSS-kompatibilis hálózati adapter van, csak a 12.illesztőt használja a kapcsolat az `Get-SmbMultichannelConnection` SMB-megosztással; a 15.illesztő nincs használatban.

![Az RSS-kompatibilis hálózati adapterek kimenetét megjelenítő képernyőkép.](../media/azure-netapp-files/azure-netapp-files-rss-capable-nics.png)

## <a name="is-nic-teaming-supported-in-azure"></a>Az Azure támogatja a hálózati adapterek összevonását?

A hálózati adapterek összevonása nem támogatott az Azure-ban. Bár az Azure-beli virtuális gépek több hálózati adaptert is támogatnak, ezek nem fizikai szerkezetet, hanem logikai konstrukciót képviselnek. Így nem biztosítják a hibatűrést.  Emellett az Azure-beli virtuális gépek számára elérhető sávszélesség kiszámítása magában a gépben történik, nem pedig az egyes hálózati adapterek alapján.

## <a name="whats-the-performance-like-for-smb-multichannel"></a>Milyen teljesítményt nyújt a többcsatornás SMB?

Az alábbi tesztek és grafikonok bemutatják a többcsatornás SMB-t az egypéldányos számítási feladatokon.

### <a name="random-io"></a>Véletlenszerű I/O  

Ha a többcsatornás SMB le van tiltva az ügyfélen, a FIO és egy 40 GiB munkakészlet használatával tiszta 4 KiB olvasási és írási teszteket hajtottak végre.  Az SMB-megosztás le lett különítve az egyes tesztek között, és az SMB-ügyfélkapcsolatok száma a ( , , ) RSS hálózati adapter beállításai szerint növekményekkel `1` `4` `8` `16` `set-SmbClientConfiguration -ConnectionCountPerRSSNetworkInterface <count>` történt. A tesztek azt mutatják, hogy az alapértelmezett beállítása elegendő az I/O-igényes számítási feladatokhoz, és elhanyagolható hatással volt a `4` `8` `16` teljesítményre. 

A parancs megállapította, hogy további kapcsolatok is létrejöttek, növekményekkel a és közötti `netstat -na | findstr 445` `1` `4` `8` növekményekkel. `16`  Az SMB-hez négy processzormag volt teljes kihasználva az egyes tesztek során, amit a teljesítménystatisztika is megerősít (amely ebben a cikkben `Per Processor Network Activity Cycles` nem szerepel).)

![A többcsatornás SMB véletlenszerű I/O-összehasonlítását bemutató diagram.](../media/azure-netapp-files/azure-netapp-files-random-io-tests.png)

Az Azure-beli virtuális gép nincs hatással az SMB-tároló (és az NFS) I/O-korlátaira.  Ahogy az alábbi diagramon látható, a D32ds példánytípus gyorsítótárazott tároló IOPS-hez korlátozott, a nem gyorsítótárazott tároló IOPS-hez pedig 308 000, a nem gyorsítótárazott tároló IOPS-értékhez pedig 51 200.00.  A fenti diagram azonban sokkal több I/O-t mutat az SMB-ben.

![Véletlenszerű I/O összehasonlító tesztet ábrázoló diagram.](../media/azure-netapp-files/azure-netapp-files-random-io-tests-list.png)

### <a name="sequential-io"></a>Szekvenciális I/O 

A korábban leírt véletlenszerű I/O-tesztekhez hasonló teszteket 64 KiB szekvenciális I/O-val hajtottak végre. Bár az RSS hálózati adapterenkénti ügyfélkapcsolatok száma a 4-esnél nagyobb növekedése nem volt észrevehető hatással a véletlenszerű I/O-re, ugyanez nem vonatkozik a szekvenciális I/O-re. Ahogy az alábbi ábra is mutatja, minden növekedés az olvasási teljesítmény megfelelő növekedésével van társítva. Az írási átviteli sebesség az Azure által az egyes példánytípusra/méretre vonatkozó hálózati sávszélesség korlátozásai miatt nem változott. 

![Az átviteli sebesség tesztje összehasonlítását bemutató diagram.](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests.png)

Az Azure a hálózati sebességkorlátokat minden virtuálisgép-típusra/méretre korlátozza. A sebességkorlát csak a kimenő forgalomra van korlátozva. A virtuális gépen jelen lévő hálózati számítógépek száma nincs hatással a gép számára elérhető teljes sávszélességre.  A D32ds példánytípus hálózati korlátja például 16 000 Mbps (2000 MiB/s).  Ahogy a fenti szekvenciális diagramon is látható, a korlát hatással van a kimenő forgalomra (írások), de a többcsatornás olvasásra nem.

![A szekvenciális I/O összehasonlítási tesztet bemutató diagram.](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests-list.png)

## <a name="what-performance-is-expected-with-a-single-instance-with-a-1-tb-dataset"></a>Milyen teljesítmény várható egyetlen, 1 TB-os adatkészlettel való példánynál?

Annak érdekében, hogy részletesebb betekintést nyújtson a számítási feladatokba olvasási/írási kombinációk segítségével, az alábbi két diagram egyetlen, 50 TB-os ultra szintű felhőkötet teljesítményét mutatja 1 TB-os adatkészlettel és 4-es többcsatornás SMB-vel. A rendszer 16 optimális IODepth paramétert és rugalmas I/O-paramétereket használt a hálózati sávszélesség teljes kihasználása érdekében ( `numjobs=16` ).

Az alábbi diagramon a 4k véletlenszerű I/O eredményei 10%-os időközönként egyetlen virtuálisgép-példány és egy olvasási/írási kombináció látható:

![Diagram a Windows 2019 standard és _D32ds_v4 4K véletlenszerű I/O-tesztről.](../media/azure-netapp-files/smb-performance-standard-4k-random-io.png)

Az alábbi diagramon a szekvenciális I/O eredményei láthatóak:

![Diagram a Windows 2019 standard és _D32ds_v4 64K szekvenciális átviteli sebességről.](../media/azure-netapp-files/smb-performance-standard-64k-throughput.png)

## <a name="what-performance-is-expected-when-scaling-out-using-5-vms-with-a-1-tb-dataset"></a>Milyen teljesítmény várható 5 virtuális gép 1 TB-os adatkészlettel való felméretezése esetén?

Ezek az 5 virtuális gépre vonatkozó tesztek ugyanazt a tesztkörnyezetet használják, mint az egyetlen virtuális gép, és mindegyik folyamat a saját fájlba ír.

Az alábbi diagram a véletlenszerű I/O-hoz kapott eredményeket mutatja:

![Diagram a Windows 2019 Standard _D32ds_v4 4K 5 példányos Io-tesztről.](../media/azure-netapp-files/smb-performance-standard-4k-random-io-5-instances.png)

Az alábbi diagramon a szekvenciális I/O eredményei láthatóak:

![Diagram, amely a Windows 2019 standard _D32ds_v4 64K 5 példány szekvenciális átviteli sebességét mutatja.](../media/azure-netapp-files/smb-performance-standard-64k-throughput-5-instances.png)

## <a name="how-do-you-monitor-hyper-v-ethernet-adapters-and-ensure-that-you-maximize-network-capacity"></a>Hogyan figyelheti a Hyper-V Ethernet-adaptereket, és hogyan gondoskodik arról, hogy maximalizálja a hálózati kapacitást?  

A FIO tesztelése során használt egyik stratégia a `numjobs=16` beállítása. Ezzel 16 adott példányra külön elássa az egyes Microsoft Hyper-V érdekében.

A Windows Teljesítményfigyelőben az egyes adapterek tevékenységének ellenőrzéséhez válassza a Teljesítményfigyelő > Számlálók hozzáadása > adapterhez **és > Microsoft Hyper-V adapterhez gombra.**

![Képernyőkép a Teljesítményfigyelő Számláló hozzáadása felületéről.](../media/azure-netapp-files/smb-performance-performance-monitor-add-counter.png)

Miután lefutott az adatforgalom a köteten, a Windows Teljesítményfigyelőben figyelheti az adaptereket. Ha nem használja mind a 16 virtuális adaptert, előfordulhat, hogy nem maximalizálja a hálózati sávszélesség kapacitását.

![Képernyőkép a Teljesítményfigyelő kimenetéről.](../media/azure-netapp-files/smb-performance-performance-monitor-output.png)

## <a name="is-accelerated-networking-recommended"></a>Ajánlott a gyorsított hálózat használata?

A maximális teljesítmény érdekében javasoljuk, hogy ahol lehetséges, konfigurálja a gyorsított [](../virtual-network/create-vm-accelerated-networking-powershell.md) hálózatépítést. Tartsa szem előtt az alábbi szempontokat:  

* A Azure Portal a gyorsított hálózat alapértelmezés szerint lehetővé teszi a funkciót támogató virtuális gépek számára.  Előfordulhat azonban, hogy más telepítési módszerek, például az Ansible és hasonló konfigurációs eszközök nem.  Ha nem engedélyezi a gyorsított hálózatépítést, az a gép teljesítményét is képessé teheti.  
* Ha a gyorsított hálózatépítés azért nincs engedélyezve a virtuális gép hálózati adapterében, mert nem támogatja a példánytípust vagy -méretet, a nagyobb példánytípusok továbbra is le lesznek tiltva. Ilyen esetekben manuális beavatkozásra lesz szüksége.

## <a name="are-jumbo-frames-supported"></a>Támogatottak a Jumbo-képkockák?

Az Azure-beli virtuális gépek nem támogatják a Jumbo-képkockákat.

## <a name="is-smb-signing-supported"></a>Támogatott az SMB-aláírás? 

Az SMB protokoll biztosítja a fájl- és nyomtatómegosztási, valamint egyéb hálózati műveletek, például a távoli Windows-felügyelet alapját. Az SMB protokoll támogatja az SMB-csomagok digitális aláírását, hogy megakadályozza az átvitel közbeni SMB-csomagokat módosító, középutas támadásokat. 

Az SMB-aláírás az SMB protokoll összes olyan verziójához támogatott, amelyet a Azure NetApp Files. 

## <a name="what-is-the-performance-impact-of-smb-signing"></a>Milyen hatással van az SMB-aláírás teljesítményére?  

Az SMB-aláírás káros hatással van az SMB teljesítményére. A teljesítménycsökkenés egyéb lehetséges okai mellett az egyes csomagok digitális aláírása további ügyféloldali PROCESSZORt használ fel, ahogy az alábbi teljesítménykimenet is mutatja. Ebben az esetben a Core 0 felelős az SMB-ért, beleértve az SMB-aláírást is.  Az előző szakaszban a nem többcsatornás szekvenciális olvasási átviteli sebesség számával való összehasonlítás azt mutatja, hogy az SMB-aláírás a teljes átviteli sebességet 875MiB/s-ről körülbelül 250MiB/s-re csökkenti. 

![Az SMB-aláírás teljesítményének hatását bemutató diagram.](../media/azure-netapp-files/azure-netapp-files-smb-signing-performance.png)

## <a name="what-is-the-anticipated-impact-of-smb-encryption-on-client-workloads"></a>Mi az SMB-titkosítás várható hatása az ügyfél számítási feladataira?

Lásd: [SMB-titkosítás – gyakori kérdések.](azure-netapp-files-faqs.md#smb_encryption_impact)

## <a name="next-steps"></a>Következő lépések  

- [Gyakori kérdések a Azure NetApp Files](azure-netapp-files-faqs.md)
- Lásd: Azure NetApp Files: Felügyelt vállalati fájlmegosztások [SMB](https://cloud.netapp.com/hubfs/Resources/ANF%20SMB%20Quickstart%20doc%20-%2027-Aug-2019.pdf?__hstc=177456119.bb186880ac5cfbb6108d962fcef99615.1550595766408.1573471687088.1573477411104.328&__hssc=177456119.1.1573486285424&__hsfp=1115680788&hsCtaTracking=cd03aeb4-7f3a-4458-8680-1ddeae3f045e%7C5d5c041f-29b4-44c3-9096-b46a0a15b9b1) számítási feladatokhoz az SMB-fájlmegosztások és -Azure NetApp Files.