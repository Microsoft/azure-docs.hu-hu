---
title: Azure NFS-fájlmegosztás csatlakoztatása – Azure Files
description: Megtudhatja, hogyan csatlakoztathat hálózati fájlrendszer-megosztást.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2020
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 8a993d9c1de35132198de5e3becc4f16d6a2a437
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96621297"
---
# <a name="how-to-mount-an-nfs-file-share"></a>NFS-fájlmegosztás csatlakoztatása

Az [Azure Files](storage-files-introduction.md) a Microsoft könnyen használható felhőalapú fájlrendszere. Az Azure-fájlmegosztás Linux-disztribúciókban is csatlakoztatható az SMB vagy a Network File System (NFS) protokoll használatával. Ez a cikk az NFS-sel való csatlakoztatásra koncentrál, az SMB-vel való csatlakoztatás részletes ismertetését lásd: a [Azure Files használata Linux rendszeren](storage-how-to-use-files-linux.md). A rendelkezésre álló protokollok részletes ismertetését lásd: [Azure fájlmegosztás protokollok](storage-files-compare-protocols.md).

## <a name="limitations"></a>Korlátozások

[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]

### <a name="regional-availability"></a>Regionális elérhetőség

[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

## <a name="prerequisites"></a>Előfeltételek

- [Hozzon létre egy NFS-megosztást](storage-files-how-to-create-nfs-shares.md).

    > [!IMPORTANT]
    > Az NFS-megosztások csak megbízható hálózatokból érhetők el. Az NFS-megosztással létesített kapcsolatoknak az alábbi források egyikéből kell származnia:

- Használja az alábbi hálózati megoldások egyikét:
    - [Hozzon létre egy privát végpontot](storage-files-networking-endpoints.md#create-a-private-endpoint) (ajánlott), vagy [korlátozza a hozzáférést a nyilvános végponthoz](storage-files-networking-endpoints.md#restrict-public-endpoint-access).
    - [Pont – hely (P2S) VPN konfigurálása Linux rendszeren a Azure Fileshoz való használathoz](storage-files-configure-p2s-vpn-linux.md).
    - [Helyek közötti VPN konfigurálása Azure Fileshoz való használatra](storage-files-configure-s2s-vpn.md).
    - Konfigurálja a [ExpressRoute](../../expressroute/expressroute-introduction.md).

## <a name="disable-secure-transfer"></a>Biztonságos átvitel letiltása

1. Jelentkezzen be a Azure Portalba, és nyissa meg a létrehozott NFS-megosztást tartalmazó Storage-fiókot.
1. Válassza a **Konfiguráció** lehetőséget.
1. Válassza a **Letiltva** a **biztonságos átvitelhez szükséges** lehetőséget.
1. Kattintson a **Mentés** gombra.

    :::image type="content" source="media/storage-files-how-to-mount-nfs-shares/storage-account-disable-secure-transfer.png" alt-text="Képernyőfelvétel a Storage-fiók konfigurációs képernyőjéről a biztonságos átvitel letiltásával.":::

## <a name="mount-an-nfs-share"></a>NFS-megosztás csatlakoztatása

1. A fájlmegosztás létrehozása után válassza ki a megosztást, és válassza a **kapcsolat Linuxon** lehetőséget.
1. Adja meg a használni kívánt csatlakoztatási útvonalat, majd másolja a szkriptet.
1. Kapcsolódjon az ügyfélhez, és használja a megadott csatlakoztatási parancsfájlt.

    :::image type="content" source="media/storage-files-how-to-create-mount-nfs-shares/mount-nfs-file-share-script.png" alt-text="Képernyőfelvétel a fájlmegosztás összekapcsolási paneljéről":::

Ezzel csatlakoztatta az NFS-megosztást.

### <a name="validate-connectivity"></a>Kapcsolat ellenőrzése

Ha a csatlakoztatás sikertelen volt, lehetséges, hogy a saját végpontja nem lett megfelelően beállítva, vagy nem érhető el. A kapcsolat megerősítéséről a hálózatkezelési végpontok című cikk [kapcsolat ellenőrzése](storage-files-networking-endpoints.md#verify-connectivity) című szakaszában talál további információt.

## <a name="next-steps"></a>Következő lépések

- További információ a Azure Filesről a cikkhez, [Azure Files központi telepítés megtervezéséhez](storage-files-planning.md).
- Ha problémákat tapasztal, tekintse meg az [Azure NFS-fájlmegosztás hibaelhárítása](storage-troubleshooting-files-nfs.md)című témakört.