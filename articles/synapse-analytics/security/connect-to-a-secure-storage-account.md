---
title: Kapcsolódás biztonságos Storage-fiókhoz az Azure szinapszis-munkaterületről
description: Ez a cikk bemutatja, hogyan csatlakozhat biztonságos Storage-fiókhoz az Azure szinapszis-munkaterületről
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 02/10/2021
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 5d43d6f56b48a34fa34baf727508ad8f1c151aa7
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101674311"
---
# <a name="connect-to-a-secure-azure-storage-account-from-your-synapse-workspace"></a>Kapcsolódás biztonságos Azure Storage-fiókhoz a szinapszis-munkaterületről

Ez a cikk bemutatja, hogyan csatlakozhat biztonságos Azure Storage-fiókhoz az Azure szinapszis-munkaterületről. A munkaterület létrehozásakor egy Azure Storage-fiókot kapcsolhat a szinapszis-munkaterülethez. A munkaterület létrehozása után további Storage-fiókokat is csatolhat.


## <a name="secured-azure-storage-accounts"></a>Biztonságos Azure Storage-fiókok
Az Azure Storage többrétegű biztonsági modellt biztosít, amely lehetővé teszi a Storage-fiókok hozzáférésének védelmét és szabályozását. Beállíthatja, hogy az IP-tűzfalszabályok a kiválasztott nyilvános IP-címtartományok forgalmát biztosítsák a Storage-fiókhoz. A hálózati szabályokat úgy is konfigurálhatja, hogy a kiválasztott virtuális hálózatoktól érkező adatforgalmat biztosítson a Storage-fiókhoz. A kiválasztott IP-címtartományok és hálózati szabályok hozzáférését engedélyező IP-tűzfalszabályok lehetővé teszik, hogy ugyanazon a Storage-fiókon keresztül hozzáférjenek a kiválasztott virtuális hálózatokhoz. Ezek a szabályok a Storage-fiók nyilvános végpontján érvényesek. Nincs szükség hozzáférési szabályokra, hogy engedélyezze a munkaterületen létrehozott felügyelt privát végpontok forgalmát egy Storage-fiókba. A tárolási tűzfalszabályok a meglévő Storage-fiókokra, illetve a létrehozásuk során új Storage-fiókokra is alkalmazhatók. A Storage-fiók védelméről [itt](../../storage/common/storage-network-security.md)olvashat bővebben.

## <a name="synapse-workspaces-and-virtual-networks"></a>Szinapszis-munkaterületek és virtuális hálózatok
A szinapszis munkaterületek létrehozásakor engedélyezheti a felügyelt virtuális hálózatok társítását. Ha nem engedélyezi a felügyelt virtuális hálózatot a munkaterülethez a létrehozásakor, a munkaterület egy megosztott virtuális hálózatban található, valamint olyan egyéb szinapszis-munkaterületekkel, amelyek nem rendelkeznek felügyelt virtuális hálózattal. Ha engedélyezte a felügyelt virtuális hálózatot a munkaterület létrehozásakor, a munkaterület az Azure szinapszis által kezelt dedikált virtuális hálózattal van társítva. Ezek a virtuális hálózatok nem az ügyfél-előfizetésben jönnek létre. Ezért ezen virtuális hálózatoktól nem fog tudni forgalmat biztosítani a biztonságos Storage-fiókhoz a fent ismertetett hálózati szabályok használatával.  

## <a name="access-a-secured-storage-account"></a>Biztonságos Storage-fiók elérése
A szinapszis olyan hálózatokból működik, amelyek nem vehetők fel a hálózati szabályokba. A következő lépéseket kell végrehajtani a munkaterület biztonságos Storage-fiókjához való hozzáférésének engedélyezéséhez.

* Hozzon létre egy Azure szinapszis-munkaterületet egy hozzá társított felügyelt virtuális hálózattal, és hozzon létre felügyelt privát végpontokat a biztonságos Storage-fiókba
* Megbízható Azure-szolgáltatásként biztosíthatja az Azure szinapszis-munkaterület hozzáférését a biztonságos Storage-fiókhoz. Megbízható szolgáltatásként az Azure szinapszis ezt követően erős hitelesítést használ a Storage-fiókhoz való biztonságos kapcsolódáshoz.   

### <a name="create-a-synapse-workspace-with-a-managed-virtual-network-and-create-managed-private-endpoints-to-your-storage-account"></a>Szinapszis-munkaterület létrehozása felügyelt virtuális hálózattal és felügyelt privát végpontok létrehozása a Storage-fiókhoz
Az [alábbi lépéseket](./synapse-workspace-managed-vnet.md) követve létrehozhat egy olyan szinapszis-munkaterületet, amelyhez hozzá van rendelve egy felügyelt virtuális hálózat. Miután létrehozta a munkaterületet egy társított felügyelt virtuális hálózattal, létrehozhat egy felügyelt privát végpontot a biztonságos Storage-fiókhoz az [itt](./how-to-create-managed-private-endpoints.md)felsorolt lépéseket követve. 

### <a name="grant-your-azure-synapse-workspace-access-to-your-secure-storage-account-as-a-trusted-azure-service"></a>Azure-beli szinapszis-munkaterület biztosítása megbízható Azure-szolgáltatásként való hozzáféréshez a biztonságos Storage-fiókhoz
Az analitikai képességek, például a dedikált SQL-készlet és a kiszolgáló nélküli SQL-készlet olyan több-bérlős infrastruktúrát használnak, amely nincs telepítve a felügyelt virtuális hálózaton. Ahhoz, hogy az ezektől a funkcióktól érkező forgalom hozzáférjen a biztonságos Storage-fiókhoz, a munkaterület rendszer által hozzárendelt felügyelt identitása alapján be kell állítania a Storage-fiók elérését az alábbi lépésekkel.

A Azure Portalban navigáljon a biztonságos Storage-fiókhoz. Válassza a **hálózatkezelés** lehetőséget a bal oldali navigációs ablaktáblán. Az **erőforrás-példányok** szakaszban válassza ki a *Microsoft. szinapszis/munkaterület* elemet az **erőforrástípus** mezőben, majd adja meg a munkaterület nevét a **példány neveként**. Kattintson a **Mentés** gombra.

![Storage-fiók hálózati konfigurációja.](./media/connect-to-a-secure-storage-account/secured-storage-access.png)

Ekkor hozzáférhet a biztonságos Storage-fiókhoz a munkaterületről.


## <a name="next-steps"></a>Következő lépések

További információ a [felügyelt munkaterület virtuális hálózatáról](./synapse-workspace-managed-vnet.md).

További információ a [felügyelt privát végpontokról](./synapse-workspace-managed-private-endpoints.md).