---
title: Azure Functions konfigurálása virtuális hálózattal
description: Ebből a cikkből megtudhatja, hogyan hajthat végre bizonyos virtuális hálózati feladatokat a Azure Functionshoz.
ms.topic: conceptual
ms.date: 3/13/2021
ms.custom: template-how-to
ms.openlocfilehash: a28a59a0de40bba7914d1920b42034fbbc223ddc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104609222"
---
# <a name="how-to-configure-azure-functions-with-a-virtual-network"></a>Azure Functions konfigurálása virtuális hálózattal

Ez a cikk bemutatja, hogyan hajthat végre olyan feladatokat, amelyek segítségével konfigurálhatja a Function app-t egy virtuális hálózaton való kapcsolódáshoz és futtatáshoz. Ha többet szeretne megtudni a Azure Functions és a hálózatkezelésről, tekintse meg a [Azure functions hálózati beállítások](functions-networking-options.md)című témakört.

## <a name="restrict-your-storage-account-to-a-virtual-network"></a>A Storage-fiók korlátozása virtuális hálózatra 

Egy Function-alkalmazás létrehozásakor létre kell hoznia egy általános célú Azure Storage-fiókot, amely támogatja a blobot, a várólistát és a Table Storage-t. Ezt a Storage-fiókot lecserélheti egy, a szolgáltatási végpontokkal vagy privát végponttal védett tárolóval. 

> [!NOTE]  
> Ez a funkció jelenleg az összes Windows Virtual Network által támogatott SKU-t támogatja a dedikált (App Service) tervben és a prémium csomagokban. A felhasználási terv nem támogatott. 

Egy privát hálózatra korlátozódó Storage-fiókkal rendelkező függvény beállítása:

1. Hozzon létre egy olyan függvényt, amely nem rendelkezik engedélyezett szolgáltatási végpontokkal.

1. Konfigurálja a függvényt a virtuális hálózathoz való kapcsolódáshoz.

1. Hozzon létre vagy konfiguráljon egy másik Storage-fiókot.  Ez lesz az a Storage-fiók, amelyet a szolgáltatási végpontok biztosítanak, és összekapcsolhatjuk a funkciót.

1. [Hozzon létre egy fájlmegosztást](../storage/files/storage-how-to-create-file-share.md#create-file-share) a biztonságos Storage-fiókban.

1. Engedélyezze a szolgáltatási végpontokat vagy a magánhálózati végpontot a Storage-fiókhoz.  
    * Privát végponti kapcsolatok használata esetén a Storage-fióknak szüksége lesz egy privát végpontra a `file` és az `blob` alerőforrásokhoz.  Ha bizonyos képességeket (például Durable Functions) használ, `queue` `table` egy privát végponti kapcsolaton keresztül is szüksége lesz rá, és elérhetővé válik.
    * Ha szolgáltatási végpontokat használ, engedélyezze a Function apps számára dedikált alhálózatot a Storage-fiókokhoz.

1. Másolja a fájl és a blob tartalmát a Function app Storage-fiókból a biztonságos Storage-fiókba és a fájlmegosztásba.

1. Másolja ki a Storage-fiókhoz tartozó kapcsolatok karakterláncát.

1. Frissítse az **alkalmazás beállításait** a Function alkalmazás **konfigurációjában** a következőre:

    | Beállítás neve | Érték | Megjegyzés |
    |----|----|----|
    | `AzureWebJobsStorage`| Storage-kapcsolatok karakterlánca | A biztonságos Storage-fiókhoz tartozó kapcsolatok karakterlánca. |
    | `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` |  Storage-kapcsolatok karakterlánca | A biztonságos Storage-fiókhoz tartozó kapcsolatok karakterlánca. |
    | `WEBSITE_CONTENTSHARE` | Fájlmegosztás | A Project telepítési fájljait tároló biztonságos Storage-fiókban létrehozott fájlmegosztás neve. |
    | `WEBSITE_CONTENTOVERVNET` | 1 | Új beállítás |
    | `WEBSITE_VNET_ROUTE_ALL` | 1 | Kikényszeríti az összes kimenő forgalmat a virtuális hálózaton. Kötelező, ha a Storage-fiók privát végponti kapcsolatokat használ. |
    | `WEBSITE_DNS_SERVER` | `168.63.129.16` | Az alkalmazás által használt DNS-kiszolgáló. Kötelező, ha a Storage-fiók privát végponti kapcsolatokat használ. |

1. Az Alkalmazásbeállítások mentéséhez válassza a **Mentés** lehetőséget. Az Alkalmazásbeállítások módosítása az alkalmazás újraindítását okozza.  

A Function app újraindítása után már csatlakoztatva van egy biztonságos Storage-fiókhoz.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Az Azure Functions hálózatkezelési lehetőségei](functions-networking-options.md)

