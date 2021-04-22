---
title: A Azure NetApp Files | Microsoft Docs
description: Összefoglalja a legújabb új funkciókat és fejlesztéseket a Azure NetApp Files.
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
ms.topic: overview
ms.date: 04/21/2021
ms.author: b-juche
ms.openlocfilehash: fa028d8fffd2a4097b5bf7d7326d355ae56aebd7
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107862812"
---
# <a name="whats-new-in-azure-netapp-files"></a>A Azure NetApp Files

Azure NetApp Files rendszeresen frissül. Ez a cikk összefoglalja a legújabb új funkciókat és fejlesztéseket. 

## <a name="april-2021"></a>2021. április

* [Az SMB folyamatos rendelkezésre állás (CA) megosztások támogatása az FSLogix felhasználói profiltárolókhoz](azure-netapp-files-create-volumes-smb.md#add-an-smb-volume) (előzetes verzió)  

    [Az FSLogix](/fslogix/overview) olyan megoldások készlete, amelyek javítják, engedélyezik és leegyszerűsítik a nem állandó Windows-számítástechnikai környezeteket. Az FSLogix-megoldások nyilvános és magánfelhőkben is használhatók virtuális környezetekhez. Az FSLogix megoldásokkal hordozhatóbb számítástechnikai munkamenetek hozhatók létre fizikai eszközök használata esetén. Az FSLogix dinamikus hozzáférést biztosít az SMB megosztott hálózati tárolókon tárolt állandó felhasználóiprofil-tárolókhoz, beleértve a Azure NetApp Files. A tárolási szolgáltatás karbantartási eseményeinek FSLogix-rugalmasságának további növelése érdekében az Azure NetApp Files kiterjesztett támogatást nyújt a transzparens SMB-feladatátvételhez az SMB folyamatos rendelkezésre állási [(CA)](azure-netapp-files-create-volumes-smb.md#add-an-smb-volume) megosztásokkal a Azure NetApp Files felhasználói profiltárolók esetében. További Azure NetApp Files [Windows Virtual Desktop a megoldásokról.](azure-netapp-files-solution-architectures.md#windows-virtual-desktop)  

* [SMB3 protokolltitkosítás](azure-netapp-files-create-volumes-smb.md#add-an-smb-volume) (előzetes verzió) 

    Mostantól engedélyezheti az SMB3 protokolltitkosítást Azure NetApp Files SMB- és kettős protokollú köteten. Ez a funkció lehetővé teszi az SMB3-adatok titkosítását az [SMB 3.0 AES-CCM algoritmusa és az SMB 3.1.1-kapcsolatokon az AES-GCM](/windows-server/storage/file-server/file-server-smb-overview#features-added-in-smb-311-with-windows-server-2016-and-windows-10-version-1607) algoritmus használatával. Az SMB3-titkosítást nem használó SMB-ügyfelek nem fogják tudni elérni ezt a kötetet. A rendszer a beállítástól függetlenül titkosítja az adatok titkosítását. Az SMB-titkosítás tovább fokozza a biztonságot. Ez azonban hatással lehet az ügyfélre (az üzenetek titkosítása és visszafejtése processzorterhelése). Ez hatással lehet a tárolóerőforrás-kihasználtságra is (az átviteli sebesség csökkentése). A számítási feladatok éles környezetben való üzembe helyezése előtt tesztelje a titkosítási teljesítményre gyakorolt hatást az alkalmazásokon.

* [Active Directory Domain Services (ADDS) LDAP felhasználóleképezés NFS bővített csoportokkal](configure-ldap-extended-groups.md) (előzetes verzió)   

    Alapértelmezés szerint a Azure NetApp Files legfeljebb 16 csoport-azonosítót támogat az NFS felhasználói hitelesítő adatainak kezelésekor, az [RFC 5531](https://tools.ietf.org/html/rfc5531)szabványban meghatározottak szerint. Ezzel az új képességgel most már 1024-re növelheti a maximális értéket, ha a felhasználók az alapértelmezett csoportszámnál több tagja van. Ennek a képességnek a támogatása érdekében az NFS-kötetek mostantól hozzáadhatók az ADDS LDAP-hoz, amely lehetővé teszi a kiterjesztett csoportbejegyzésekkel rendelkező Active Directory (legfeljebb 1024 csoporttal rendelkező) LDAP-felhasználók számára a kötethez való hozzáférést. 

## <a name="march-2021"></a>2021. március
 
* [SMB folyamatos rendelkezésre állású (CA) megosztások](azure-netapp-files-create-volumes-smb.md#add-an-smb-volume) (előzetes verzió)  

    Az SMB transzparens feladatátvétele lehetővé teszi a karbantartási műveleteket az Azure NetApp Files szolgáltatáson anélkül, hogy megszakad a kapcsolat az SMB-köteten adatokat tároló és hozzáférő kiszolgálóalkalmazásokkal. Az SMB transzparens feladatátvételének támogatásához a Azure NetApp Files támogatja az SMB folyamatos rendelkezésre állású megosztások használatát az Azure-beli virtuális gépeken futó SMB-SQL Server-alkalmazásokhoz. Ez a funkció jelenleg Windows rendszerű SQL Server. A Linux SQL Server jelenleg nem támogatott. Ennek a szolgáltatásnak az engedélyezése jelentős SQL Server teljesítménybeli fejlesztéseket és méretezési és költségbeli előnyöket biztosít az egypéldányos, Always-On feladatátvevőfürt-példányok és Always-On rendelkezésre állási csoportok [üzemelő példányai számára.](azure-netapp-files-solution-architectures.md#sql-server) Lásd: [A Azure NetApp Files használatának előnyei SQL Server telepítéshez.](solutions-benefits-azure-netapp-files-sql-server.md)

* [Régiók közötti replikációs célkötet automatikus átméretezése](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-a-cross-region-replication-destination-volume)

    Régiók közötti replikációs kapcsolatban a célkötetet a forráskötet mérete alapján automatikusan átméretezi a rendszer. Így nem kell külön átméreteznie a célkötetet. Ez az automatikus átméretezés akkor alkalmazható, ha a kötetek aktív replikációs kapcsolatban vannak, vagy ha a replikációs társviszony megszakad az újraszinkronizálási művelettel. Ahhoz, hogy ez a funkció működjön, gondoskodnia kell arról, hogy a forrás- és a célkötet számára is elegendő hely legyen a kapacitáskészletben.

## <a name="december-2020"></a>2020. december

* [Azure Application Consistent Snapshot Tool](azacsnap-introduction.md) (előzetes verzió)    

    Az Azure Application Consistent Snapshot Tool (AzAcSnap) egy parancssori eszköz, amellyel leegyszerűsítheti a külső adatbázisok (SAP HANA) adatvédelmét Linux-környezetekben (például SUSE és RHEL).   

    Az AzAcSnap a kötet pillanatkép- és replikációs funkcióit használja Azure NetApp Files azure-beli nagyméretű példányban. A következő előnyöket biztosítja:

    * Alkalmazás-konzisztens adatvédelem 
    * Adatbáziskatalógus kezelése 
    * *Alkalmi kötetvédelem* 
    * Tárolókötetek klónozása 
    * Vészhelyreállítás támogatása 

## <a name="november-2020"></a>2020. november

* [Pillanatkép-visszaállítás](azure-netapp-files-manage-snapshots.md#revert-a-volume-using-snapshot-revert)

    A pillanatkép-visszaállítási funkció lehetővé teszi, hogy gyorsan visszaállítsa a kötetet arra az állapotra, amely egy adott pillanatkép készítésekor volt. A kötetek visszaállítása a legtöbb esetben sokkal gyorsabb, mint az egyes fájlok visszaállítása pillanatképből az aktív fájlrendszerbe. Emellett a tárhely is hatékonyabb, mint egy pillanatkép visszaállítása egy új kötetre.

## <a name="september-2020"></a>2020. szeptember

* [Azure NetApp Files replikálás (előzetes](cross-region-replication-introduction.md) verzió)

  Azure NetApp Files támogatja a régiók közötti replikációt. Ezzel az új vészhelyreállítási képességgel gyorsan és költséghatékonyan replikálhatja az Azure NetApp Files-köteteket az egyik Azure-régióból a másikba, így megvédheti adatait a nem előre nem látható regionális hibáktól. Azure NetApp Files régióközi replikáció a NetApp SnapMirror® technológiát használja; A csak a módosított blokkokat küldi át a hálózaton tömörített, hatékony formátumban. Ez a saját fejlesztésű technológia minimálisra csökkenti a régiók közötti replikáláshoz szükséges adatmennyiséget, így megtakarítja az adatátviteli költségeket. Emellett lerövidíti a replikációs időt, így kisebb visszaállításipont-célkitűzést (RPO) érhet el.

* [Manuális QoS-kapacitáskészlet](manual-qos-capacity-pool-introduction.md) (előzetes verzió)  

    A manuális QoS-kapacitáskészletben a kötetek kapacitását és átviteli sebességét egymástól függetlenül rendelheti hozzá. A manuális QoS-kapacitáskészletben létrehozott összes kötet teljes átviteli sebességét a készlet teljes átviteli sebessége korlátozza. Ezt a készlet mérete és a szolgáltatásszintű átviteli sebesség kombinációja határozza meg. Másik lehetőségként a kapacitáskészlet [QoS-típusa](azure-netapp-files-understand-storage-hierarchy.md#qos_types) lehet automatikus (automatikus), ami az alapértelmezett beállítás. Az automatikus QoS-kapacitáskészletek átviteli sebességét a rendszer automatikusan hozzárendeli a készletben található kötetekhez a kötetek méretkvótája alapján.

* [LDAP-aláírás](azure-netapp-files-create-volumes-smb.md) (előzetes verzió)   

    Azure NetApp Files támogatja az LDAP-aláírást a biztonságos LDAP-keresések érdekében a Azure NetApp Files szolgáltatás és a felhasználó által megadott Active Directory Domain Services tartományvezérlők között. Ez a szolgáltatás jelenleg előzetes kiadásban elérhető.

* [AES-titkosítás AD-hitelesítéshez](azure-netapp-files-create-volumes-smb.md) (előzetes verzió)

    Azure NetApp Files támogatja az AES-titkosítást az LDAP-kapcsolaton a tartományvezérlővel, hogy engedélyezze az AES-titkosítást egy SMB-köteten. Ez a szolgáltatás jelenleg előzetes kiadásban elérhető. 

* Új [metrikák:](azure-netapp-files-metrics.md)   

    * Új kötetmetrikák: 
        * *Lefoglalt kötet mérete:* A kötet kiosztott mérete
    * Új készletmetrikák: 
        * *Lefoglalt készlet mérete:* A készlet kiosztott mérete 
        * *A készlet teljes pillanatképmérete:* A készletben található összes kötet pillanatképméretének összege

## <a name="july-2020"></a>2020. július

* [Kettős protokollú (NFSv3 és SMB) kötet](create-volumes-dual-protocol.md)

    Most már létrehozhat egy Azure NetApp Files kötetet, amely lehetővé teszi az egyidejű kettős protokollú (NFS v3 és SMB) hozzáférést az LDAP-felhasználóleképezés támogatásával. Ez a funkció olyan eseteket tesz lehetővé, amikor egy Linux-alapú számítási feladat adatokat hoz létre és tárol egy Azure NetApp Files köteten. Ugyanakkor az alkalmazottaknak Windows-alapú ügyfeleket és szoftvereket kell használnia az ugyanattól a kötettől származó, újonnan létrehozott Azure NetApp Files elemzéséhez. Az egyidejű kettős protokollos hozzáférési funkcióval nem kell a számítási feladat által létrehozott adatokat egy külön kötetre másolni az elemzés után, a tárolási költségek és a működési idő megtakarítása érdekében. Ez a funkció ingyenes (a [Azure NetApp Files tárolási](https://azure.microsoft.com/pricing/details/netapp/) költségek továbbra is érvényesek), és általánosan elérhető. További információt az egyidejű [kettős protokollos hozzáférési dokumentációban talál.](create-volumes-dual-protocol.MD)

* [NFS v4.1 Kerberos-titkosítás átvitel közben](configure-kerberos-encryption.MD)

    Azure NetApp Files az NFS-ügyféltitkosítást Kerberos módban (krb5, krb5i és krb5p) támogatja az AES-256 titkosítással, ami további adatbiztonságot biztosít. Ez a funkció ingyenes (a [Azure NetApp Files tárolási](https://azure.microsoft.com/pricing/details/netapp/) költségek továbbra is érvényesek), és általánosan elérhető. További információt az [NFS v4.1 Kerberos titkosítási dokumentációjában talál.](configure-kerberos-encryption.MD)

* [Dinamikus kötet szolgáltatási szintjének módosítása](dynamic-change-volume-service-level.MD)

    A felhő rugalmasságot biztosít az it-kiadások terén. Most már módosíthatja egy meglévő Azure NetApp Files-kötet szolgáltatási szintjét, ha a kötetet egy másik kapacitáskészletbe mozgatja, amely a kötethez kívánt szolgáltatási szintet használja. A kötet szolgáltatásszint-módosítása nem igényli az adatok áttelepítését. Ez a kötet adatsík-hozzáférését sem befolyásolja. Módosíthatja a meglévő köteteket, hogy magasabb szolgáltatási szintet használjanak a jobb teljesítmény érdekében, vagy alacsonyabb szolgáltatási szintet használjanak a költségoptimalizáláshoz. Ez a funkció ingyenes (a [Azure NetApp Files](https://azure.microsoft.com/pricing/details/netapp/) tárolási költségek továbbra is érvényesek), és jelenleg nyilvános előzetes verzióban érhető el. A szolgáltatás előzetes kiadására a dinamikus kötet szolgáltatási szintjének változását [bemutató dokumentációban regisztrálhat.](dynamic-change-volume-service-level.md)

* [Kötet pillanatkép-szabályzata](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies) (előzetes verzió) 

    Azure NetApp Files lehetővé teszi, hogy a kötetek időponthozott pillanatképeket készítsenek. Mostantól létrehozhat egy pillanatkép-szabályzatot, Azure NetApp Files automatikusan létrehozhat kötet-pillanatképeket a választott gyakorisággal. A pillanatképeket óránként, naponta, hetente vagy havonta készítheti el. Azt is megadhatja, hogy legfeljebb hány pillanatképet kell megtartani a pillanatkép-szabályzat részeként. Ez a funkció ingyenes (a Azure NetApp Files [tárolási](https://azure.microsoft.com/pricing/details/netapp/) költségek továbbra is érvényesek), és jelenleg előzetes verzióban érhető el. A funkció előzetes verzióra való regisztráláshoz tekintse meg a kötet [pillanatfelvétel-szabályzatának dokumentációját.](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies)

* [NFS-gyökérelérés exportálási szabályzata](azure-netapp-files-configure-export-policy.md)

    Azure NetApp Files most már megadhatja, hogy a gyökérfiók hozzáférhet-e a kötethez. 

* [Pillanatkép elérési útjának elrejtése](azure-netapp-files-manage-snapshots.md#restore-a-file-from-a-snapshot-using-a-client)

    Azure NetApp Files lehetővé teszi annak megadását, hogy a felhasználó láthatja-e és elérheti-e a csatlakoztatott köteten lévő címtárat `.snapshot` (NFS-ügyfeleket) vagy mappát `~snapshot` (SMB-ügyfeleket).

## <a name="may-2020"></a>2020. május

* [Biztonsági mentési szabályzat felhasználói](create-active-directory-connections.md) (előzetes verzió)

    Azure NetApp Files további, emelt szintű jogosultságokat igénylő fiókokat foglalhat bele a fiókkal való használatra létrehozott Azure NetApp Files. A megadott fiókok a fájl vagy mappa szintjén módosíthatják az NTFS-engedélyeket. Megadhat például egy nem kiemelt jogosultságú szolgáltatásfiókot, amely az adatok áttelepítésére használható egy SMB-fájlmegosztásra a Azure NetApp Files. A Biztonsági mentési szabályzat felhasználói funkció jelenleg előzetes verzióban érhető el.

## <a name="next-steps"></a>Következő lépések
* [Mi az az Azure NetApp Files?](azure-netapp-files-introduction.md)
* [Az Azure NetApp Files tárhely-hierarchiájának ismertetése](azure-netapp-files-understand-storage-hierarchy.md) 
