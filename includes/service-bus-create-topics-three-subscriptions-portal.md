---
title: fájlbefoglalás
description: fájlbefoglalás
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 02/20/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: ace42278269ff6af31902dbecead81329815af12
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "67179355"
---
## <a name="create-a-topic-using-the-azure-portal"></a>Üzenettéma létrehozása az Azure Portal használatával
1. A **Service Bus névtér** lapon válassza a bal oldali menü **témakörök** elemét.
2. Válassza a **+ témakör** lehetőséget az eszköztáron. 
4. Adja meg a témakör **nevét** . A többi beállítást hagyja az alapértelmezett értékükön.
5. Kattintson a **Létrehozás** gombra.

    ![Témakör létrehozása](./media/service-bus-create-topics-subscriptions-portal/create-topic.png)

## <a name="create-subscriptions-to-the-topic"></a>Előfizetések létrehozása a témakörhöz
1. Válassza ki az előző szakaszban létrehozott **témakört** . 
    
    ![Témakör kiválasztása](./media/service-bus-create-topics-subscriptions-portal/select-topic.png)
2. A **Service Bus témakör** lapon válassza az **előfizetések** lehetőséget a bal oldali menüben, majd válassza a **+ előfizetés** elemet az eszköztáron. 
    
    ![Előfizetés hozzáadása gomb](./media/service-bus-create-topics-subscriptions-portal/add-subscription-button.png)
3. Az **előfizetés létrehozása** lapon adja meg az **S1** nevet az előfizetés **neveként** , majd válassza a **Létrehozás**lehetőséget. 

    ![Előfizetés létrehozása lap](./media/service-bus-create-topics-subscriptions-portal/create-subscription-page.png)
4. Ismételje meg az előző lépést kétszer az **S2** és **S3**nevű előfizetések létrehozásához.