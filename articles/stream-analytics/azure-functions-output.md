---
title: Azure Stream Analytics Azure Functions kimenete
description: Ez a cikk az Azure functions-t ismerteti Azure Stream Analytics kimenetként.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: e5ea7a1abbbd6ab4be32955179227fbd539cf641
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98019618"
---
# <a name="azure-functions-output-from-azure-stream-analytics"></a>Azure Stream Analytics Azure Functions kimenete

A Azure Functions egy kiszolgáló nélküli számítási szolgáltatás, amellyel igény szerint futtathat programkódokat anélkül, hogy explicit módon kellene kiépíteni vagy kezelni az infrastruktúrát. Lehetővé teszi az Azure-ban vagy a partneri szolgáltatásokban bekövetkező események által aktivált kódok megvalósítását. Azure Functions az eseményindítók megválaszolásának lehetősége a Azure Stream Analytics természetes kimenetét teszi lehetővé. Ez a kimeneti adapter lehetővé teszi, hogy a felhasználók a Azure Functionshoz csatlakozzanak Stream Analyticshoz, és egy parancsfájlt vagy programkódot futtassanak a különböző eseményekre adott válaszként.

Stream Analytics Azure Functions kimenete nem érhető el az Azure China 21Vianet és az Azure Germany (T-Systems International) régióiban. Egy virtuális hálózaton (VNet) belüli, több-bérlős fürtön futó Stream Analytics-feladatokból való Azure Functions-kapcsolódás szintén nem támogatott.

A Azure Stream Analytics HTTP-eseményindítók használatával hívja meg Azure Functions. A Azure Functions kimeneti adapter a következő konfigurálható tulajdonságokkal érhető el:

| Tulajdonság neve | Description |
| --- | --- |
| Függvényalkalmazás |A Azure Functions alkalmazás neve. |
| Függvény |A függvény neve a Azure Functions alkalmazásban. |
| Kulcs |Ha egy másik előfizetésből származó Azure-függvényt szeretne használni, ezt megteheti a függvény eléréséhez szükséges kulcs megadásával. |
| Köteg maximális mérete |Olyan tulajdonság, amely lehetővé teszi az Azure-függvénynek elküldhető kimeneti kötegek maximális méretének beállítását. A bemeneti egység bájtban van. Alapértelmezés szerint ez az érték 262 144 bájt (256 KB). |
| Kötegek maximális száma  |Olyan tulajdonság, amely lehetővé teszi, hogy megadhatja az egyes kötegekben a Azure Functionsba küldendő események maximális számát. Az alapértelmezett érték 100. |

A Azure Stream Analytics a sikeresen feldolgozott kötegekhez tartozó functions alkalmazásból 200 HTTP-állapotot vár.

Ha a Azure Stream Analytics egy Azure-függvénytől egy 413-es ("http-kérelem entitás túl nagy") kivételt kap, akkor csökkenti a Azure Functions számára küldött kötegek méretét. Az Azure-függvény kódjában használja ezt a kivételt annak biztosításához, hogy Azure Stream Analytics ne küldjön túlméretes kötegeket. Győződjön meg arról is, hogy a függvényben használt kötegek maximális száma és mérete konzisztens a Stream Analytics portálon megadott értékekkel.

> [!NOTE]
> A tesztelési kapcsolat során a Stream Analytics egy üres köteget küld Azure Functionsnak, hogy tesztelje, hogy a két működik-e a kapcsolat. Győződjön meg arról, hogy a functions alkalmazás az üres batch-kérelmeket kezeli, hogy ellenőrizze a kapcsolatok ellenőrzésének menetét.

Abban az esetben is, ha egy időablakban nincs esemény kirakodás, a rendszer nem generál kimenetet. Ennek eredményeképpen a **computeResult** függvény nem lett meghívva. Ez a viselkedés konzisztens a beépített ablakos összesítő függvényekkel.

## <a name="partitioning"></a>Particionálás

A partíciós kulcs a lekérdezés PARTITION BY záradékán alapul. A kimeneti írók száma követi a bemeneti particionálást a [teljesen párhuzamos lekérdezéseknél](stream-analytics-scale-jobs.md).

## <a name="output-batch-size"></a>Kimeneti köteg mérete

Az alapértelmezett köteg mérete 262 144 bájt (256 KB). Az események alapértelmezett száma/batch értéke 100. A köteg mérete konfigurálható, és a Stream Analytics kimeneti beállításokban növelheti vagy csökkenthető.

## <a name="next-steps"></a>Következő lépések

* [Útmutató: Stream Analytics-feladat létrehozása az Azure Portal használatával](stream-analytics-quick-create-portal.md)
* [Gyors útmutató: Azure Stream Analytics-feladatok létrehozása az Azure CLI használatával](quick-create-azure-cli.md)
* [Gyors útmutató: Azure Stream Analytics-feladatok létrehozása ARM-sablon használatával](quick-create-azure-resource-manager.md)
* [Gyors útmutató: Stream Analytics-feladatok létrehozása Azure PowerShell használatával](stream-analytics-quick-create-powershell.md)
* [Rövid útmutató: Azure Stream Analytics-feladatok létrehozása a Visual Studio használatával](stream-analytics-quick-create-vs.md)
* [Rövid útmutató: Azure Stream Analytics-feladatok létrehozása a Visual Studio Code-ban](quick-create-visual-studio-code.md)