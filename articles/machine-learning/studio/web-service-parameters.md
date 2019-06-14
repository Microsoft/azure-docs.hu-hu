---
title: Webalkalmazás-paraméterek – Azure Machine Learning Studióban |} A Microsoft Docs
description: Hogyan használható az Azure Machine Learning webszolgáltatás-paraméterek a web service elérésekor a modell működésének módosítása céljából.
services: machine-learning
documentationcenter: ''
author: xiaoharper
ms.custom: seodec18
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: c49187db-b976-4731-89d6-11a0bf653db1
ms.service: machine-learning
ms.subservice: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/12/2017
ms.openlocfilehash: a236043d5622e5a2e1ffd572c887fb5ffac2174a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60345447"
---
# <a name="use-azure-machine-learning-studio-web-service-parameters"></a>Az Azure Machine Learning Studio webszolgáltatás-paraméterek használata
Az Azure Machine Learning webszolgáltatás tegye közzé konfigurálható paraméterek és modulokat tartalmazó kísérlet jön létre. Bizonyos esetekben előfordulhat, hogy módosítani szeretné a modul viselkedését a webszolgáltatás futtatása közben. *Webalkalmazás-paraméterek* lehetővé teszi, hogy a feladat végrehajtásához. 

Ilyenek például állítsa be a [adatok importálása] [ reader] modul úgy, hogy a felhasználó a közzétett webes szolgáltatás egy másik adatforráshoz adhatja meg a webszolgáltatás eléréséhez. Vagy konfigurálása a [adatok exportálása] [ writer] modul úgy, hogy egy másik célhelyet lehet megadni. Néhány példa szemlélteti tartalmazza a bits számának módosítása a [Szolgáltatáskivonatolás] [ feature-hashing] modul vagy a száma a kívánt funkcióinak a [szűrő-alapú szolgáltatás kiválasztása] [ filter-based-feature-selection] modul. 

Állítsa be a webszolgáltatás-paraméterek, és rendelje azokat egy vagy több modulja paraméter a kísérlet során, és megadhatja, hogy azok a szükséges és választható. A felhasználó a webszolgáltatás majd megadhat értékeket a paraméterekhez, a webszolgáltatás hívásakor. 



## <a name="how-to-set-and-use-web-service-parameters"></a>Állítsa be, és webszolgáltatás-paraméterek használata
A paraméter egy modul melletti ikonra kattintva, majd válassza a "Webes szolgáltatás paraméter beállítása" meghatározhat egy webes szolgáltatás paramétert. Ez létrehoz egy új webes szolgáltatás paramétert, és csatlakoztatja, hogy a modul paraméter. Ezután a web service érhető el, amikor a felhasználó egyik értékét adhatja meg a Web Service paraméterhez, és a modul paramétert alkalmazza a rendszer.

Miután meghatározott egy webes szolgáltatás paraméter, érhető el a kísérletben bármely más modul paraméteréhez. Egy olyan paramétert egy modul társított webszolgáltatási paraméter határozza meg, ha használható a webszolgáltatási paraméter bármely egyéb modult, mindaddig, amíg a paraméter a ugyanolyan típusú értéket vár. Például ha a Web Service paraméter egy numerikus értéket, majd csak használat modul paraméterek egy numerikus értéket várt. A felhasználó beállít egy értéket a Web Service paraméter, akkor lépnek érvénybe az összes társított modul paraméterei.

Eldöntheti, hogy adja meg a Web Service paraméter alapértelmezett értékét. Ha így tesz, a paraméter megadása esetén a felhasználó a webszolgáltatás nem kötelező. Ha nem ad meg alapértelmezett értéket, majd a felhasználó szükséges adjon meg egy értéket, ha a webszolgáltatás.

A webszolgáltatás API-dokumentáció programozott módon megadása a Web Service paraméter, a web service elérésekor a szolgáltatás felhasználó információkat biztosít.

> [!NOTE]
> A klasszikus webszolgáltatások API dokumentációjában érhető el a **API súgóoldalt** hivatkozásra a web service **IRÁNYÍTÓPULT** a Machine Learning Studióban. Az API-dokumentáció egy új webszolgáltatás által biztosított a [Azure Machine Learning webszolgáltatások](https://services.azureml.net/Quickstart) a portál a **felhasználás** és **Swagger API** a webes oldalak a szolgáltatás.
> 
> 

## <a name="example"></a>Példa
Például tegyük fel, hogy kísérletezés az egy [adatok exportálása] [ writer] modul, amely adatokat küld az Azure blob storage-bA. Fogunk meghatározni a "Blob elérési útja" nevű Web Service paraméter, amely lehetővé teszi, hogy a szolgáltatás felhasználó elérési útjának módosítása a blob Storage, a szolgáltatás eléréséhez.

1. A Machine Learning Studióban, kattintson a [adatok exportálása] [ writer] modult, válassza ki azt. A tulajdonságainak jobb oldalán a kísérletvászonra a Tulajdonságok panelen látható.
2. Adja meg a tárolás típusát:
   
   * A **adja meg az adat célhelyét**, válassza az "Azure Blob Storage".
   * A **adja meg a hitelesítési típus**, válassza az "Account".
   * Írja be a fiók adatait az Azure blob storage. 

3. Kattintson az ikonra a jobb oldalán a **blob-tároló paraméter kezdődő elérési útja**. Ez a következőhöz hasonló:
   
   ![Webes szolgáltatás paraméter ikon](./media/web-service-parameters/icon.png)
   
   Válassza ki a "Webes szolgáltatás paraméter beállítása".
   
   Bejegyzés kerül a **webszolgáltatás-paraméterek** alján, a Tulajdonságok panelen a "Blob-tároló kezdődő elérési" néven. Ez az a Web Service paraméter, amely mostantól a társított [adatok exportálása] [ writer] modul paraméter.
4. Nevezze át a webes paraméter, kattintson a nevére, adja meg a "Blob elérési útja", és nyomja le az **Enter** kulcsot. 
5. Adja meg a webszolgáltatási paraméter alapértelmezett értékét, kattintson az ikonra a jobb oldalon a név, jelölje ki "Adja meg az alapértelmezett érték", adjon meg egy értéket (például "container1/output1.csv"), és nyomja le az **Enter** kulcs.
   
   ![Webszolgáltatási paraméter](./media/web-service-parameters/parameter.png)
6. Kattintson a **Run** (Futtatás) parancsra. 
7. Kattintson a **webszolgáltatás üzembe helyezése** válassza **Web Service telepítése [klasszikus]** vagy **[Új] Web Service telepítése** a webszolgáltatás üzembe helyezéséhez.

> [!NOTE] 
> Egy új webszolgáltatás üzembe helyezéséhez rendelkeznie megfelelő engedélyekkel, amelyhez az előfizetésben, a web Service szolgáltatásának telepítése. További információ: [egy webszolgáltatás, az Azure Machine Learning Web Services portál használata kezelheti](manage-new-webservice.md). 

A felhasználó a webszolgáltatás most adjon meg egy új célhelyet a [adatok exportálása] [ writer] modul, a web service elérésekor.

## <a name="more-information"></a>További információ
Egy részletes példa: a [webszolgáltatás-paraméterek](https://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx) bejegyzést a [Machine Learning blogon](https://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx).

A Machine Learning webszolgáltatás eléréséhez további információkért lásd: [használata az Azure Machine Learning Web service](consume-web-services.md).

<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[writer]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/

