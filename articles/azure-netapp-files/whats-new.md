---
title: A Azure NetApp Files újdonságai | Microsoft Docs
description: Összefoglalja a Azure NetApp Files legújabb új funkcióit és fejlesztéseit.
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
ms.date: 12/04/2020
ms.author: b-juche
ms.openlocfilehash: b40bbd6c470302b78b78744307805021a3649376
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100571035"
---
# <a name="whats-new-in-azure-netapp-files"></a>A Azure NetApp Files újdonságai

Azure NetApp Files rendszeresen frissül. Ez a cikk a legújabb új funkciókról és fejlesztésekről nyújt összefoglalót. 

## <a name="december-2020"></a>2020. december

* [Azure Application konzisztens pillanatkép-eszköz](azacsnap-introduction.md) (nyilvános előzetes verzió)    

    Az Azure Application konzisztens pillanatkép-eszköz (AzAcSnap) egy olyan parancssori eszköz, amely lehetővé teszi, hogy leegyszerűsítse a harmadik féltől származó adatbázisok SAP HANA (például a SUSE és a RHEL) adatvédelmi funkcióit.   

    A AzAcSnap kihasználja a kötet-pillanatfelvételt és a replikálási funkciókat Azure NetApp Files és az Azure nagyméretű példányain. A következő előnyöket biztosítja:

    * Alkalmazás – konzisztens adatvédelem 
    * Adatbázis-katalógus kezelése 
    * *Ad hoc* mennyiségi védelem 
    * A tárolási kötetek klónozása 
    * A vész-helyreállítási támogatás 

## <a name="november-2020"></a>2020. november

* [Pillanatkép visszaállítása](azure-netapp-files-manage-snapshots.md#revert-a-volume-using-snapshot-revert)

    A pillanatkép-visszavonási funkció lehetővé teszi, hogy gyorsan visszaállítson egy kötetet arra az állapotba, amely egy adott pillanatkép készítésekor megtörtént. A legtöbb esetben a kötetek visszafordítása sokkal gyorsabb, mint az egyes fájlok pillanatképből az aktív fájlrendszerbe való visszaállítása. Emellett a pillanatképek új kötetre való visszaállításához is nagyobb a terület.

## <a name="september-2020"></a>2020. szeptember

* [Régiók közötti replikálás Azure NetApp Files](cross-region-replication-introduction.md) (nyilvános előzetes verzió)

  A Azure NetApp Files mostantól támogatja a régiók közötti replikálást. Ezzel az új vész-helyreállítási képességgel gyorsan és költséghatékonyan replikálhatja Azure NetApp Files köteteit az egyik Azure-régióból a másikba, így az adatok az előre nem látható regionális meghibásodások miatt is védelmet biztosíthatnak. Azure NetApp Files a régiók közötti replikáció a NetApp SnapMirror® Technology technológiát használja; a rendszer csak a megváltozott blokkokat tömörített, hatékony formátumban továbbítja a hálózaton. Ez a szabadalmaztatott technológia minimálisra csökkenti a régiók közötti replikáláshoz szükséges adatok mennyiségét, így az adatátviteli költségeket is megtakaríthatja. Emellett lerövidíti a replikálási időt, így elérheti a kisebb visszaállítási pontok célját (RPO) is.

* [Kézi QoS-kapacitás készlete](manual-qos-capacity-pool-introduction.md) (előzetes verzió)  

    A manuális QoS-kapacitás készletében a kötetek kapacitását és átviteli sebességét egymástól függetlenül lehet hozzárendelni. A manuális QoS-kapacitási készlettel létrehozott kötetek teljes átviteli sebességét a készlet teljes átviteli sebessége korlátozza. Ezt a készlet méretének és a szolgáltatási szintű átviteli sebességnek a kombinációja határozza meg. Azt is megteheti, hogy a kapacitás-készlet [QoS-típusa](azure-netapp-files-understand-storage-hierarchy.md#qos_types) automatikus (automatikus), amely az alapértelmezett. Egy automatikus QoS-kapacitási készletben a rendszer automatikusan hozzárendeli az átviteli sebességet a készletben lévő kötetekhez, a kötetekhez rendelt mérethez viszonyítva.

* [LDAP-aláírás](azure-netapp-files-create-volumes-smb.md) (előzetes verzió)   

    Azure NetApp Files mostantól támogatja az LDAP-aláírást a biztonságos LDAP-keresésekhez a Azure NetApp Files szolgáltatás és a felhasználó által megadott Active Directory Domain Services tartományvezérlők között. Ez a szolgáltatás jelenleg előzetes kiadásban elérhető.

* [AES-titkosítás az ad-hitelesítéshez](azure-netapp-files-create-volumes-smb.md) (előzetes verzió)

    A Azure NetApp Files mostantól támogatja az AES-titkosítást a DC-hez való LDAP-kapcsolatok esetében, hogy engedélyezze az AES titkosítást az SMB-kötetek Ez a szolgáltatás jelenleg előzetes kiadásban elérhető. 

* Új [metrikák](azure-netapp-files-metrics.md):   

    * Új mennyiségi mérőszámok: 
        * *Kötet lefoglalt mérete*: kötet kiépített mérete
    * Új készlet metrikái: 
        * *Készlet lefoglalt mérete*: a készlet kiépített mérete 
        * *A készlethez tartozó pillanatképek teljes mérete*: a készlet összes kötetének pillanatkép-méretének összege

## <a name="july-2020"></a>2020. július

* [Dual-Protocol (NFSv3 és SMB) kötet](create-volumes-dual-protocol.md)

    Mostantól létrehozhat egy Azure NetApp Files kötetet, amely lehetővé teszi az egyidejű kétprotokollos (NFS v3 és SMB) hozzáférést az LDAP-felhasználók leképezésének támogatásával. Ez a funkció lehetővé teszi olyan használati esetek használatát, amelyekben a Linux-alapú számítási feladatok egy Azure NetApp Files köteten lévő adatok létrehozásához és tárolásához használhatók. Ugyanakkor a személyzetnek Windows-alapú ügyfeleket és szoftvereket kell használnia az újonnan generált adatok elemzéséhez ugyanarról a Azure NetApp Files kötetről. Az egyidejű kettős protokollú hozzáférési szolgáltatás eltávolítja a munkaterhelés által generált adatok egy különálló kötetre való másolásának szükségességét, amely az elemzés utáni, a tárolási díjak és a működési idő megtakarítására szolgáló eltérő protokollt tartalmaz. Ez a szolgáltatás díjmentes (a normál [Azure NetApp Files tárolási költségek](https://azure.microsoft.com/pricing/details/netapp/) továbbra is érvényesek), és általánosan elérhető. További információt az [egyidejű kettős protokoll hozzáférési dokumentációjában talál](create-volumes-dual-protocol.MD).

* [NFS v 4.1 Kerberos-titkosítás az átvitel során](configure-kerberos-encryption.MD)

    Azure NetApp Files mostantól támogatja az NFS-ügyfél titkosítását a Kerberos-üzemmódokban (krb5, krb5i és krb5p) AES-256 titkosítással, és további adatbiztonságot biztosít. Ez a szolgáltatás díjmentes (a normál [Azure NetApp Files tárolási költségek](https://azure.microsoft.com/pricing/details/netapp/) továbbra is érvényesek), és általánosan elérhető. További információt az [NFS v 4.1 Kerberos titkosítási dokumentációjában talál](configure-kerberos-encryption.MD).

* [Dinamikus kötet szolgáltatás szintjének változása](dynamic-change-volume-service-level.MD)

    A felhő rugalmasságot ígér az informatikai kiadások terén. Most már módosíthatja egy meglévő Azure NetApp Files kötet szolgáltatási szintjét úgy, hogy áthelyezi a kötetet egy másik, a kötethez használni kívánt szolgáltatási szintet használó kapacitási készletbe. Ennek a kötetnek a helyi szolgáltatási szintjének módosítása nem igényli az adatáttelepítést. Emellett nem befolyásolja az adatsíkon a kötethez való hozzáférést. Egy meglévő kötetet úgy módosíthat, hogy magasabb szolgáltatási szintet használjon a jobb teljesítmény érdekében, vagy alacsonyabb szolgáltatási szintet használjon a költség optimalizálásához. Ez a szolgáltatás díjmentes (a normál [Azure NetApp Files tárolási költségek](https://azure.microsoft.com/pricing/details/netapp/) továbbra is érvényesek), és jelenleg nyilvános előzetes verzióban érhető el. A szolgáltatás előzetes verziójának regisztrálásához kövesse a [dinamikus kötet szolgáltatás szintjének módosítására vonatkozó dokumentációt](dynamic-change-volume-service-level.md).

* [Mennyiségi pillanatkép-házirend](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies) (előzetes verzió) 

    Azure NetApp Files lehetővé teszi a kötetek időponthoz kötődő pillanatképek létrehozását. Most létrehozhat egy pillanatkép-szabályzatot, hogy Azure NetApp Files automatikusan hozzon létre mennyiségi pillanatképeket a választott gyakorisággal. A pillanatképeket óránként, naponta, hetente vagy havonta lehet ütemezni. Megadhatja azt is, hogy a pillanatkép-szabályzat részeként hány pillanatfelvételt kell megőrizni. Ez a szolgáltatás díjmentes (a normál [Azure NetApp Files tárolási költségek](https://azure.microsoft.com/pricing/details/netapp/) továbbra is érvényesek), és jelenleg előzetes verzióban érhető el. A funkció előzetes verziójának regisztrálásához kövesse a [mennyiségi pillanatkép-házirend dokumentációját](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies).

* [NFS gyökérszintű hozzáférés exportálási házirendje](azure-netapp-files-configure-export-policy.md)

    Azure NetApp Files mostantól megadhatja, hogy a főfiók hozzáférhet-e a kötethez. 

* [Pillanatkép elérési útjának elrejtése](azure-netapp-files-manage-snapshots.md#restore-a-file-from-a-snapshot-using-a-client)

    Azure NetApp Files mostantól megadhatja, hogy egy felhasználó láthatja-e a `.snapshot` csatlakoztatott köteten található könyvtárat (NFS-ügyfeleket) vagy `~snapshot` MAPPÁT (SMB-ügyfeleket).

## <a name="may-2020"></a>2020. május

* [Biztonsági mentési szabályzat felhasználói](create-active-directory-connections.md) (előzetes verzió)

    Azure NetApp Files lehetővé teszi olyan további fiókok hozzáadását, amelyek emelt szintű jogosultságokat igényelnek a Azure NetApp Fileshoz való használatra létrehozott számítógépfiók számára. A megadott fiókok a fájl vagy mappa szintjén módosíthatják az NTFS-engedélyeket. Megadhat például egy nem Kiemelt szolgáltatásfiók-fiókot, amely az adatáttelepítés során az SMB-fájlmegosztás Azure NetApp Files-ben való áttelepítésére szolgál. A biztonsági mentési házirend felhasználói szolgáltatás jelenleg előzetes verzióban érhető el.

## <a name="next-steps"></a>Következő lépések
* [Mi az az Azure NetApp Files?](azure-netapp-files-introduction.md)
* [Az Azure NetApp Files tárhely-hierarchiájának ismertetése](azure-netapp-files-understand-storage-hierarchy.md) 
