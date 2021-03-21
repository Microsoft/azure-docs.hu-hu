---
title: 'ML Studio (klasszikus): webszolgáltatások naplózásának engedélyezése – Azure'
description: Megtudhatja, hogyan engedélyezheti a Machine Learning Studio (klasszikus) webszolgáltatások naplózását. A naplózás további információkat biztosít az API-k hibakereséséhez.
services: machine-learning
author: likebupt
ms.author: keli19
ms.assetid: c54d41e1-0300-46ef-bbfc-d6f7dca85086
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
ms.date: 06/15/2017
ms.openlocfilehash: c9ce9b7b0d739301e76abd43b265fe28195ed302
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100518283"
---
# <a name="enable-logging-for-azure-machine-learning-studio-classic-web-services"></a>Azure Machine Learning Studio (klasszikus) webszolgáltatások naplózásának engedélyezése

**a következőkre vonatkozik:** ![ A következőre vonatkozik:. ](../../../includes/media/aml-applies-to-skus/yes.png) A Machine Learning Studio (klasszikus) ![ nem vonatkozik a következőre:.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  


Ez a dokumentum a Machine Learning Studio (klasszikus) webszolgáltatások naplózási képességével kapcsolatos információkat tartalmaz. A naplózási szolgáltatás további információkat tartalmaz, amelyek csak egy adott számú és egy üzeneten felül segítenek a Machine Learning Studio (klasszikus) API-k hívásának hibakeresésében.  

## <a name="how-to-enable-logging-for-a-web-service"></a>Webszolgáltatások naplózásának engedélyezése

Engedélyezi a naplózást a [Azure Machine learning Studio (klasszikus) webszolgáltatások](https://services.azureml.net) portálján. 

1. Jelentkezzen be a Azure Machine Learning Studio (klasszikus) webszolgáltatások portálján a következő címen: [https://services.azureml.net](https://services.azureml.net) . A klasszikus webszolgáltatások esetében az új webMachine Learning Studio **szolgáltatások** használata a Studióban (klasszikus) webszolgáltatások lapján is elérhetővé teheti a portált.

   ![Új webszolgáltatási élmény hivatkozása](./media/web-services-logging/new-web-services-experience-link.png)

2. A felső menüsorban kattintson az új webszolgáltatáshoz tartozó **webszolgáltatások** lehetőségre, vagy kattintson a klasszikus webszolgáltatások klasszikus webes **szolgáltatásai** lehetőségre.

   ![Új vagy klasszikus webszolgáltatások kiválasztása](./media/web-services-logging/select-web-service.png)

3. Új webszolgáltatás esetén kattintson a webszolgáltatás nevére. Klasszikus webszolgáltatás esetén kattintson a webszolgáltatás nevére, majd a következő lapon kattintson a megfelelő végpontra.

4. A felső menüsorban kattintson a **Konfigurálás** elemre.

5. Állítsa be a **naplózás engedélyezése** beállítást a *hiba* (csak a hibák naplózása) vagy az *összes* (teljes naplózáshoz) beállításnál.

   ![Naplózási szint kiválasztása](./media/web-services-logging/enable-logging.png)

6. Kattintson a **Mentés** gombra.

7. A klasszikus webszolgáltatások esetében hozza létre a **ml-diagnosztika** tárolót.

   Az összes webszolgáltatás naplója egy **ml-diagnosztika** nevű blob-tárolóban marad a webszolgáltatáshoz társított Storage-fiókban. Az új webszolgáltatások esetében ez a tároló jön létre, amikor először fér hozzá a webszolgáltatáshoz. A klasszikus webszolgáltatások esetében létre kell hoznia a tárolót, ha még nem létezik. 

   1. A [Azure Portal](https://portal.azure.com)lépjen a webszolgáltatáshoz társított Storage-fiókra.

   2. A **blob szolgáltatás** alatt kattintson a **tárolók** elemre.

   3. Ha a tároló **ml-diagnosztika** nem létezik, kattintson a **+ tároló** elemre, adja meg a tárolónak a "ml-diagnosztika" nevet, és válassza a **hozzáférési típust** "blob"-ként. Kattintson az **OK** gombra.

      ![Hozzon létre egy új tárolót a diagnosztikai naplók tárolásához](./media/web-services-logging/create-ml-diagnostics-container.png)

> [!TIP]
>
> A klasszikus webszolgáltatások esetében a Machine Learning Studio (klasszikus) webszolgáltatások irányítópultján is van egy kapcsoló a naplózás engedélyezéséhez. Mivel azonban a naplózást most a webszolgáltatások portálon keresztül felügyelik, engedélyeznie kell a naplózást a portálon a jelen cikkben leírtak szerint. Ha már engedélyezte a naplózást a Studióban (klasszikus), akkor a Web Services portálon tiltsa le a naplózást, majd engedélyezze újra.


## <a name="the-effects-of-enabling-logging"></a>A naplózás engedélyezésének hatásai
Ha a naplózás engedélyezve van, a webszolgáltatási végpont diagnosztika és hibák naplózása a felhasználó munkaterületéhez társított Azure Storage **-fiók ml-diagnosztika blob-** tárolójában történik. Ez a tároló tartalmazza az összes webszolgáltatási végponthoz tartozó diagnosztikai információt az ehhez a Storage-fiókhoz társított összes munkaterülethez.

A naplók az Azure Storage-fiók megismeréséhez rendelkezésre álló számos eszköz használatával tekinthetők meg. A legegyszerűbb lehet, ha a Azure Portal a Storage-fiókra kattint **, kattintson a tárolók** elemre, majd kattintson a tároló **ml-diagnosztika** elemre.  

## <a name="log-blob-detail-information"></a>A blob részletes adatainak naplózása
A tárolóban lévő összes blob a diagnosztikai adatokat a következő műveletek pontosan egyikének megfelelően tárolja:

* A Batch-Execution metódus végrehajtása  
* A Request-Response metódus végrehajtása  
* Request-Response tároló inicializálása

Az egyes Blobok neve a következő formátumú előtaggal rendelkezik: 


`{Workspace Id}-{Web service Id}-{Endpoint Id}/{Log type}`


Ahol a _napló típusa_ a következő értékek egyike:  

* kötegelt  
* pontszám/kérelmek  
* pontszám/init