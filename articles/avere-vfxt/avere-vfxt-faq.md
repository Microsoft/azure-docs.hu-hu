---
title: Gyakori kérdések – avere vFXT for Azure
description: Ezekkel a gyakran ismételt kérdésekkel eldöntheti, hogy az Azure-hoz készült avere-vFXT megfelel-e az igényeinek. Ismerje meg, hogy a avere vFXT for Azure hogyan működik más Azure-összetevőkkel.
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: rohogue
ms.custom: references_regions
ms.openlocfilehash: 647a9792384a21dfc191371ef444cbdc098bc819
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92342365"
---
# <a name="avere-vfxt-for-azure-faq"></a>Gyakori kérdések az Azure-hoz készült Avere vFXT-ről

Ez a cikk azokat a kérdéseket válaszolja meg, amelyek segítségével eldöntheti, hogy az Azure-hoz készült avere-vFXT az Ön igényeinek megfelelően. Alapszintű információt nyújt a avere vFXT, és bemutatja, hogyan működik együtt más Azure-összetevőkkel és a külső gyártók termékeivel.

## <a name="general"></a>Általános kérdések

### <a name="what-is-avere-vfxt-for-azure"></a>Mi az Azure-hoz készült Avere vFXT?

Az Azure-hoz készült avere-vFXT egy nagy teljesítményű fájlrendszer, amely az Azure számítási feladatait a kritikus fontosságú számítási feladatok hatékony feldolgozásához gyorsítótárazza.

### <a name="is-avere-vfxt-a-storage-solution"></a>Avere vFXT?

Nem. A avere vFXT for Azure egy olyan fájlrendszer- *gyorsítótár* , amely tárolási környezetekhez, például az EMC vagy a NetApp NAS kiszolgálóhoz, vagy egy Azure Blob-tárolóhoz csatlakozik. A avere vFXT egyszerűsíti az ügyfelektől érkező adatkéréseket, és gyorsítótárazza azokat az adatokkal, amelyeket a teljesítmény növelése érdekében a méret és az idő múlásával javít. A avere vFXT maga nem tárolja az adattárolást. Nem rendelkezik információval a mögötte tárolt adatok mennyiségéről.

### <a name="is-avere-vfxt-a-tiering-solution"></a>VFXT megoldás a avere?

A avere vFXT for Azure nem automatikusan tömöríti az adatszinteket a gyors és a lassú elérésű szint között.  

### <a name="how-do-i-know-if-an-environment-is-right-for-avere-vfxt"></a>Hogyan tudni, hogy van-e megfelelő környezet a avere-vFXT?

Ennek a kérdésnek a legjobb módja, ha megkérdezi, hogy "a munkaterhelés gyorsítótárazható?" A számítási feladatnak van-e magas olvasási és írási aránya? Például 80/20 vagy 70/30 olvasás/írás.

Vegye fontolóra a avere vFXT for Azure-t, ha olyan fájl-alapú elemzési folyamattal rendelkezik, amely nagy számú Azure-beli virtuális gépen fut, és az alábbi feltételek közül egyet vagy többet teljesít:

* A teljes teljesítmény lassú vagy inkonzisztens a hosszú fájl-hozzáférési idő (a követelményektől függően tíz ezredmásodperc vagy másodperc) miatt. Ez a késés nem fogadható el az ügyfél számára.

* A feldolgozáshoz szükséges adatmennyiség egy WAN-környezet túlsó végén található, és az adatátvitelt véglegesen nem célszerű áthelyezni. Lehet, hogy az adatközpont egy másik Azure-régióban vagy egy ügyfél-adatközpontban található.

* Jelentős számú ügyfél kéri az adatokat – például egy nagy teljesítményű számítástechnikai (HPC-) fürtben. Az egyidejű kérések nagy száma növelheti a késést.

* Az ügyfél az Azure-beli virtuális gépek aktuális folyamatát szeretné futtatni, és egy POSIX-alapú megosztott tárolási (vagy gyorsítótárazási) megoldásra van szüksége a méretezhetőség érdekében. A avere vFXT for Azure használatával nem kell újratervezni a munkafolyamatot, hogy natív hívásokat hajtson végre az Azure Blob Storage-ba.

* A HPC-alkalmazás a NFSv3-ügyfeleken alapul. (Bizonyos esetekben használhat SMB 2,1-ügyfeleket, de a teljesítmény korlátozott.)

A következő diagram segíthet a kérdés megválaszolásában. Minél közelebb van a munkafolyamat a jobb felső sarokban, annál valószínűbb, hogy az Azure gyorsítótárazási megoldás avere-vFXT megfelelő a környezete számára.

![Diagram diagram, amely azt mutatja, hogy a avere vFXT több ezer ügyfelet tartalmazó olvasási nagy terhelések is jobbak](media/avere-vfxt-fit-assessment.png)

### <a name="at-what-scale-of-clients-does-the-avere-vfxt-solution-make-the-most-sense"></a>Az ügyfelek milyen skálán teszik a avere vFXT-megoldást?

A avere vFXT cache megoldás több száz, több ezer vagy több tízezer számítási mag kezelésére készült. Ha van néhány, könnyű munkát futtató gép, a avere vFXT nem a megfelelő megoldás.

A tipikus avere-vFXT ügyfeleink a 1 000 CPU-magoktól kezdődően igényes számítási feladatokat futtatnak. Ezek a környezetek akár 50 000 vagy több magot is használhatnak. Mivel a avere vFXT méretezhető, csomópontokat adhat hozzá, hogy támogassa ezeket a számítási feladatokat, mivel azok egyre nagyobb átviteli sebességre vagy további IOPS igényelnek.

### <a name="how-much-data-can-an-avere-vfxt-environment-store"></a>Mennyibe kerül az avere vFXT-környezet?

Az Azure-hoz készült avere-vFXT egy gyorsítótár. Nem tárolja kifejezetten az adattárolást. A RAM és az SSD-k kombinációját használja a gyorsítótárazott adatmennyiségek tárolásához. A rendszer véglegesen tárolja az adattárolási tárolót (például egy NetApp NAS-vagy BLOB-tárolót). A avere vFXT rendszer nem rendelkezik információval a mögötte tárolt adatok mennyiségéről. A avere vFXT csak az ügyfelek által kért adatrészhalmazt gyorsítótárazza.  

### <a name="what-regions-are-supported"></a>Mely régiók támogatottak?

Az Azure-hoz készült avere-vFXT minden régióban támogatott, kivéve a szuverén régiókat (Kína, Németország). Győződjön meg arról, hogy a használni kívánt régió képes támogatni a számítási magok nagy mennyiségét és a avere vFXT-fürt létrehozásához szükséges virtuálisgép-példányokat.

### <a name="how-do-i-get-help-with-avere-vfxt"></a>Hogyan segítséget kaphat a avere vFXT?

A támogatási munkatársak speciális csoportja segítséget nyújt az Azure-hoz készült avere-vFXT. A támogatási jegy a Azure Portalból való megnyitásához kövesse a [segítség a rendszerhez](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt) című témakör utasításait.

### <a name="is-avere-vfxt-highly-available"></a>A avere vFXT nagyon elérhető?

Igen, a avere vFXT kizárólag HA megoldásként fut.

### <a name="does-avere-vfxt-for-azure-also-support-other-cloud-services"></a>Támogatja a avere vFXT for Azure más felhőalapú szolgáltatásokat is?

Igen, az ügyfelek több felhőalapú szolgáltatót is használhatnak a avere vFXT-fürttel. Támogatja az AWS S3 standard gyűjtőket, a Google Cloud Services standard gyűjtőt és az Azure Blob-tárolókat.

> [!NOTE]
> Az AWS vagy a Google Cloud Storage szolgáltatással a avere vFXT használatára vonatkozó díj vonatkozik. Az Azure Blob Storage használatához nincs szükség további szoftverre.

## <a name="technical-compute"></a>Technikai: számítás

### <a name="can-you-describe-what-an-avere-vfxt-environment-looks-like"></a>Le tudja írni a avere vFXT-környezetét?

A avere vFXT több Azure-beli virtuális gépekből álló fürtözött készülék. A Python-függvénytár kezeli a fürtök létrehozását, törlését és módosítását. Olvassa el a [Mi az az Azure avere vFXT?](avere-vfxt-overview.md) című témakört.

### <a name="what-kind-of-azure-virtual-machines-does-avere-vfxt-run-on"></a>Milyen típusú Azure-beli virtuális gépeken fut a avere vFXT?  

Az Azure-fürthöz tartozó avere-vFXT Microsoft Azure E32s_v3 virtuális gépeket használnak.

<!-- ### Can I mix and match virtual machine types for my cluster?

No, you must choose one virtual machine type or the other.
    
### Can I move between virtual machine types?

Yes, there is a migration path to move from one VM type to the other. [Open a support ticket](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt) to learn how.
-->

### <a name="does-the-avere-vfxt-environment-scale"></a>A avere vFXT-környezete méretezhető?

A avere vFXT-fürt lehet kisebb, mint három virtuális gép csomópontja, vagy akár 24 csomópont is. Ha úgy véli, hogy több mint kilenc csomóponttal rendelkező fürtre van szüksége, forduljon az Azure technikai támogatási szolgálatához. A csomópontok nagyobb száma nagyobb telepítési architektúrát igényel.

### <a name="does-the-avere-vfxt-environment-autoscale"></a>A avere vFXT-környezete "autoscale"?

Nem. A fürt méretének méretezése felfelé és lefelé is elvégezhető, de a fürtcsomópontok hozzáadása vagy eltávolítása manuális lépés.

### <a name="can-i-run-the-avere-vfxt-cluster-as-a-virtual-machine-scale-set"></a>Futtathatom a avere vFXT-fürtöt virtuálisgép-méretezési csoportként?

A avere vFXT nem támogatja a virtuálisgép-méretezési csoport központi telepítését. Számos beépített rendelkezésre állási támogatási mechanizmus csak a fürtben részt vevő atomi virtuális gépekhez lett kialakítva.  

### <a name="can-i-run-the-avere-vfxt-cluster-on-low-priority-vms"></a>Futtathatom a avere vFXT-fürtöt alacsony prioritású virtuális gépeken?

Nem, a rendszer a virtuális gépek mögöttes stabil készletét igényli.

### <a name="can-i-run-the-avere-vfxt-cluster-in-containers"></a>Futtathatom a avere vFXT-fürtöt a tárolókban?

Nem, a avere-vFXT független alkalmazásként kell telepíteni.

### <a name="do-the-avere-vfxt-vms-count-against-my-compute-quota"></a>A avere vFXT virtuális gépek száma a számítási kvóta alapján történjen?

Igen. Győződjön meg arról, hogy elegendő kvóta van a régióban a fürt támogatásához.  

### <a name="can-i-run-the-avere-vfxt-cluster-machines-in-different-availability-zones"></a>Futtathatom a avere vFXT-fürtöt különböző rendelkezésre állási zónákban?

Nem. A magas rendelkezésre állású modell a avere-vFXT jelenleg nem támogatja a különböző rendelkezésre állási zónákban található egyedi avere vFXT-fürtöket.

### <a name="can-i-clone-avere-vfxt-virtual-machines"></a>A avere vFXT virtuális gépek klónozása is megtehető?

Nem, a támogatott Python-szkriptet kell használnia a avere vFXT-fürt csomópontjainak hozzáadásához vagy eltávolításához. További információért olvassa el [a avere vFXT-fürt kezelése](avere-vfxt-manage-cluster.md)című témakört.

### <a name="is-there-a-vm-version-of-the-software-i-can-run-in-my-own-local-environment"></a>Létezik a saját helyi környezetében futtatható szoftver "VM" verziója?

Nem, a rendszer fürtözött készülékként van felkínálva, és meghatározott virtuálisgép-típusokra van tesztelve. Ez a korlátozás segít az ügyfeleknek elkerülni egy olyan rendszer létrehozását, amely nem támogatja a tipikus avere vFXT-munkafolyamatok nagy teljesítményű követelményeit.

## <a name="technical-disks"></a>Technikai: lemezek

### <a name="what-types-of-disks-are-supported-for-the-azure-vms"></a>Milyen típusú lemezeket támogat az Azure-beli virtuális gépek?

Az Azure-hoz készült avere-vFXT 1 TB vagy 4 TB-os prémium SSD-konfigurációkat használhatnak. A prémium SSD-konfiguráció több felügyelt lemezként is telepíthető.

### <a name="does-the-cluster-support-unmanaged-disks"></a>Támogatja a fürt a nem felügyelt lemezeket?

Nem, a fürtnek felügyelt lemezekre van szüksége.

### <a name="does-the-system-support-local-attached-ssds"></a>Támogatja a rendszer a helyi (csatlakoztatott) SSD-ket?

Az Azure-hoz készült avere-vFXT jelenleg nem támogatja a helyi SSD-ket. A avere vFXT használt lemezeken le kell állítani és újra kell indítani a rendszert, de ebben a konfigurációban csak a helyi csatlakoztatott SSD-k állíthatók le.

### <a name="does-the-system-support-ultra-ssds"></a>Támogatja a rendszer az ultra SSD-ket?

Nem, a rendszeren csak a prémium szintű SSD-konfigurációk támogatottak.

### <a name="can-i-detach-my-premium-ssds-and-reattach-them-later-to-preserve-cache-contents-between-use"></a>Leválaszthatom a prémium SSD-ket, és később újra csatolhatom a gyorsítótár tartalmának megőrzéséhez a használat között?

Az SSD-k leválasztása és újbóli csatlakoztatása nem támogatott. Előfordulhat, hogy a forrás metaadatainak vagy fájljának tartalma módosult a használati adatok között, ami adatintegritási problémákat okozhat.

### <a name="does-the-system-encrypt-the-cache"></a>Titkosítja a rendszeren a gyorsítótárat?

Az adatlemezek a lemezek között vannak, de nincs titkosítva. Maguk a lemezek azonban titkosítva is lehetnek. További információ: [a házirendek biztonságossá tétele és használata az Azure-beli virtuális gépeken](../virtual-machines/security-policy.md#encryption).

## <a name="technical-networking"></a>Technikai: hálózatkezelés

### <a name="what-network-is-recommended"></a>Milyen hálózat ajánlott?

Ha helyszíni tárolót használ a avere vFXT, akkor a tárterület és a fürt között 1 GB/s vagy annál nagyobb hálózati kapcsolattal kell rendelkeznie. Ha kis mennyiségű adattal rendelkezik, és készen áll az adatok felhőbe másolására a feladatok futtatása előtt, előfordulhat, hogy a VPN-kapcsolat elegendő.

> [!TIP]
> Minél lassabb a hálózati kapcsolat, annál lassabbak lesznek a kezdeti "hideg" olvasások. A lassú olvasások a munkahelyi folyamat késését fokozzák.

### <a name="can-i-run-avere-vfxt-in-a-different-virtual-network-than-my-compute-cluster"></a>Futtathatom a avere-vFXT egy másik virtuális hálózaton, mint a számítási fürtem?

Igen, a avere vFXT rendszerét egy másik virtuális hálózatban is létrehozhatja. A részletekért olvassa el [a avere vFXT-rendszer megtervezése](avere-vfxt-deploy-plan.md) című leírást.

### <a name="does-avere-vfxt-require-its-own-subnet"></a>A avere vFXT saját alhálózata szükséges?

Igen. A avere vFXT szigorúan magas rendelkezésre állású (HA) fürtként fut, és több IP-címet igényel a működéséhez. Ha a fürt a saját alhálózatában található, akkor elkerülhető az IP-címek ütközésének kockázata, ami problémát okozhat a telepítésben és a normál működésben. A fürt alhálózata más erőforrások által használt virtuális hálózaton belül lehet, feltéve, hogy egyetlen IP-cím sem fedi át egymást.

### <a name="can-i-run-avere-vfxt-on-infiniband"></a>Futtathatom a avere vFXT a InfiniBand-on?

Nem, a avere vFXT csak Ethernet/IP protokollt használ.

### <a name="how-do-i-access-my-on-premises-nas-environment-from-avere-vfxt"></a>Hogyan hozzáférni a helyszíni NAS-környezethez a avere vFXT?

A avere vFXT-környezet olyan, mint bármely más Azure-beli virtuális gép, amely a hálózati átjárón vagy VPN-en keresztül, az ügyfél-adatközpontba (és vissza) irányítja át a hozzáférést. Ha a környezetében elérhető, érdemes lehet az Azure ExpressRoute-kapcsolatot használni.

### <a name="what-are-the-bandwidth-requirements-for-avere-vfxt"></a>Milyen sávszélességre vonatkozó követelmények vonatkoznak a avere-vFXT?

A teljes sávszélességre vonatkozó követelmény két tényezőtől függ:

* A forrástól kért adatok mennyisége
* Az ügyfélrendszer tűréshatára a kezdeti adatbetöltések késése esetén  

A késésre érzékeny környezetek esetében az 1 GB/s-os minimális kapcsolati sebességű Fiber-megoldást kell használnia. A ExpressRoute használata, ha elérhető.  

### <a name="can-i-run-avere-vfxt-with-public-ip-addresses"></a>Futtathatok avere-vFXT nyilvános IP-címekkel?

Nem, a avere vFXT az ajánlott eljárásokkal védett hálózati környezetben kell üzemeltetni.

### <a name="can-i-restrict-internet-access-from-my-clusters-virtual-network"></a>Korlátozható az Internet-hozzáférés a fürt virtuális hálózatáról?

Általánosságban megadhatja, hogy a virtuális hálózatra szükség szerint további biztonsági beállítások is érvényesek legyenek, néhány korlátozás azonban zavarhatja a fürt működését.

Például a virtuális hálózatról érkező kimenő internet-hozzáférés korlátozása problémát okoz a fürt számára, kivéve, ha olyan szabályt is hozzáad, amely explicit módon engedélyezi a hozzáférést a AzureCloud. Ez a helyzet a [GitHub kiegészítő dokumentációjában](https://github.com/Azure/Avere/tree/master/src/vfxt/internet_access.md)olvasható.

A testreszabott biztonsággal kapcsolatos segítségért forduljon az ügyfélszolgálathoz a [Segítség kérése a rendszerhez](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt)című témakörben leírtak szerint.

## <a name="technical-back-end-storage-core-filers"></a>Technikai: háttérbeli tárolás (alapszintű filers)

### <a name="how-many-core-filers-does-a-single-avere-vfxt-environment-support"></a>Hány alapvető Filer-t támogat egyetlen avere vFXT-környezet?

A avere vFXT-fürtök legfeljebb 20 Magos Filer-t támogatnak.

### <a name="how-does-the-avere-vfxt-environment-store-data"></a>Hogyan tárolja a avere vFXT-környezet az adattárolást?

A avere vFXT nem tároló. Ez egy olyan gyorsítótár, amely több tárolási célpontból származó adatokat olvas és ír a Core filers néven. A prémium szintű SSD-lemezeken tárolt, avere-vFXT található adatkészletek átmenetiek, és végül a háttérbeli alapszintű Filer-tárolóba kerülnek.

### <a name="which-core-filers-does-avere-vfxt-support"></a>Mely alapszintű Filer-avere támogatja a vFXT?

Általános értelemben a avere vFXT for Azure a következő rendszereket támogatja alapszintű Filer-ként:

* Dell EMC Isilon (OneFS 7,1, 7,2, 8,0 és 8,1) 
* NetApp-ONTAP (fürtözött mód 9,4, 9,3, 9,2, 9.1 P1, 8.0-8.3) és (7 mód 7. *, 8.0-8.3)

* Azure Blob-tárolók (csak helyileg redundáns tárolás)
* AWS S3 gyűjtők
* Google Cloud-gyűjtők

### <a name="why-doesnt-avere-vfxt-support-all-nfs-filers"></a>Miért nem támogatja a avere a vFXT az összes NFS-Filer-t?

Bár az összes NFS-platform azonos IETF-szabványoknak felel meg, a gyakorlatban minden megvalósítás saját hirtelen fordulattal rendelkezik. Ezek a részletek befolyásolják, hogy a avere vFXT hogyan kommunikál a tárolási rendszerrel. A támogatott rendszerek a legszélesebb körben használt platformok a piactéren.

### <a name="does-avere-vfxt-support-private-object-storage-such-as-swiftstack"></a>Támogatja a avere vFXT a privát objektumok tárolását (például SwiftStack)?

A avere vFXT nem támogatja a privát objektumok tárolását.

### <a name="how-can-i-get-a-specific-storage-product-under-support"></a>Hogyan szerezhetek be egy adott tárolási terméket a támogatás alatt?

A támogatás a mező igény szerinti mennyiségén alapul. Ha van elég bevétel-alapú kérelem egy NAS-megoldás támogatásához, akkor figyelembe vesszük. Az Azure-támogatáson keresztül teheti meg a kérelmeket.

### <a name="can-i-use-azure-blob-storage-as-a-core-filer"></a>Használhatom az Azure Blob Storage-t Core Filer-ként?

Igen, az Azure-hoz készült avere-vFXT egy blokk BLOB tárolót használhatnak Cloud Core filerként.

### <a name="what-are-the-storage-account-requirements-for-a-blob-core-filer"></a>Mik a blob Core Filer Storage-fiókra vonatkozó követelményei?

A Storage-fióknak általános célú v2-(GPv2-) fióknak kell lennie, és csak helyileg redundáns tárolásra van konfigurálva. A Geo-redundáns tárolás és a zóna-redundáns tárolás nem támogatott.

A Storage-fiókra vonatkozó követelményekkel kapcsolatos további információkért olvassa el az [Azure Blob Storage Cloud Core filert](avere-vfxt-add-storage.md#azure-blob-storage-cloud-core-filer) .

### <a name="can-i-use-archive-blob-storage"></a>Használhatom az Archive blob Storage szolgáltatást?

Nem. Az archiválási tárterületre vonatkozó szolgáltatói szerződés (SLA) nem kompatibilis a avere vFXT rendszer valós idejű címtár-és fájl-hozzáférési igényeivel.

### <a name="can-i-use-cool-blob-storage"></a>Használhatom a Cool blob Storage szolgáltatást?

A ritka elérésű rétegbeli blob Storage használata általában nem ajánlott az Azure Core Filer avere-vFXT. A lassú elérési szint alacsonyabb tárolási költségeket nyújt, de magasabb üzemeltetési költségeket is biztosít. (További részletekért lásd a [blob díjszabásának blokkolása](<https://azure.microsoft.com/pricing/details/storage/blobs/>) című témakört.) Ha az adatok elérése és módosítása vagy törlése gyakran megtörténik, vegye fontolóra a gyors használatot.

A [hozzáférési szintek](../storage/blobs/storage-blob-storage-tiers.md#cool-access-tier) további információt biztosítanak arról, hogy mikor érdemes a ritka elérésű rétegbeli tárterületet vFXT Core filerként használni.

### <a name="how-do-i-encrypt-the-blob-container"></a>Hogyan titkosítani a BLOB-tárolót?

Az Azure-ban (előnyben részesített) vagy a avere vFXT Core Filer szinten is konfigurálhatja a Blobok titkosítását.  

### <a name="can-i-use-my-own-encryption-key-for-a-blob-core-filer"></a>Használhatom a blob Core Filer saját titkosítási kulcsát?

Alapértelmezés szerint az adattitkosítás a Microsoft által felügyelt kulcsokkal történik az Azure Blob-, tábla-és üzenetsor-tároláshoz, valamint Azure Files. A blob Storage és a Azure Files titkosításához használhatja a saját kulcsát. Ha úgy dönt, hogy avere vFXT-titkosítást használ, akkor a avere által generált kulcsot kell használnia, és helyileg kell tárolnia.

## <a name="purchasing"></a>Beszerzés

### <a name="how-do-i-get-avere-vfxt-for-azure-licensing"></a>Hogyan avere vFXT az Azure licenceléshez?

Az Azure piactéren egyszerűen avere vFXT az Azure-licenchez. Regisztráljon egy Azure-fiókra, majd kövesse az [avere vFXT-fürt üzembe helyezése](avere-vfxt-deploy.md) a avere vFXT-fürt létrehozásához című témakör utasításait.

### <a name="how-much-does-avere-vfxt-cost"></a>Mennyibe kerül a avere vFXT?

Az Azure-ban nincs további licencelési díj a avere vFXT-fürtök használatához. Az ügyfelek felelősek a tárolásért és az egyéb Azure-használati díjakért.

### <a name="can-avere-vfxt-vms-be-run-as-low-priority"></a>Avere vFXT virtuális gépek alacsony prioritással futnak?

Nem, a avere vFXT-fürtök "always on" szolgáltatást igényelnek. A fürtök kikapcsolhatók, ha nem szükségesek.

## <a name="next-steps"></a>Következő lépések

Az Azure-hoz készült avere vFXT megismeréséhez olvassa el ezeket a cikkeket a saját rendszer megtervezéséhez és üzembe helyezéséhez:

* [Az Avere vFXT rendszer megtervezése](avere-vfxt-deploy-plan.md)
* [Az üzembe helyezés áttekintése](avere-vfxt-deploy-overview.md)
* [Felkészülés a avere vFXT-fürt létrehozására](avere-vfxt-prereqs.md)
* [Az Avere vFXT-fürt üzembe helyezése](avere-vfxt-deploy.md)

Ha többet szeretne megtudni a avere vFXT kapcsolatos képességekről és használati esetekről, látogasson el [Az Azure avere vFXT](https://azure.microsoft.com/services/storage/avere-vfxt/).