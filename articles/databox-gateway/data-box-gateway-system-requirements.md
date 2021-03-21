---
title: Microsoft Azure Data Box Gateway rendszerkövetelményei | Microsoft Docs
description: Tudnivalók a Azure Data Box Gateway szoftveres és hálózati követelményeiről
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/01/2021
ms.author: alkohli
ms.openlocfilehash: e7c8653b39a3e0333ff6e98783a6e9a1437dba22
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101739211"
---
# <a name="azure-data-box-gateway-system-requirements"></a>Rendszerkövetelmények Azure Data Box Gateway

Ez a cikk a Microsoft Azure Data Box Gateway megoldás és a Azure Data Box Gatewayhoz csatlakozó ügyfelek fontos rendszerkövetelményeit ismerteti. Javasoljuk, hogy körültekintően tekintse át az információkat, mielőtt üzembe helyezi a Data Box Gateway, majd az üzembe helyezés és az azt követő művelet során szükség szerint visszatekinti azt. 

A Data Box Gateway virtuális eszköz rendszerkövetelményei a következők:

- A **gazdagépek szoftveres követelményei** – ismerteti a támogatott platformokat, a helyi konfiguráció felhasználói felületének böngészőit, az SMB-ügyfeleket, valamint az eszközhöz csatlakozó gazdagépek további követelményeit.
- **Az eszköz hálózati követelményei** – információt nyújt a virtuális eszköz működésével kapcsolatos hálózati követelményekről.


## <a name="specifications-for-the-virtual-device"></a>A virtuális eszköz specifikációi

A Data Box Gateway mögöttes gazdagéprendszer képes a következő erőforrások kiosztására a virtuális eszköz kiépítése érdekében:

| Specifikációk                                          | Description              |
|---------------------------------------------------------|--------------------------|
| Virtuális processzorok (magok)   | Minimum 4 |
| Memória  | Legalább 8 GB. Legalább 16 GB-ot javasoljuk. |
| Rendelkezésre állás|Egyetlen csomópont|
| Lemezek| Operációsrendszer-lemez: 250 GB <br> Adatlemez: legalább 2 TB, dinamikusan kiosztott és SSD-másolatokkal kiegészített|
| Hálózati adapterek|1 vagy több virtuális hálózati adapter|


## <a name="supported-os-for-clients-connected-to-device"></a>Az eszközhöz csatlakoztatott ügyfelek támogatott operációs rendszere

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>Az eszközt elérő ügyfelek által támogatott protokollok

[!INCLUDE [Supported protocols for clients accessing device](../../includes/data-box-edge-gateway-supported-client-protocols.md)]

## <a name="supported-virtualization-platforms-for-device"></a>Az eszköz által támogatott virtualizációs platformok

| **Operációs rendszer/platform**  |**Versions** (Verziók)   |**Megjegyzések**  |
|---------|---------|---------|
|Hyper-V  |  2012 R2 <br> 2016 <br> 2019 |         |
|VMware ESXi     | 6.0 <br> 6.5 <br> 6.7       |A VMware-eszközök nem támogatottak.         |


## <a name="supported-storage-accounts"></a>Támogatott tárfiókok

[!INCLUDE [Supported storage accounts](../../includes/data-box-edge-gateway-supported-storage-accounts.md)]


## <a name="supported-storage-types"></a>Támogatott tárolótípusok

[!INCLUDE [Supported storage types](../../includes/data-box-edge-gateway-supported-storage-types.md)]

## <a name="supported-browsers-for-local-web-ui"></a>A helyi webes felhasználói felület által támogatott böngészők

[!INCLUDE [Supported browsers for local web UI](../../includes/data-box-edge-gateway-supported-browsers.md)]

## <a name="networking-port-requirements"></a>Hálózati portokra vonatkozó követelmények

A következő táblázat felsorolja azokat a portokat, amelyeket meg kell nyitni a tűzfalon az SMB-, felhő-vagy felügyeleti forgalom engedélyezéséhez. A (z)  vagy a *bejövő* tábla a bejövő ügyfél által az eszközhöz való hozzáférést kérő irányt jelöli. A *kimenő vagy kimenő* *állapot arra utal* , hogy a Data Box Gateway-eszköz hogyan küldi el az adatokat külsőleg, az üzembe helyezésen kívül: például az internet felé.

[!INCLUDE [Port configuration for device](../../includes/data-box-edge-gateway-port-config.md)]

## <a name="url-patterns-for-firewall-rules"></a>Tűzfalszabályok URL-mintái

A hálózati rendszergazdák gyakran konfigurálhatják a speciális tűzfalszabályok alapján a bejövő és a kimenő forgalom szűrésére szolgáló URL-mintákat. A Data Box Gateway eszköz és a Data Box Gateway szolgáltatás más Microsoft-alkalmazásokkal (például Azure Service Bus, Azure Active Directory Access Control, Storage-fiókokkal és Microsoft Update-kiszolgálókkal függ. Az ezekhez az alkalmazásokhoz társított URL-mintákat a tűzfalszabályok konfigurálására lehet használni. Fontos megérteni, hogy az alkalmazásokhoz társított URL-minták megváltoztathatók. Ehhez a hálózati rendszergazdának szüksége lesz a Data Box Gateway tűzfalszabályok figyelésére és frissítésére, ahogy és amikor szükséges.

Javasoljuk, hogy a legtöbb esetben a Data Box Gateway rögzített IP-címek alapján állítsa be a tűzfal szabályait a kimenő forgalomra vonatkozóan. Az alábbi információk segítségével azonban megadhatja a biztonságos környezetek létrehozásához szükséges speciális tűzfalszabályok beállításait is.

> [!NOTE]
> - Az eszköz (forrás) IP-címeit mindig az összes felhőalapú hálózati adapterre kell beállítani.
> - A cél IP-címeket az [Azure Datacenter IP-címtartományok](https://www.microsoft.com/download/confirmation.aspx?id=41653)értékre kell beállítani.

[!INCLUDE [URL patterns for firewall](../../includes/data-box-edge-gateway-url-patterns-firewall.md)]

### <a name="url-patterns-for-azure-government"></a>A Azure Government URL-mintái

[!INCLUDE [Azure Government URL patterns for firewall](../../includes/data-box-edge-gateway-gov-url-patterns-firewall.md)]

## <a name="internet-bandwidth"></a>Internetes sávszélesség

[!INCLUDE [Internet bandwidth](../../includes/data-box-edge-gateway-internet-bandwidth.md)]

## <a name="next-step"></a>Következő lépés

* [A Azure Data Box Gateway üzembe helyezése](data-box-gateway-deploy-prep.md)

