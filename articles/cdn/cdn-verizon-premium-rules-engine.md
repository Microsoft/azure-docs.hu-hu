---
title: A HTTP-viselkedés felülbírálása a Azure CDN-Verizon Premium Rules motorral
description: A szabályok motor lehetővé teszi a HTTP-kérések kezelésének testreszabását a Verizon Premium Azure CDNtól, például bizonyos tartalomtípusok kézbesítésének blokkolásával, a gyorsítótárazási házirend meghatározásával és a HTTP-fejlécek módosításával.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: how-to
ms.date: 05/31/2019
ms.author: allensu
ms.openlocfilehash: a49912bc2275e478d657f06587c4ddc830210d3a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87040212"
---
# <a name="override-http-behavior-using-the-azure-cdn-from-verizon-premium-rules-engine"></a>HTTP-viselkedés felülbírálása a Verizon Premium Rules Engine Azure CDN használatával

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Áttekintés

A Azure CDN Rules Engine lehetővé teszi a HTTP-kérések kezelésének testreszabását. Például letilthatja bizonyos tartalomtípusok kézbesítését, a gyorsítótárazási házirend meghatározását vagy egy HTTP-fejléc módosítását. Ez az oktatóanyag bemutatja, hogyan hozhat létre olyan szabályt, amely megváltoztatja a CDN-eszközök gyorsítótárazási viselkedését. A szabályok motor szintaxisával kapcsolatos további információkért lásd a [Azure CDN Rules Engine-referenciát](cdn-verizon-premium-rules-engine-reference.md).

## <a name="access"></a>Access

A szabályok motor eléréséhez először a **CDN-profil** oldal tetején lévő **kezelés** elemre kell kiválasztania a Azure CDN felügyeleti oldalának eléréséhez. Attól függően, hogy a végpont a dinamikus hely gyorsítására van-e optimalizálva (DSA), akkor a szabályok motorját a megadott típusú végpontnak megfelelő szabályokkal érheti el:

- Általános webes kézbesítésre vagy más, nem DSA-optimalizálásra optimalizált végpontok:
    
    Válassza ki a **http nagyméretű** lapot, majd válassza a **szabályok motor** elemet.

    ![A HTTP-re vonatkozó szabályok motorja](./media/cdn-rules-engine/cdn-http-rules-engine.png)

- DSA-ra optimalizált végpontok:
    
    Válassza az **ADN** lapot, majd a **szabályok motor** elemet.
    
    Az ADN olyan kifejezés, amelyet a Verizon a DSA-tartalom megadására használ. Az itt létrehozott szabályokat a rendszer figyelmen kívül hagyja a profil egyik olyan végpontján sem, amely nem a DSA-ra van optimalizálva.

    ![A DSA szabályainak motorja](./media/cdn-rules-engine/cdn-dsa-rules-engine.png)

## <a name="tutorial"></a>Oktatóanyag

1. A **CDN-profil** lapon válassza a **kezelés** lehetőséget.
   
    ![CDN-profil kezelése gomb](./media/cdn-rules-engine/cdn-manage-btn.png)
   
    Megnyílik a CDN felügyeleti portál.

2. Válassza ki a **http nagyméretű** lapot, majd válassza a **szabályok motor** elemet.
   
    Megjelenik egy új szabály beállításai.
   
    ![CDN új szabály beállításai](./media/cdn-rules-engine/cdn-new-rule.png)
   
   > [!IMPORTANT]
   > A különböző szabályok listájának sorrendje befolyásolja a kezelésük módját. Egy későbbi szabály felülbírálhatja egy korábbi szabály által megadott műveleteket. Ha például van olyan szabály, amely lehetővé teszi az erőforrásokhoz való hozzáférést egy kérelem tulajdonsága alapján, valamint egy olyan szabályt, amely megtagadja az összes kérelem elérését, a második szabály felülbírálja az elsőt. A szabályok csak akkor bírálják felül a korábbi szabályokat, ha ugyanazokkal a tulajdonságokkal működnek.
   >

3. Adjon meg egy nevet a **név/leírás** szövegmezőben.

4. Azon kérelmek típusának meghatározása, amelyekre a szabály vonatkozik. Használja az alapértelmezett egyezési feltételt, **mindig**.
   
   ![CDN-szabály egyeztetési feltétele](./media/cdn-rules-engine/cdn-request-type.png)
   
   > [!NOTE]
   > A legördülő listában több egyezési feltétel is elérhető. Az aktuálisan kiválasztott egyeztetési feltétellel kapcsolatos információkért válassza ki a kék információs ikont a bal oldalon.
   >
   >  A feltételes kifejezések részletes listáját a [szabályok motor feltételes kifejezései](cdn-verizon-premium-rules-engine-reference-match-conditions.md)című témakörben tekintheti meg.
   >  
   > Az egyeztetési feltételek részletes listáját lásd: a [szabályok motorjának egyeztetési feltételei](cdn-verizon-premium-rules-engine-reference-match-conditions.md).
   >
   >

5. Új szolgáltatás hozzáadásához kattintson a szolgáltatások elem **+** melletti gombra .  A bal oldali legördülő menüben válassza a **kényszerített belső Max-Age** lehetőséget.  A megjelenő szövegmezőbe írja be a **300** értéket. Ne módosítsa a fennmaradó alapértelmezett értékeket.
   
   ![CDN-szabály funkció](./media/cdn-rules-engine/cdn-new-feature.png)
   
   > [!NOTE]
   > Több funkció is elérhető a legördülő listában. Az aktuálisan kiválasztott szolgáltatással kapcsolatos információkért válassza ki a kék információs ikont a bal oldalon.
   >
   > A **belső Max-Age kényszerítéséhez** az eszköz `Cache-Control` és a `Expires` fejlécek felülbírálva vannak, amikor a CDN peremhálózati csomópontja frissíti az eszközt a forrástól. Ebben a példában a CDN peremhálózati csomópontja 300 másodpercig vagy 5 percen belül gyorsítótárazza az adategységet, mielőtt frissíti az eszközt a forrástól.
   >
   > A szolgáltatások részletes listáját a [szabályok motor funkciói](cdn-verizon-premium-rules-engine-reference-features.md)című témakörben tekintheti meg.
   >
   >

6. Az új szabály mentéséhez válassza a **Hozzáadás** lehetőséget.  Az új szabály most jóváhagyásra vár. A jóváhagyást követően az állapot a **függőben lévő XML** -ből **aktív XML-** re változik.
   
   > [!IMPORTANT]
   > A szabályok változásai akár 10 percet is igénybe vehetnek Azure CDN.
   >
   >

## <a name="see-also"></a>Lásd még

- [Azure CDN áttekintése](cdn-overview.md)
- [Szabályok motor referenciája](cdn-verizon-premium-rules-engine-reference.md)
- [Szabálymotor egyezési feltételei](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Szabálymotor feltételes kifejezései](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Szabálymotor funkciói](cdn-verizon-premium-rules-engine-reference-features.md)
- [Azure Fridays: Azure CDN hatékony új funkciói](https://azure.microsoft.com/documentation/videos/azure-cdns-powerful-new-premium-features/) (videó)