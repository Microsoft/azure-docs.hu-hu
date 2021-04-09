---
title: Hozzon létre Apache Kafka for Fluent Cloud-Azure partneri megoldások
description: 'Ez a cikk azt ismerteti, hogyan lehet a Azure Portal használatával létrehozni egy Apache Kafka egy példányát a következőhöz: Fluent felhő.'
ms.service: partner-services
ms.topic: quickstart
ms.date: 01/15/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: f4c6dacf63b1be44e826fe6841c87ccec4bf9b1a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98253769"
---
# <a name="quickstart-get-started-with-apache-kafka-for-confluent-cloud"></a>Gyors útmutató: az Apache Kafka használatának első lépései az Fluent felhőben

Ebben a rövid útmutatóban a Azure Portal használatával hozza létre a Apache Kafka egy példányát a felhőhöz.

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure-fiók. Ha nem rendelkezik aktív Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/).
- Az Azure-előfizetéshez _tulajdonos_ vagy _közreműködő_ szerepkörrel kell rendelkeznie. Az Azure és a Fluent integrációja csak _tulajdonosi_ vagy _közreműködői_ hozzáféréssel rendelkező felhasználók számára állítható be. Az első lépések előtt [ellenőrizze, hogy rendelkezik-e a megfelelő hozzáféréssel](../../role-based-access-control/check-access.md).

## <a name="find-offer"></a>Ajánlat keresése

A Azure Portal használatával megkeresheti az Fluent Cloud Application Apache Kafka.

1. A böngészőben nyissa meg a [Azure Portal](https://portal.azure.com/) , és jelentkezzen be.

1. Ha egy nemrégiben látogatott munkamenetben látogatta meg a **piactért** , válassza ki az ikont az elérhető lehetőségek közül. Egyéb esetben keressen rá a _Marketplace_ kifejezésre.

    :::image type="content" source="media/marketplace.png" alt-text="Piactér ikon.":::

1. A **piactér** lapon két lehetőség közül választhat a kívánt csomag típusától függően. Regisztrálhat az utólagos elszámolású csomagra vagy kötelezettségvállalási tervre. Az utólagos elszámolású szolgáltatás nyilvánosan elérhető. A kötelezettségvállalási terv azon ügyfelek számára érhető el, akik számára jóváhagytak egy privát ajánlatot.

   - Az **utólagos elszámolású ügyfelek esetében** keressen rá a _Apache Kafkara az Fluent felhőben_. Válassza ki az ajánlatot a Apache Kafka az Fluent felhőben.

     :::image type="content" source="media/search-pay-as-you-go.png" alt-text="Azure Marketplace-ajánlat keresése.":::

   - A **kötelezettségvállalási** ügyfelek esetében válassza a hivatkozásokat a **privát ajánlatok megtekintéséhez**. A kötelezettségvállalás megköveteli, hogy regisztráljon a minimális ráfordítás összegre. Ezt a lehetőséget csak akkor használja, ha biztos benne, hogy hosszabb időre van szüksége a szolgáltatásra.

     :::image type="content" source="media/view-private-offers.png" alt-text="privát ajánlatokat tekinthet meg.":::

     Tekintse meg a _Apache Kafkat az Fluent felhőben_.

     :::image type="content" source="media/select-from-private-offers.png" alt-text="Válassza a privát ajánlat lehetőséget.":::

## <a name="create-resource"></a>Erőforrás létrehozása

Miután kiválasztotta a Apache Kafkara vonatkozó ajánlatot az Fluent felhőben, készen áll az alkalmazás beállítására.

1. Ha az előző szakaszban a privát ajánlatokat választotta, két lehetőség közül választhat a csomag típusaihoz:

    - Felhőalapú – utólagos elszámolású
    - Kötelezettségvállalás – a véglegesítés terve

   Ha nem választotta ki a privát ajánlatokat, akkor csak az utólagos elszámolású opciót használhatja.

   Válassza ki a használni kívánt csomagot, majd kattintson a **beállítás + előfizetés** lehetőségre.

    :::image type="content" source="media/setup-subscribe.png" alt-text="Beállítás és előfizetés.":::

1. A **create Fluent Cloud Resource** alapjai lapon adja meg a következő értékeket. Ha elkészült, kattintson a **Tovább gombra: címkék**.

    :::image type="content" source="media/setup-basics.png" alt-text="A Felhőbeli felhőalapú erőforrás beállítására szolgáló űrlap.":::

    | Tulajdonság | Leírás |
    | ---- | ---- |
    | **Előfizetés** | A legördülő menüben válassza ki a telepíteni kívánt Azure-előfizetést. _Tulajdonosi_ vagy _közreműködői_ hozzáféréssel kell rendelkeznie. |
    | **Erőforráscsoport** | Itt adhatja meg, hogy új erőforráscsoportot kíván-e létrehozni, vagy egy meglévő erőforráscsoportot használ. Az erőforráscsoport olyan tároló, amely egy adott Azure-megoldás kapcsolódó erőforrásait tartalmazza. További információért olvassa el az [Azure-erőforráscsoportok áttekintését](../../azure-resource-manager/management/overview.md). |
    | **Fluent szervezet neve** | Adja meg a szolgáltatott szoftver (SaaS) erőforrás nevét. |
    | **Régió** | A legördülő menüben válassza ki az alábbi régiók egyikét: <br/><br/> Kelet-Ausztrália, Közép-Kanada, USA középső régiója, USA keleti régiója, USA 2. keleti régiója, Közép-Európa, Délkelet-Ázsia, Egyesült Királyság déli régiója, USA nyugati középső régiója, Nyugat-Európa, USA 2. nyugati régiója |
    | **Terv** | Válassza **a fizetés menet közben vagy a** **kötelezettségvállalás** lehetőséget. |
    | **Számlázási időszak** | A kiválasztott számlázási csomag alapján feltöltve. |
    | **Ár** | A kiválasztott Fluent-terv alapján feltöltve. |

1. A **címkék** területen adja meg az erőforrásra alkalmazni kívánt címkék **nevét** és **érték** párokat. A címkék megadása után válassza a **felülvizsgálat + létrehozás** elemet.

    :::image type="content" source="media/setup-tags.png" alt-text="Adja hozzá a projekt címkéit.":::

1. Tekintse át a megadott beállításokat. Ha elkészült, válassza a **Létrehozás** lehetőséget.

1. Az erőforrás létrehozása néhány percet vesz igénybe. A központi telepítés állapotát az **értesítésekben** tekintheti meg. Az üzembe helyezés befejezése után válassza ki az erőforrást az **Áttekintés** lap megtekintéséhez.

    :::image type="content" source="media/deployment-status.png" alt-text="Központi telepítés állapota.":::

   Ha hibaüzenetet kap, tekintse meg a következő témakört: [a felhőalapú megoldások Apache Kafka hibaelhárítása](troubleshoot.md).

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Az Fluent felhő erőforrásának kezelése](manage.md)
