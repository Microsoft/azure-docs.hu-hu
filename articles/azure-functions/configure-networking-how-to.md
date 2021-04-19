---
title: Virtuális hálózat Azure Functions konfigurálása
description: Ez a cikk bemutatja, hogyan hajthat végre bizonyos virtuális hálózati feladatokat a Azure Functions.
ms.topic: conceptual
ms.date: 3/13/2021
ms.custom: template-how-to
ms.openlocfilehash: c123b20e163731f9a872a969f2f1564479b6e308
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718430"
---
# <a name="how-to-configure-azure-functions-with-a-virtual-network"></a>Virtuális hálózat Azure Functions konfigurálása

Ez a cikk bemutatja, hogyan végezhet el olyan feladatokat, amelyek a függvényalkalmazás virtuális hálózathoz való csatlakozásra és virtuális hálózaton való futtatására való konfigurálásával kapcsolatosak. A hálózati Azure Functions és hálózattal kapcsolatos további információkért lásd a Azure Functions [beállításokat.](functions-networking-options.md)

## <a name="restrict-your-storage-account-to-a-virtual-network"></a>A tárfiók korlátozása virtuális hálózatra 

Függvényalkalmazás létrehozásakor létre kell hoznia egy általános célú Azure Storage-fiókot, vagy hivatkozni kell rá, amely támogatja a Blob, Queue és Table Storage tárolókat. Ezt a tárfiókot lecserélheti egy szolgáltatásvégpontokkal vagy privát végpontokkal védett tárfiókra. 

> [!NOTE]  
> Ez a funkció jelenleg a Dedikált (App Service) csomag összes, Windows virtuális hálózat által támogatott SKUs és Prémium szintű csomag esetén működik. A használatban való használatra szánt csomag nem támogatott. 

Magánhálózatra korlátozott tárfiókkal működő függvény beállítása:

1. Olyan tárfiókkal rendelkező függvény létrehozása, amely nem rendelkezik engedélyezett szolgáltatásvégpontokkal.

1. Konfigurálja a függvényt a virtuális hálózathoz való csatlakozáshoz.

1. Hozzon létre vagy konfigurál egy másik tárfiókot.  Ez lesz a szolgáltatásvégpontokkal biztonságossá tetünk, és csatlakoztatjuk a függvényt.

1. [Hozzon létre egy fájlmegosztást](../storage/files/storage-how-to-create-file-share.md#create-a-file-share) a biztonságos tárfiókban.

1. Engedélyezze a szolgáltatásvégpontokat vagy privát végpontokat a tárfiókhoz.  
    * Privát végponti kapcsolatok használata esetén a tárfióknak szüksége lesz egy privát végpontra a és az `file` `blob` alerőforrásokhoz.  Bizonyos képességek, például a Durable Functions használata esetén privát végpontkapcsolaton keresztül is szüksége lesz és `queue` `table` elérhetőnek kell lennie.
    * Szolgáltatásvégpont használata esetén engedélyezze a függvényalkalmazások számára kijelölt alhálózatot a tárfiókok számára.

1. Másolja a fájl- és blobtartalmat a függvényalkalmazás tárfiókból a biztonságos tárfiókba és fájlmegosztásba.

1. Másolja ki a tárfiók kapcsolati sztringját.

1. Frissítse a **függvényalkalmazás Konfigurációja** alatti Alkalmazásbeállításokat a következőre: 

    | Beállítás neve | Érték | Megjegyzés |
    |----|----|----|
    | `AzureWebJobsStorage`| Storage kapcsolati sztring | Ez egy biztonságos tárfiók kapcsolati sztringe. |
    | `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` |  Storage kapcsolati sztring | Ez egy biztonságos tárfiók kapcsolati sztringe. |
    | `WEBSITE_CONTENTSHARE` | Fájlmegosztás | Annak a fájlmegosztásnak a neve, amely abban a biztonságos tárfiókban jött létre, ahol a projekttelepítési fájlok találhatók. |
    | `WEBSITE_CONTENTOVERVNET` | 1 | Új beállítás |
    | `WEBSITE_VNET_ROUTE_ALL` | 1 | Kényszeríti az összes kimenő forgalmat a virtuális hálózaton keresztül. Kötelező, ha a tárfiók privát végponti kapcsolatokat használ. |
    | `WEBSITE_DNS_SERVER` | `168.63.129.16` | Az alkalmazás által használt DNS-kiszolgáló. Kötelező, ha a tárfiók privát végponti kapcsolatokat használ. |

1. Az **alkalmazásbeállítások** mentéséhez válassza a Mentés lehetőséget. Az alkalmazásbeállítások módosítása az alkalmazás újraindítását okozza.  

A függvényalkalmazás az újraindítása után csatlakozik egy biztonságos tárfiókhoz.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Az Azure Functions hálózatkezelési lehetőségei](functions-networking-options.md)

