---
title: Webszolgáltatás-végpontok létrehozása
titleSuffix: ML Studio (classic) - Azure
description: Webszolgáltatás-végpontok létrehozása Azure Machine Learning Studio (klasszikus). A webszolgáltatás minden végpontja független módon van kezelve, szabályozva és felügyelve.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.custom: seodec18
ms.date: 02/15/2019
ms.openlocfilehash: 618f2e15a2f4acf8ac397872b33aaa98f0b5c4d8
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77149915"
---
# <a name="create-endpoints-for-deployed-azure-machine-learning-studio-classic-web-services"></a>Végpontok létrehozása központilag telepített Azure Machine Learning Studio (klasszikus) webszolgáltatásokhoz

> [!NOTE]
> Ez a témakör a **klasszikus** Machine learning webszolgáltatásokra vonatkozó technikákat ismerteti.

A webszolgáltatások telepítése után egy alapértelmezett végpont kerül létrehozásra az adott szolgáltatáshoz. Az alapértelmezett végpont az API-kulcs használatával hívható meg. A webszolgáltatások portálján több végpontot is hozzáadhat a saját kulcsaihoz.
A webszolgáltatás minden végpontja független módon van kezelve, szabályozva és felügyelve. Mindegyik végpont egy egyedi URL-cím, amely egy olyan engedélyezési kulccsal rendelkezik, amelyet továbbíthat az ügyfeleknek.

## <a name="add-endpoints-to-a-web-service"></a>Végpontok hozzáadása egy webszolgáltatáshoz

A Azure Machine Learning webszolgáltatások portál használatával hozzáadhat egy végpontot egy webszolgáltatáshoz. A végpont a létrehozás után szinkron API-k segítségével, a batch API-k, ezért használja fel, és excel-munkalapokat.

> [!NOTE]
> Ha további végpontokat adott hozzá a webszolgáltatáshoz, nem törölheti az alapértelmezett végpontot.

1. Machine Learning Studio (klasszikus), a bal oldali navigációs oszlopban kattintson a webszolgáltatások elemre.
2. A webszolgáltatás irányítópultjának alján kattintson a **végpontok kezelése**elemre. Megnyílik az Azure Machine Learning webszolgáltatások portál a webszolgáltatás végpontok lapjára.
3. Kattintson az **Új** lehetőségre.
4. Írjon be egy nevet és leírást az új végpont. A Végpontnevek 24 karakter vagy kevesebb, hosszúságúnak kell lennie, és kisbetűket betűk vagy számok el kell készíteni. Válassza ki a naplózási szint, és hogy engedélyezve van-e a mintaadatok. További információ a naplózásról: [Machine learning webszolgáltatások naplózásának engedélyezése](web-services-logging.md).

## <a id="scaling"></a>Webszolgáltatás méretezése további végpontok hozzáadásával

Alapértelmezés szerint minden közzétett webszolgáltatás 20 egyidejű kérés támogatására van konfigurálva, és akár 200 egyidejű kérelem is lehet. A Azure Machine Learning Studio (klasszikus) automatikusan optimalizálja a beállítást, hogy a legjobb teljesítményt nyújtsa a webszolgáltatás számára, és a portál értékét a rendszer figyelmen kívül hagyja.

Ha azt tervezi, hogy az API-t nagyobb terheléssel hívja meg, mint az egyidejű hívások maximális száma 200, akkor több végpontot is létre kell hoznia ugyanazon a webszolgáltatáson. Ezután véletlenszerűen terjesztheti a terhelést az összes közül.

A webszolgáltatás skálázása gyakori feladat. A skálázás bizonyos okai több mint 200 egyidejű kérés támogatásához, a rendelkezésre állás növeléséhez több végponton keresztül, vagy külön végpontokat biztosítanak a webszolgáltatásnak. A skálázás növeléséhez további végpontokat adhat hozzá ugyanahhoz a webszolgáltatáshoz a [Azure Machine learning webszolgáltatás](https://services.azureml.net/) -portálon keresztül.

Ne feledje, hogy a magas egyidejűségek használata hátrányos lehet, ha nem hívja meg az API-t megfelelő magas díjszabással. Ha a nagy terheléshez konfigurált API viszonylag alacsony terhelést eredményez, előfordulhat, hogy a késési időkorlát és/vagy a tüskék is megjelennek.

A szinkron API-kat jellemzően olyan helyzetekben használják, ahol alacsony késésre van szükség. A késés itt azt jelenti, hogy az API-nak egy kérést kell végrehajtania, és nem veszi figyelembe a hálózati késéseket. Tegyük fel, hogy van egy API-val egy 50-MS késéssel. Ahhoz, hogy teljes mértékben felhasználja a rendelkezésre álló kapacitást a magas szintű és a maximális párhuzamos hívásokkal: 20, meg kell hívnia ezt az API-t 20 * 1000/50 = 400-szor másodpercenként. Ezen túlmenően az 200-es maximális egyidejű hívások lehetővé teszik a 4000-os API-k meghívását másodpercenként, a 50 – MS késést feltételezve.

## <a name="next-steps"></a>Következő lépések

[Azure Machine learning webszolgáltatás felhasználása](consume-web-services.md).
