---
title: Azure NFS-fájlmegosztás csatlakoztatása – Azure Files
description: Ismerje meg, hogyan csatlakoztatható egy hálózati fájlrendszermegosztás.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/15/2021
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 4369619cd83dffe36cf156f523a951e1360438db
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717076"
---
# <a name="how-to-mount-an-nfs-file-share"></a>NFS-fájlmegosztás csatlakoztatása

Az [Azure Files](storage-files-introduction.md) a Microsoft könnyen használható felhőalapú fájlrendszere. Az Azure-fájlmegosztások Linux-disztribúciókban csatlakoztathatóak a Server Message Block protokoll (SMB) vagy a hálózati fájlrendszer (NFS) protokoll használatával. Ez a cikk az NFS-sel való csatlakoztatással kapcsolatos. Az SMB-csatlakoztatással kapcsolatos részletekért lásd: Use Azure Files with Linux (A virtuális gépek [használata Linux rendszeren).](storage-how-to-use-files-linux.md) Az egyes elérhető protokollokkal kapcsolatos részletekért lásd: [Azure-fájlmegosztási protokollok.](storage-files-compare-protocols.md)

## <a name="limitations"></a>Korlátozások

[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]

### <a name="regional-availability"></a>Regionális elérhetőség

[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

## <a name="prerequisites"></a>Előfeltételek

- [Hozzon létre egy NFS-megosztást.](storage-files-how-to-create-nfs-shares.md)

    > [!IMPORTANT]
    > Az NFS-megosztások csak megbízható hálózatokról érhetők el. Az NFS-megosztáshoz való csatlakozásnak az alábbi források egyikével kell származni:

- Használja az alábbi hálózati megoldások egyikét:
    - Hozzon [létre egy privát végpontot](storage-files-networking-endpoints.md#create-a-private-endpoint) (ajánlott), vagy korlátozza a [nyilvános végponthoz való hozzáférést.](storage-files-networking-endpoints.md#restrict-public-endpoint-access)
    - [Pont–hely (P2S) VPN konfigurálása Linux rendszeren](storage-files-configure-p2s-vpn-linux.md)az Azure Files.
    - [Konfigurálja a hely–hely VPN-t a](storage-files-configure-s2s-vpn.md)Azure Files.
    - Az [ExpressRoute konfigurálása.](../../expressroute/expressroute-introduction.md)

## <a name="disable-secure-transfer"></a>Biztonságos átvitel letiltása

1. Jelentkezzen be a Azure Portal és a létrehozott NFS-megosztást tartalmazó tárfiókhoz.
1. Válassza a **Konfiguráció** lehetőséget.
1. A **Biztonságos átvitelre** **van szükség beállításhoz válassza a Letiltva lehetőséget.**
1. Kattintson a **Mentés** gombra.

    :::image type="content" source="media/storage-files-how-to-mount-nfs-shares/storage-account-disable-secure-transfer.png" alt-text="Képernyőkép a tárfiók konfigurációs képernyőről a biztonságos átvitel letiltásával.":::

## <a name="mount-an-nfs-share"></a>NFS-megosztás csatlakoztatása

1. A fájlmegosztás létrehozása után válassza ki a megosztást, majd válassza a **Csatlakozás Linuxról lehetőséget.**
1. Adja meg a használni szeretne csatlakoztatási útvonalat, majd másolja ki a szkriptet.
1. Csatlakozzon az ügyfélhez, és használja a megadott csatlakoztatási szkriptet.

    :::image type="content" source="media/storage-files-how-to-create-mount-nfs-shares/mount-nfs-file-share-script.png" alt-text="Képernyőkép a fájlmegosztás csatlakoztatási panelről.":::

Csatlakoztatta az NFS-megosztást.

### <a name="validate-connectivity"></a>Kapcsolat ellenőrzése

Ha a csatlakoztatás sikertelen volt, lehetséges, hogy a privát végpont nem megfelelően lett beállítva, vagy nem érhető el. A kapcsolat megerősítésével kapcsolatos [](storage-files-networking-endpoints.md#verify-connectivity) részletekért tekintse meg a hálózati végpontok kapcsolatának ellenőrzése című cikket.

## <a name="next-steps"></a>Következő lépések

- Az üzembe helyezésről Azure Files a [Planning for an Azure Files deployment (Az](storage-files-planning.md)üzembe helyezés tervezése) Azure Files olvashat.
- Ha bármilyen problémát tapasztal, tekintse meg [az Azure NFS-fájlmegosztások hibaelhárítását bemutató témakört.](storage-troubleshooting-files-nfs.md)