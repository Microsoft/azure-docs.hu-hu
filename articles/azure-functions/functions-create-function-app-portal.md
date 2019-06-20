---
title: Függvényalkalmazás létrehozása az Azure Portal használatával | Microsoft Docs
description: Új függvényalkalmazás létrehozása a portálon Azure App Service használatával.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: quickstart
ms.date: 04/11/2017
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: ad9c50953447c1effee48eec5b0cb9f64386e6cc
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2019
ms.locfileid: "67155575"
---
# <a name="create-a-function-app-from-the-azure-portal"></a>Függvényalkalmazás létrehozása az Azure Portal használatával

Az Azure Function Apps az Azure App Service infrastruktúráját használja. Ez a témakör bemutatja, hogyan hozhat létre függvényalkalmazást az Azure Portalon. A függvényalkalmazás olyan tároló, amely a különálló függvények végrehajtását futtatja. Ha létrehoz egy függvényalkalmazást az App Service szolgáltatási csomagjában, a függvényalkalmazása az App Service összes szolgáltatását használhatja.

## <a name="create-a-function-app"></a>Függvényalkalmazás létrehozása

[!INCLUDE [functions-create-function-app-portal](../../includes/functions-create-function-app-portal.md)]

Ha függvényalkalmazást hoz létre, ügyeljen arra, hogy érvényes **alkalmazásnevet** adjon meg, amely csak betűket, számokat és kötőjelet tartalmazhat. Az aláhúzás ( **_** ) nem engedélyezett karakter.

A tárfiókok neve 3–24 karakter hosszúságú lehet, és csak számokból és kisbetűkből állhat. A tárfiók nevének egyedinek kell lennie az Azure rendszerben. 

A függvényalkalmazás létrehozása után létrehozhatja a különálló függvényeket egy vagy több nyelven. A [portál használatával](functions-create-first-azure-function.md#create-function), [folyamatos üzembe helyezéssel](functions-continuous-deployment.md), vagy [FTP-s feltöltéssel](https://github.com/projectkudu/kudu/wiki/Accessing-files-via-ftp) is létrehozhatja a függvényeket.

## <a name="service-plans"></a>Szolgáltatáscsomagok

Az Azure Functions két különböző szolgáltatáscsomaggal rendelkezik: Használatalapú és App Service-csomag. A használatalapú csomag automatikusan foglalja le a számítási teljesítményt a kód futtatásakor: szükség szerint horizontális felskálázást végez a terhelésnek megfelelően, majd horizontális leskálázást hajt végre, ha a kód nem fut. Az App Service-csomag az App Service minden szolgáltatásához hozzáférést nyújt a függvényalkalmazásnak. A szolgáltatáscsomagot a függvényalkalmazás létrehozásakor kell kiválasztania, és a módosítása jelenleg nem lehetséges. További információ: [Azure Functions szolgáltatási csomag kiválasztása](functions-scale.md).

Ha JavaScript-függvények futtatását tervezi egy App Service-csomagon, kevesebb maggal rendelkező csomagot érdemes választania. További információ: [JavaScript-referencia a Functionshöz](functions-reference-node.md#choose-single-vcpu-app-service-plans).

<a name="storage-account-requirements"></a>

## <a name="storage-account-requirements"></a>Storage-fiókra vonatkozó követelmények

Ha függvényalkalmazást hoz létre az App Service-ben, létre kell hoznia egy általános célú Azure Storage-fiókot, amely támogatja a blobok, az üzenetsorok és a táblák tárolását, vagy hivatkoznia kell egy ilyen Storage-fiókra. A Functions a Storage-ot használja olyan belső műveletekre, mint például az eseményindítók kezelése és a függvénykivételek naplózása. Egyes Storage-fiókok, mint például a csak blobok tárolására alkalmas tárfiókok, az Azure Premium Storage és a ZRS-replikációval rendelkező általános célú tárolófiókok nem támogatják az üzenetsorokat és a táblákat. Ezek a fiókok nem jelennek meg a Storage-fiók panelen a függvényalkalmazások létrehozásakor.

>[!NOTE]
>A használatalapú szolgáltatási csomag használatakor a rendszer az Azure File Storage a fő tárfiókjában tárolja a függvénykódot és a kötéskonfigurációs fájlokat. Ha törli ezt a fő tárfiókot, ez a tartalom is törlődik, és nem állítható helyre.

További információ a tárfiókok típusairól: [Az Azure Storage szolgáltatásainak bemutatása](../storage/common/storage-introduction.md#azure-storage-services). 

## <a name="next-steps"></a>További lépések

Bár az Azure portal segítségével egyszerűen hozhat létre, és próbálja ki a Funkciók, javasoljuk, hogy [helyi fejlesztési](functions-develop-local.md). Függvényalkalmazás létrehozása a portálon, után továbbra is szeretné függvény hozzáadása. 

> [!div class="nextstepaction"]
> [Egy HTTP által aktivált függvény hozzáadásához](functions-create-first-azure-function.md#create-function)
