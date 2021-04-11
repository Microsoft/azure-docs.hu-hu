---
title: Hálózati fájlrendszer 3,0-támogatás az Azure Blob Storage-ban (előzetes verzió) | Microsoft Docs
description: A blob Storage mostantól támogatja a Network File System (NFS) 3,0 protokollt. Ez a támogatás lehetővé teszi, hogy a Linux-ügyfelek egy Azure-beli virtuális gépről (VM) vagy egy helyszíni rendszert futtató számítógépről csatlakoztassanak egy tárolót a blob Storage-tárolóban.
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 02/19/2021
ms.author: normesta
ms.reviewer: yzheng
ms.custom: references_regions
ms.openlocfilehash: 57dec4187ea42476ff1c0a993b751a7389da5639
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106490352"
---
# <a name="network-file-system-nfs-30-protocol-support-in-azure-blob-storage-preview"></a>Hálózati fájlrendszer (NFS) 3,0 protokoll támogatása az Azure Blob Storage-ban (előzetes verzió)

A blob Storage mostantól támogatja a Network File System (NFS) 3,0 protokollt. Ez a támogatás lehetővé teszi a Linux fájlrendszer kompatibilitását az objektum tárolási skálázási és díjszabási szolgáltatásával, és lehetővé teszi a Linux-ügyfelek számára, hogy egy Azure-beli virtuális gépről (VM) vagy egy helyszíni számítógépről csatlakoztatnak tárolót a blob Storage-ban. 

> [!NOTE]
> Az NFS 3,0 protokoll támogatása az Azure Blob Storage-ban nyilvános előzetes verzióban érhető el. A következő régiókban támogatja a standard szintű teljesítményű GPV2-tárolók használatát: Kelet-Ausztrália, Korea középső régiója, USA keleti régiója és az USA déli középső régiója. Az előzetes verzióban az összes nyilvános régióban a prémium szintű teljesítménnyel rendelkező Block blob is támogatott.

Mindig kihívást jelent a nagy léptékű örökölt számítási feladatok futtatása, például a nagy teljesítményű számítástechnika (HPC) a felhőben. Ennek egyik oka az, hogy az alkalmazások gyakran használnak olyan hagyományos protokollokat, mint az NFS vagy a Server Message Block (SMB) az adateléréshez. Emellett a natív felhőalapú tárolási szolgáltatások olyan objektum-tárterületre összpontosítanak, amely egy hierarchikus névteret és hatékony metaadat-műveleteket biztosító fájlrendszer helyett egyszerű névteret és kiterjedt metaadatokat tartalmaz. 

Blob Storage mostantól támogatja a hierarchikus névteret, és az NFS 3,0 protokoll támogatásával kombinálva az Azure sokkal egyszerűbbé teszi az örökölt alkalmazások futtatását a nagyméretű felhőalapú objektum-tárolók felett. 

## <a name="applications-and-workloads-suited-for-this-feature"></a>A szolgáltatáshoz illeszkedő alkalmazások és munkaterhelések

Az NFS 3,0 protokoll szolgáltatás a legjobb megoldás a nagy teljesítményű, nagy léptékű, nagy mennyiségű számítási feladatokhoz, például a médiafájlok feldolgozásához, a kockázati szimulációk és a genomikai feladatok előkészítéséhez. Érdemes ezt a funkciót bármilyen más típusú munkaterhelés használatára használni, amely több olvasót és sok szálat használ, ami nagy sávszélességet igényel. 

## <a name="nfs-30-and-the-hierarchical-namespace"></a>Az NFS 3,0 és a hierarchikus névtér

Az NFS 3,0 protokoll támogatásához a blobokat hierarchikus névtérbe kell rendezni. Egy hierarchikus névteret is engedélyezhet a Storage-fiók létrehozásakor. A hierarchikus névtér használatát Azure Data Lake Storage Gen2 vezette be. Objektumokat (fájlokat) rendez a címtárak és alkönyvtárak hierarchiájában, ugyanúgy, ahogy a számítógép fájlrendszere is meg van szervezve.  A hierarchikus névtér lineárisan méretezhető, és nem csökkenti az adatkapacitást vagy a teljesítményt. A különböző protokollok kiterjeszthetők a hierarchikus névtérből. Az NFS 3,0 protokoll az egyik ilyen elérhető protokoll.   

> [!div class="mx-imgBorder"]
> ![hierarchikus névtér](./media/network-protocol-support/hierarchical-namespace-and-nfs-support.png)
  
## <a name="data-stored-as-block-blobs"></a>Blokk blobként tárolt adattárolók

Ha engedélyezi az NFS 3,0 protokoll támogatását, a Storage-fiókban lévő összes érték blokk blobként lesz tárolva. A blokkos Blobok a nagy mennyiségű olvasási idejű adatok hatékony feldolgozására vannak optimalizálva. A blokkos Blobok tömbből állnak. Az egyes blokkokat egy blokk-azonosító azonosítja. A blokk Blobok akár 50 000 blokkot is tartalmazhatnak. A blokk-Blobok egyes blokkai eltérő méretűek lehetnek, a fiók által használt szolgáltatás verziójának maximális méretével.

Ha az alkalmazás az NFS 3,0 protokoll használatával kezdeményezi a kérést, a rendszer lefordítja a kérést a Blobok blokkolása műveletekkel együtt. Például az NFS 3,0 olvasási távoli eljáráshívási (RPC) kérelmeket a [blob](/rest/api/storageservices/get-blob) lekérése művelettel fordítja le. Az NFS 3,0 írási RPC-kérelmek lefordítása a [letiltási lista](/rest/api/storageservices/get-block-list), a [put blokk](/rest/api/storageservices/put-block)és a [blokkolási lista](/rest/api/storageservices/put-block-list)kombinációjával történik.

## <a name="general-workflow-mounting-a-storage-account-container"></a>Általános munkafolyamat: Storage-fiók tárolójának csatlakoztatása

A linuxos ügyfelei egy Azure-beli virtuális gépről (VM) vagy egy helyszíni számítógépről is csatlakoztathatók a blob Storage-tárolók. A Storage-fiók tárolójának csatlakoztatásához ezeket a dolgokat kell végrehajtania.

1. Regisztrálja az NFS 3,0 Protocol szolgáltatást az előfizetésével.

2. Ellenőrizze, hogy a szolgáltatás regisztrálva van-e.

3. Hozzon létre egy Azure-Virtual Network (VNet).

4. Konfigurálja a hálózati biztonságot.

5. Hozzon létre és konfiguráljon olyan Storage-fiókot, amely csak a VNet érkező forgalmat fogadja.

6. Hozzon létre egy tárolót a Storage-fiókban.

7. Csatlakoztassa a tárolót.

Részletes útmutatásért lásd: [blob Storage csatlakoztatása a hálózati fájlrendszer (NFS) 3,0 protokoll (előzetes verzió) használatával](network-file-system-protocol-support-how-to.md).

> [!IMPORTANT]
> Fontos, hogy ezeket a feladatokat sorrendben végezze el. Nem csatlakoztathatja a létrehozott tárolókat, mielőtt engedélyezi az NFS 3,0 protokollt a fiókjában. Továbbá, miután engedélyezte az NFS 3,0 protokollt a fiókjához, nem tilthatja le.

## <a name="network-security"></a>Hálózati biztonság

A VNet belül szerepelnie kell a Storage-fióknak. A VNet lehetővé teszi az ügyfelek számára, hogy biztonságosan kapcsolódjanak a Storage-fiókhoz. A fiókban tárolt adatvédelem egyetlen módja a VNet és más hálózati biztonsági beállítások használata. Az adatvédelmet, például a Azure Active Directory (AD) biztonságot és a hozzáférés-vezérlési listákat (ACL-eket) használó más eszközök még nem támogatják azokat a fiókokat, amelyeken engedélyezve van az NFS 3,0 protokoll támogatása. 

További információ: [hálózati biztonsági javaslatok a blob Storage](security-recommendations.md#networking)-hoz.

## <a name="supported-network-connections"></a>Támogatott hálózati kapcsolatok

Az ügyfél nyilvános vagy [privát végponton](../common/storage-private-endpoints.md)keresztül kapcsolódhat, és a következő hálózati helyekről csatlakozhat:

- A Storage-fiókhoz konfigurált VNet. 

  Ebben a cikkben a VNet *elsődleges VNet* fogunk hivatkozni. További információ: [hozzáférés engedélyezése egy virtuális hálózatról](../common/storage-network-security.md#grant-access-from-a-virtual-network).

- Olyan VNet, amely ugyanabban a régióban található, mint az elsődleges VNet.

  A megosztott VNet való hozzáférés engedélyezéséhez konfigurálnia kell a Storage-fiókot. További információ: [hozzáférés engedélyezése egy virtuális hálózatról](../common/storage-network-security.md#grant-access-from-a-virtual-network).

- Egy helyszíni hálózat, amely [VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md) vagy egy [ExpressRoute-átjáró](../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)használatával csatlakozik az elsődleges VNet. 

  További tudnivalókért lásd: [hozzáférés konfigurálása helyszíni hálózatokból](../common/storage-network-security.md#configuring-access-from-on-premises-networks).

- Egy olyan helyszíni hálózat, amely összekapcsolt hálózathoz csatlakozik.

  Ezt [VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md) vagy egy [ExpressRoute-átjáró](../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md) , valamint az [átjáró-átvitel](/azure/architecture/reference-architectures/hybrid-networking/vnet-peering#gateway-transit)használatával végezheti el. 

> [!IMPORTANT]
> Ha helyszíni hálózatról csatlakozik, győződjön meg arról, hogy az ügyfél engedélyezi a kimenő kommunikációt az 111-es és a 2048-es porton keresztül. Az NFS 3,0 protokoll ezeket a portokat használja.

## <a name="azure-storage-features-not-yet-supported"></a>Az Azure Storage szolgáltatásai még nem támogatottak

A következő Azure Storage-funkciók nem támogatottak, ha engedélyezi az NFS 3,0 protokollt a fiókjában. 

- Azure Active Directory (AD) biztonság

- POSIX – például hozzáférés-vezérlési listák (ACL-ek)

- Az NFS 3,0-támogatás engedélyezése meglévő Storage-fiókokon

- Lehetőség az NFS 3,0-támogatás letiltására egy Storage-fiókban (miután engedélyezte)

- A Blobok REST API-k vagy SDK-k használatával történő írására való képesség. 
  
## <a name="nfs-30-features-not-yet-supported"></a>Az NFS 3,0-funkciók még nem támogatottak

A következő NFS 3,0-funkciók még nem támogatottak.

- NFS 3,0 UDP-n keresztül. Csak a TCP protokollon keresztüli NFS 3,0 támogatott.

- Fájlok zárolása a Network Lock Managerrel (NLM). A csatlakoztatási parancsoknak tartalmazniuk kell a `-o nolock` paramétert.

- Alkönyvtárak csatlakoztatása. Csak a gyökérkönyvtár (tároló) csatlakoztatására van lehetőség.

- Csatlakoztatások listázása (például a parancs használatával `showmount -a` )

- Exportálások listázása (például: a parancs használatával `showmount -e` )

- Rögzített hivatkozás

- Tároló exportálása írásvédettként

## <a name="nfs-30-clients-not-yet-supported"></a>Az NFS 3,0-ügyfelek még nem támogatottak

A következő NFS 3,0-ügyfelek még nem támogatottak.

- Windows-ügyfél NFS-hez

## <a name="pricing"></a>Díjszabás

Az előzetes verzió ideje alatt a Storage-fiókban tárolt adatokra ugyanazon a kapacitási arányban kerül sor, mint a blob Storage-díj havi GB-onként. 

Az előzetes verzióban nem számítunk fel tranzakciót. A tranzakciók díjszabása változhat, és akkor lesz meghatározva, ha általánosan elérhető.

## <a name="next-steps"></a>Következő lépések

- Első lépésként tekintse meg [a blob Storage csatlakoztatása a hálózati fájlrendszer (NFS) 3,0 protokoll (előzetes verzió) használatával](network-file-system-protocol-support-how-to.md)című témakört.

- A teljesítmény optimalizálása érdekében tekintse [meg a hálózati fájlrendszer (NFS) 3,0 teljesítményével kapcsolatos szempontokat az Azure Blob Storage-ban (előzetes verzió)](network-file-system-protocol-support-performance.md).
