---
title: Gyakori kérdések a Azure NetApp Files | Microsoft Docs
description: Tekintse át a gyakori kérdéseket a Azure NetApp Files, például a hálózatkezelésről, a biztonságról, a teljesítményről, a kapacitáskezelésről és az adatáttelepítésről/-védelemről.
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
ms.date: 04/19/2021
ms.author: b-juche
ms.openlocfilehash: a8c06b25b923d663e982e940100be7b9a2a009e1
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726843"
---
# <a name="faqs-about-azure-netapp-files"></a>Gyakori kérdések a Azure NetApp Files

Ez a cikk a gyakori kérdésekre ad választ a Azure NetApp Files. 

## <a name="networking-faqs"></a>Hálózat – gyakori kérdések

### <a name="does-the-data-path-for-nfs-or-smb-go-over-the-internet"></a>Az NFS vagy az SMB adatútvonala az interneten keresztül halad?  

Nem. Az NFS vagy az SMB adatútvonala nem az interneten halad át. Azure NetApp Files egy natív Azure-szolgáltatás, amely az Azure Virtual Network (VNet) szolgáltatásban van üzembe helyezni, ahol a szolgáltatás elérhető. Azure NetApp Files delegált alhálózatot használ, és közvetlenül a VNeten biztosít hálózati adaptert. 

A [részleteket a hálózati Azure NetApp Files útmutatója tartalmazza.](./azure-netapp-files-network-topologies.md)  

### <a name="can-i-connect-a-vnet-that-i-already-created-to-the-azure-netapp-files-service"></a>Csatlakoztatok már létrehozott virtuális hálózatokat a Azure NetApp Files szolgáltatáshoz?

Igen, csatlakoztathatja a létrehozott virtuális hálózatokat a szolgáltatáshoz. 

A [részleteket a hálózati Azure NetApp Files útmutatója tartalmazza.](./azure-netapp-files-network-topologies.md)  

### <a name="can-i-mount-an-nfs-volume-of-azure-netapp-files-using-dns-fqdn-name"></a>Csatlakoztatok egy NFS-kötetet Azure NetApp Files DNS teljes tartománynevének nevével?

Igen, létrehozhatja, ha létrehozza a szükséges DNS-bejegyzéseket. Azure NetApp Files a kiépített kötet szolgáltatás IP-címét. 

> [!NOTE] 
> Azure NetApp Files további IP-eket is üzembe helyezhet a szolgáltatáshoz.  Előfordulhat, hogy a DNS-bejegyzéseket rendszeresen frissíteni kell.

### <a name="can-i-set-or-select-my-own-ip-address-for-an-azure-netapp-files-volume"></a>Be lehet állítani vagy kiválasztani a saját IP-címemet egy Azure NetApp Files kötethez?  

Nem. A kötetek IP Azure NetApp Files-hozzárendelése dinamikus. A statikus IP-hozzárendelés nem támogatott. 

### <a name="does-azure-netapp-files-support-dual-stack-ipv4-and-ipv6-vnet"></a>Támogatja Azure NetApp Files (IPv4 és IPv6) virtuális hálózatokat?

Nem, Azure NetApp Files nem támogatja a kettős verem (IPv4 és IPv6) virtuális hálózatokat.  
 
## <a name="security-faqs"></a>Biztonsági gyakori kérdések

### <a name="can-the-network-traffic-between-the-azure-vm-and-the-storage-be-encrypted"></a>Titkosítható az Azure-beli virtuális gép és a tároló közötti hálózati forgalom?

Az NFSv4.1-ügyfelek és Azure NetApp Files kötetek közötti adatforgalom AES-256 titkosítással, Kerberos használatával titkosítható. A [részletekért lásd: Configure NFSv4.1 Kerberos encryption for Azure NetApp Files.configure NFSv4.1 Kerberos encryption for Azure NetApp Files.](configure-kerberos-encryption.md)   

Az NFSv3- vagy SMB3-ügyfelek közötti adatforgalom nincs titkosítva Azure NetApp Files kötetek felé. Az (NFS- vagy SMB-ügyfelet futtató) Azure-beli virtuális gépekről a virtuális gépekre Azure NetApp Files forgalom azonban olyan biztonságos, mint bármely más Azure-VM–VM forgalom. Ez a forgalom az Azure adatközpont-hálózatának helyi része. 

### <a name="can-the-storage-be-encrypted-at-rest"></a>Titkosítható-e a tárterület az adatok tárolásához?

Minden Azure NetApp Files a FIPS 140-2 szabvány használatával van titkosítva. Az összes kulcsot a Azure NetApp Files kezeli. 

### <a name="how-are-encryption-keys-managed"></a>Hogyan kezeli a titkosítási kulcsokat? 

A szolgáltatás Azure NetApp Files kezeli a kulcskezelést. Minden kötethez egyedi XTS-AES-256 adattitkosítási kulcs jön létre. A titkosítási kulcshierarchia az összes kötetkulcs titkosítására és védelmére használható. Ezek a titkosítási kulcsok soha nem jelennek meg és nem jelentek titkosítatlan formátumban. A titkosítási kulcsok a kötet törlésekor azonnal törlődnek.

Az ügyfél által felügyelt kulcsok (Bring Your Own Key Azure Dedicated HSM) támogatása szabályozott alapon elérhető az USA keleti régiójában, az USA déli középső régiójában, az USA 2. nyugati régiójában és a US Gov Virginia régióban. A hozzáférést itt [anffeedback@microsoft.com](mailto:anffeedback@microsoft.com) kérheti: . Amint a kapacitás elérhetővé válik, a kérelmeket jóváhagyjuk.

### <a name="can-i-configure-the-nfs-export-policy-rules-to-control-access-to-the-azure-netapp-files-service-mount-target"></a>Konfigurálhatóak az NFS exportálási házirendszabályai a Azure NetApp Files csatlakoztatási célhoz való hozzáférés szabályozására?

Igen, egyetlen NFS-exportálási szabályzatban legfeljebb öt szabályt konfigurálhat.

### <a name="does-azure-netapp-files-support-network-security-groups"></a>Támogatja Azure NetApp Files a hálózati biztonsági csoportokat?

Nem, jelenleg nem alkalmazhat hálózati biztonsági csoportokat a Azure NetApp Files vagy a szolgáltatás által létrehozott hálózati adapterek delegált alhálózatára.

### <a name="can-i-use-azure-rbac-with-azure-netapp-files"></a>Használhatom az Azure RBAC-t Azure NetApp Files?

Igen, Azure NetApp Files támogatja az Azure RBAC funkcióit. A beépített Azure-szerepkörök mellett egyéni [szerepköröket](../role-based-access-control/custom-roles.md) is létrehozhat a Azure NetApp Files. 

A szolgáltatásengedélyek teljes Azure NetApp Files lásd: Azure erőforrás-szolgáltatói műveletek a [`Microsoft.NetApp`](../role-based-access-control/resource-provider-operations.md#microsoftnetapp) következőben: .

### <a name="are-azure-activity-logs-supported-on-azure-netapp-files"></a>Támogatottak az Azure-tevékenységnaplók Azure NetApp Files?

Azure NetApp Files egy natív Azure-szolgáltatás. A rendszer minden PUT, POST és DELETE API-t Azure NetApp Files a naplóba. A naplók például olyan tevékenységeket mutatnak, mint például a pillanatkép létrehozója, a kötetet módosító felhasználó stb.

Az API-műveletek teljes listájáért lásd: [Azure NetApp Files REST API.](/rest/api/netapp/)

### <a name="can-i-use-azure-policies-with-azure-netapp-files"></a>Használhatok Azure-szabályzatokat Azure NetApp Files?

Igen, létrehozhat egyéni [Azure-szabályzatokat.](../governance/policy/tutorials/create-custom-policy-definition.md) 

Azonban nem hozhat létre Azure-szabályzatokat (egyéni elnevezési szabályzatokat) a Azure NetApp Files felületén. Lásd: [Irányelvek a Azure NetApp Files tervezéshez.](azure-netapp-files-network-topologies.md#considerations)

## <a name="performance-faqs"></a>Gyakori kérdések a teljesítményről

### <a name="what-should-i-do-to-optimize-or-tune-azure-netapp-files-performance"></a>Mit tegyek a teljesítmény optimalizálásához vagy Azure NetApp Files hangolnom?

A következő műveleteket használhatja a teljesítményre vonatkozó követelményeknek megfelelően: 
- Győződjön meg arról, hogy a virtuális gép megfelelően van méretezett.
- Engedélyezze a gyorsított hálózatépítést a virtuális gépen.
- Válassza ki a kapacitáskészlet kívánt szolgáltatási szintjét és méretét.
- Hozzon létre egy kötetet a kapacitás és a teljesítmény kívánt kvótaméretével.

### <a name="how-do-i-convert-throughput-based-service-levels-of-azure-netapp-files-to-iops"></a>Hogyan átviteli sebességen alapuló szolgáltatási szinteket IOPS-Azure NetApp Files konvertálni?

Az MB/s-eket az alábbi képlettel konvertálhatja IOPS-értékké:  

`IOPS = (MBps Throughput / KB per IO) * 1024`

### <a name="how-do-i-change-the-service-level-of-a-volume"></a>Hogyan a kötet szolgáltatási szintjét?

Egy meglévő kötet szolgáltatási szintjét úgy módosíthatja, hogy a kötetet a kötethez kívánt szolgáltatási szintet használó másik kapacitáskészletbe mozgatja. [](azure-netapp-files-service-levels.md) Lásd: [Kötet szolgáltatási szintjének dinamikus módosítása.](dynamic-change-volume-service-level.md) 

### <a name="how-do-i-monitor-azure-netapp-files-performance"></a>Hogyan a Azure NetApp Files teljesítményét?

Azure NetApp Files kötetteljesítmény-mérőszámokat biztosít. Az alkalmazásokat a Azure Monitor is használhatja a használati metrikák Azure NetApp Files.  Az [Azure NetApp Files](azure-netapp-files-metrics.md) teljesítménymetrikák listájáért lásd: Metrikák Azure NetApp Files.

### <a name="whats-the-performance-impact-of-kerberos-on-nfsv41"></a>Milyen hatással van a Kerberos teljesítménye az NFSv4.1-re?

Az NFSv4.1 biztonsági beállításaival, a tesztelt teljesítményvektorokkal és a várt teljesítményre gyakorolt hatásokkal kapcsolatos információkért lásd: A [Kerberos teljesítménye NFSv4.1-kötetek](performance-impact-kerberos.md) esetén. 

## <a name="nfs-faqs"></a>NFS – gyakori kérdések

### <a name="i-want-to-have-a-volume-mounted-automatically-when-an-azure-vm-is-started-or-rebooted--how-do-i-configure-my-host-for-persistent-nfs-volumes"></a>Azt szeretném, hogy egy kötet automatikusan csatlakoztatva legyen egy Azure-beli virtuális gép indításakor vagy újraindításakor.  Hogyan konfigurálni a gazdagépet állandó NFS-kötetek számára?

Ahhoz, hogy egy NFS-kötet automatikusan csatlakozik a virtuális gép indításakor vagy újraindításakor, adjon hozzá egy bejegyzést a `/etc/fstab` gazdagépen található fájlhoz. 

További [információ: Kötet csatlakoztatása Windows](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md) vagy Linux rendszerű virtuális gépekhez.  

### <a name="why-does-the-df-command-on-nfs-client-not-show-the-provisioned-volume-size"></a>Miért nem mutatja az NFS-ügyfél DF-parancsa a kiépített kötet méretét?

A DF-fájlban jelentett kötetméret a kötet Azure NetApp Files maximális mérete. A DF Azure NetApp Files kötetének mérete nem tükrözi a kötet kvótáját vagy méretét.  A kötet méretét Azure NetApp Files kvótát a köteten vagy az API Azure Portal keresztül kaphatja meg.

### <a name="what-nfs-version-does-azure-netapp-files-support"></a>Milyen NFS-verziót Azure NetApp Files támogatni?

Azure NetApp Files NFSv3 és NFSv4.1 támogatott. Kötetet [bármelyik](azure-netapp-files-create-volumes.md) NFS-verzióval létrehozhat. 

### <a name="how-do-i-enable-root-squashing"></a>Hogyan a gyökér-összecsukás?

A kötet exportálási szabályzatával megadhatja, hogy a gyökérfiók hozzáférhet-e a kötethez. Részletekért [lásd: Exportálási szabályzat](azure-netapp-files-configure-export-policy.md) konfigurálása NFS-kötethez.

### <a name="can-i-use-the-same-file-path-volume-creation-token-for-multiple-volumes"></a>Használhatom ugyanazt a fájlútvonalat (kötet-létrehozási jogkivonatot) több kötethez is?

Igen, írhat. A fájl elérési útját azonban vagy egy másik előfizetésben, vagy egy másik régióban kell használni.   

Tegyük fel például, hogy létrehoz egy nevű `vol1` kötetet. Ezután létrehoz egy másik nevű kötetet egy másik kapacitáskészletben, de ugyanabban az előfizetésben és `vol1` régióban. Ebben az esetben ha ugyanazt a kötetnevet `vol1` használja, az hibát okoz. Ha ugyanazt a fájlútvonalat használja, a névnek egy másik régióban vagy előfizetésben kell lennie.

### <a name="when-i-try-to-access-nfs-volumes-through-a-windows-client-why-does-the-client-take-a-long-time-to-search-folders-and-subfolders"></a>Amikor Windows-ügyfélen keresztül próbálok hozzáférni az NFS-kötetekhez, miért tart sokáig az ügyfélnek mappákban és almappákban keresnie?

Győződjön meg arról, hogy a Windows-ügyfélen engedélyezve van a mappák és almappák keresési `CaseSensitiveLookup` sebességének felgyorsítás érdekében:

1. A CaseSensitiveLookup engedélyezéséhez használja a következő PowerShell-parancsot:   
    `Set-NfsClientConfiguration -CaseSensitiveLookup 1`    
2. Csatlakoztassa a kötetet a Windows-kiszolgálón.   
    Példa:   
    `Mount -o rsize=1024 -o wsize=1024 -o mtype=hard \\10.x.x.x\testvol X:*`

### <a name="how-does-azure-netapp-files-support-nfsv41-file-locking"></a>Hogyan támogatja Azure NetApp Files NFSv4.1 fájlzárolást? 

NFSv4.1-ügyfelek esetén az Azure NetApp Files támogatja az NFSv4.1 fájlzárolási mechanizmust, amely fenntartja a bérletalapú modellben található összes fájlzárolás állapotát. 

Az RFC 3530 Azure NetApp Files az NFS-ügyfél által tartott összes állapothoz egyetlen bérletidőszakot határoz meg. Ha az ügyfél nem újítja meg a bérletét a megadott időszakon belül, a kiszolgáló felszabadítja az ügyfél bérletéhez társított összes államot.  

Ha például egy kötetet rögzítő ügyfél nem válaszol vagy az időtúllépések után összeomlik, a zárolások felszabadulnak. Az ügyfél explicit módon vagy implicit módon újíthatja meg a bérletet olyan műveletek elvégzésével, mint például egy fájl olvasása.   

A türelmi időszak egy speciális feldolgozási időszakot határoz meg, amelyben az ügyfelek megpróbálják helyreállítni a zárolási állapotukat a kiszolgáló helyreállítása során. A bérletek alapértelmezett időkorlátja 30 másodperc, 45 másodperces türelmi időszakkal. Ezt követően az ügyfél bérlete felszabadul.   

## <a name="smb-faqs"></a>SMB – gyakori kérdések

### <a name="which-smb-versions-are-supported-by-azure-netapp-files"></a>Mely SMB-verziókat támogatja a Azure NetApp Files?

Azure NetApp Files támogatja az SMB 2.1-et és az SMB 3.1-et (amely támogatja az SMB 3.0-t).    

### <a name="is-an-active-directory-connection-required-for-smb-access"></a>Szükség van Active Directory SMB-hozzáféréshez? 

Igen, létre kell hoznia egy Active Directory kapcsolatot az SMB-kötet üzembe helyezése előtt. A sikeres csatlakozás érdekében a megadott tartományvezérlőknek elérhetőnek kell lenniük a Azure NetApp Files alhálózata számára.  További [információ: SMB-kötet](./azure-netapp-files-create-volumes-smb.md) létrehozása. 

### <a name="how-many-active-directory-connections-are-supported"></a>Hány Active Directory támogatott?

Azure NetApp Files nem támogat több Active Directory (AD) kapcsolatot egyetlen *régióban,* még akkor sem, ha az AD-kapcsolatok különböző NetApp-fiókokban vannak. Azonban több AD-kapcsolattal is lehet egy előfizetésben, ha az AD-kapcsolatok különböző régiókban vannak. Ha több AD-kapcsolatra van szüksége egy régión belül, különálló előfizetéseket is használhat. 

Az AD-kapcsolat NetApp-fiókonként van konfigurálva; az AD-kapcsolat csak azon a NetApp-fiókon keresztül látható, amelyből létre van hozva.

### <a name="does-azure-netapp-files-support-azure-active-directory"></a>Támogatja Azure NetApp Files a Azure Active Directory? 

A [Azure Active Directory (AD) tartományi szolgáltatások](../active-directory-domain-services/overview.md) és a Active Directory Domain Services [(AD DS) is](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) támogatottak. A meglévő tartományvezérlőket Active Directory használhatja a Azure NetApp Files. A tartományvezérlők virtuális gépként az Azure-ban, vagy az ExpressRoute-on vagy virtuális hálózaton keresztül S2S VPN. Azure NetApp Files jelenleg nem támogatja az AD-Azure Active Directory [az](https://azure.microsoft.com/resources/videos/azure-active-directory-overview/) AD-csatlakozást.

Ha alkalmazásokat Azure NetApp Files a Azure Active Directory Domain Services, a szervezeti egység elérési útja a `OU=AADDC Computers` NetApp-fiók Active Directory konfigurálásakor történik.

### <a name="what-versions-of-windows-server-active-directory-are-supported"></a>A Windows Server Active Directory verziói támogatottak?

Azure NetApp Files a Windows Server 2008r2SP1-2019 verziókat támogatja a Active Directory Domain Services.

### <a name="why-does-the-available-space-on-my-smb-client-not-show-the-provisioned-size"></a>Miért nem mutatja az SMB-ügyfélen rendelkezésre álló terület a kiépített méretet?

Az SMB-ügyfél által jelentett kötetméret az a maximális méret, Azure NetApp Files a kötet mérete. A kötet Azure NetApp Files SMB-ügyfélen látható mérete nem tükrözi a kötet kvótáját vagy méretét. A kötet méretét Azure NetApp Files kvótát a köteten vagy az API Azure Portal keresztül kaphatja meg.

### <a name="im-having-issues-connecting-to-my-smb-share-what-should-i-do"></a>Problémákat értem el az SMB-megosztáshoz való csatlakozáskor. Mit tegyek?

Ajánlott eljárásként állítsa a számítógép órajel-szinkronizálásának maximális tűréshatárát öt percre. További információ: A számítógép [órajel-szinkronizálásának maximális tűréshatára.](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/jj852172(v=ws.11)) 

### <a name="can-i-manage-smb-shares-sessions-and-open-files-through-computer-management-console-mmc"></a>Felügyelheti a , a és a `SMB Shares` `Sessions` et `Open Files` a Számítógép-kezelési konzolon (MMC)?

A `SMB Shares` , `Sessions` a és a `Open Files` számítógép-kezelési konzolon (MMC) keresztüli kezelése jelenleg nem támogatott.

### <a name="how-can-i-obtain-the-ip-address-of-an-smb-volume-via-the-portal"></a>Hogyan szerezhetem be egy SMB-kötet IP-címét a portálon keresztül?

Használja a kötet áttekintő ablaktábláján található **JSON-nézet** hivatkozást, és keresse meg a **startIp** azonosítót a   ->  **mountTargets tulajdonságok között.**

### <a name="smb-encryption-faqs"></a>SMB-titkosítás – gyakori kérdések

Ez a szakasz az SMB-titkosítással (SMB 3.0 és SMB 3.1.1) kapcsolatos gyakori kérdésekre ad választ.

#### <a name="what-is-smb-encryption"></a>Mi az az SMB-titkosítás?  

[Az SMB-titkosítás](/windows-server/storage/file-server/smb-security) végpontok között titkosítja az SMB-adatokat, és megvédi az adatokat a nem megbízható hálózatokon történt lehallgatásokkal szemben. Az SMB-titkosítás az SMB 3.0-s és annál nagyobb verziókban támogatott. 

#### <a name="how-does-smb-encryption-work"></a>Hogyan működik az SMB-titkosítás?

Amikor kérést küld a tárolónak, az ügyfél titkosítja a kérést, amelyet a tároló visszafejt. A válaszokat hasonlóan titkosítja a kiszolgáló, és az ügyfél visszafejti.

#### <a name="which-clients-support-smb-encryption"></a>Mely ügyfelek támogatják az SMB-titkosítást?

Windows 10, Windows 2012 és újabb verziók támogatják az SMB-titkosítást.

#### <a name="with-azure-netapp-files-at-what-layer-is-smb-encryption-enabled"></a>A Azure NetApp Files rétegben melyik rétegen van engedélyezve az SMB-titkosítás?  

Az SMB-titkosítás megosztási szinten van engedélyezve.

#### <a name="what-forms-of-smb-encryption-are-used-by-azure-netapp-files"></a>Milyen SMB-titkosítási formákat Azure NetApp Files?

Az SMB 3.0 AES-CCM algoritmust, míg az SMB 3.1.1 az AES-GCM algoritmust alkalmazza

#### <a name="is-smb-encryption-required"></a>Szükség van SMB-titkosításra?

SMB-titkosításra nincs szükség. Ezért csak akkor van engedélyezve egy adott megosztáshoz, ha a felhasználó Azure NetApp Files engedélyezni. Azure NetApp Files megosztások soha nem fedik fel az internetet. Ezek csak egy adott virtuális hálózatról érhetők el VPN-en vagy expressz útvonalon keresztül, így a Azure NetApp Files megosztások eredendően biztonságosak. Az SMB-titkosítás engedélyezését teljes mértékben a felhasználó dönti el. A funkció engedélyezése előtt vegye figyelembe a várható teljesítménybeli büntetést.

#### <a name="what-is-the-anticipated-impact-of-smb-encryption-on-client-workloads"></a><a name="smb_encryption_impact"></a>Mi az SMB-titkosítás várható hatása az ügyfél számítási feladataira?

Bár az SMB-titkosítás hatással van az ügyfélre (az üzenetek titkosításához és visszafejtéséhez szükséges processzorterhelésre) és a tárterületre (az átviteli sebesség csökkentése), az alábbi táblázat csak a tárolásra gyakorolt hatást emeli ki. A számítási feladatok éles környezetben való üzembe helyezése előtt tesztelje a titkosítási teljesítményre gyakorolt hatást a saját alkalmazásaiban.

|     I/O-profil       |     Hatás        |
|-  |-  |
|     Számítási feladatok olvasása és írása      |     10% és 15% között        |
|     Nagy metaadat-igényű        |     5%    |

## <a name="capacity-management-faqs"></a>Kapacitáskezelés – gyakori kérdések

### <a name="how-do-i-monitor-usage-for-capacity-pool-and-volume-of-azure-netapp-files"></a>Hogyan a kapacitáskészlet és a kapacitáskészletek Azure NetApp Files? 

Azure NetApp Files kapacitáskészlet- és kötethasználati metrikákat biztosít. A használati adatokat Azure Monitor is használhatja a Azure NetApp Files. A [részletekért lásd Azure NetApp Files](azure-netapp-files-metrics.md) metrikákat. 

### <a name="can-i-manage-azure-netapp-files-through-azure-storage-explorer"></a>Kezelhetem a Azure NetApp Files a Azure Storage Explorer?

Nem. Azure NetApp Files nem támogatja a Azure Storage Explorer.

### <a name="how-do-i-determine-if-a-directory-is-approaching-the-limit-size"></a>Hogyan, hogy egy könyvtár megközelíti-e a korlát méretét?

Az ügyféltől származó paranccsal láthatja, hogy egy könyvtár megközelíti-e a címtár-metaadatok maximális méretkorlátját `stat` (320 MB).   

Egy 320 MB-os könyvtárban a blokkok száma 655360, a blokkok mérete pedig 512 bájt.  (Ez azt jelenti, hogy 320x1024x1024/512.)  Ez a szám egy 320 MB-os könyvtár legfeljebb körülbelül 4 millió fájlját jelenti. A fájlok maximális száma azonban alacsonyabb is lehet, olyan tényezőktől függően, mint például a könyvtárban nem ASCII-karaktereket tartalmazó fájlok száma. Ezért az alábbi paranccsal állapítsa meg, hogy a címtár megközelíti-e `stat` a korlátot.  

Angol nyelvű Példák:

```console
[makam@cycrh6rtp07 ~]$ stat bin
File: 'bin'
Size: 4096            Blocks: 8          IO Block: 65536  directory

[makam@cycrh6rtp07 ~]$ stat tmp
File: 'tmp'
Size: 12288           Blocks: 24         IO Block: 65536  directory
 
[makam@cycrh6rtp07 ~]$ stat tmp1
File: 'tmp1'
Size: 4096            Blocks: 8          IO Block: 65536  directory
```


## <a name="data-migration-and-protection-faqs"></a>Adatáttelepítéssel és -védelemmel kapcsolatos gyakori kérdések

### <a name="how-do-i-migrate-data-to-azure-netapp-files"></a>Hogyan az adatokat a Azure NetApp Files?
Azure NetApp Files NFS- és SMB-köteteket biztosít.  Bármilyen fájlalapú másolási eszközt használhat az adatok szolgáltatásba való áttelepítéséhez. 

A NetApp saaS-alapú megoldást kínál, a [NetApp Cloud Syncet.](https://cloud.netapp.com/cloud-sync-service)  A megoldás lehetővé teszi, hogy NFS- vagy SMB-adatokat replikáljon NFS Azure NetApp Files vagy SMB-megosztások használatával. 

Az adatok másolására számos ingyenes eszköz is használható. NFS esetén a forrásadatok másolására és szinkronizálására olyan számítási feladatokkal kapcsolatos eszközök használhatók, mint az [rsync,](https://rsync.samba.org/examples.html) Azure NetApp Files kötetre. Az SMB-hez a [robocopy](/windows-server/administration/windows-commands/robocopy) számítási feladatait is használhatja.  Ezek az eszközök fájl- vagy mappaengedélyeket is replikálni tudnak. 

A helyszínről a Azure NetApp Files adatok áttelepítésének követelményei a következők: 

- Győződjön Azure NetApp Files, hogy az elérhető a cél Azure-régióban.
- Ellenőrizze a forrás és a célkötet IP Azure NetApp Files közötti hálózati kapcsolatot. A helyszíni rendszer és a Azure NetApp Files közötti adatátvitel expressroute-on keresztül támogatott.
- Hozza létre a Azure NetApp Files kötetet.
- A forrásadatokat a célkötetre az előnyben részesített fájlmásoló eszközzel továbbíthatja.

### <a name="how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region"></a>Hogyan egy másolatot egy Azure NetApp Files egy másik Azure-régióban?
    
Azure NetApp Files NFS- és SMB-köteteket biztosít.  Bármilyen fájlalapú másolási eszköz használható az adatok Azure-régiók közötti replikálása érdekében. 

A NetApp egy SaaS-alapú megoldást kínál, a [NetApp Cloud Syncet.](https://cloud.netapp.com/cloud-sync-service)  A megoldás lehetővé teszi, hogy NFS- vagy SMB-adatokat replikáljon NFS Azure NetApp Files vagy SMB-megosztások használatával. 

Az adatok másolására számos ingyenes eszköz is használható. NFS esetén a forrásadatok másolására és szinkronizálására olyan számítási feladatokat futtató eszközöket használhat, mint az [rsync,](https://rsync.samba.org/examples.html) Azure NetApp Files kötetre. Az SMB-hez ugyanúgy használhatja a [robocopy](/windows-server/administration/windows-commands/robocopy) számítási feladatokat.  Ezek az eszközök fájl- vagy mappaengedélyeket is replikálni tudnak. 

A virtuális kötetek Azure NetApp Files Azure-régióba való replikálása a következő: 
- Győződjön Azure NetApp Files, hogy az elérhető a cél Azure-régióban.
- Ellenőrizze az egyes régiók virtuális hálózatai közötti hálózati kapcsolatot. A virtuális hálózatok közötti globális társviszony-létesítés jelenleg nem támogatott.  A virtuális hálózatok közötti kapcsolatot egy ExpressRoute-kapcsolat kapcsolati kapcsolattal vagy egy kapcsolati kapcsolattal S2S VPN létesítheti. 
- Hozza létre a Azure NetApp Files kötetet.
- A forrásadatok átvitele a célkötetre az előnyben részesített fájlmásolási eszközzel.

### <a name="is-migration-with-azure-data-box-supported"></a>Támogatott a Azure Data Box migrálás?

Nem. Azure Data Box jelenleg nem támogatja Azure NetApp Files- 

### <a name="is-migration-with-azure-importexport-service-supported"></a>Támogatott a migrálás az Azure Import/Export szolgáltatással?

Nem. Az Azure Import/Export szolgáltatás jelenleg Azure NetApp Files támogatja.

## <a name="product-faqs"></a>Termékekkel kapcsolatos gyakori kérdések

### <a name="can-i-use-azure-netapp-files-nfs-or-smb-volumes-with-azure-vmware-solution-avs"></a>Használhatok NFS Azure NetApp Files- vagy SMB-köteteket Azure VMware Solution (AVS)?

NfS-Azure NetApp Files AVS Windows rendszerű vagy Linux rendszerű virtuális gépeken is csatlakoztathat. Az SMB-Azure NetApp Files AVS Windows rendszerű virtuális gépeken is leképezheti. További részletekért lásd: [Azure NetApp Files a Azure VMware Solution.]( ../azure-vmware/netapp-files-with-azure-vmware-solution.md)  

### <a name="what-regions-are-supported-for-using-azure-netapp-files-nfs-or-smb-volumes-with-azure-vmware-solution-avs"></a>Milyen régiók támogatottak a Azure NetApp Files NFS- vagy SMB-kötetek Azure VMware Solution (AVS)?

Az Azure NetApp Files NFS- vagy SMB-kötetek AVS-sel való használata a következő régiókban támogatott: az USA keleti régiója, az USA nyugati régiója, Nyugat-Európa és Kelet-Ausztrália.

## <a name="next-steps"></a>Következő lépések  

- [Microsoft Azure ExpressRoute – gyakori kérdések](../expressroute/expressroute-faqs.md)
- [Microsoft Azure Virtual Network gyakori kérdések](../virtual-network/virtual-networks-faq.md)
- [Azure-támogatáskérések létrehozása](../azure-portal/supportability/how-to-create-azure-support-request.md)
- [Azure Data Box](../databox/index.yml)
- [Gyakori kérdések az SMB teljesítményéről a Azure NetApp Files](azure-netapp-files-smb-performance.md)
