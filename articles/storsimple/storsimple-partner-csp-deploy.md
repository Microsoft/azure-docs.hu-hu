---
title: A StorSimple Virtual Array üzembe helyezése a Cloud Solution Provider programhoz
description: Megtudhatja, hogy a CSP-partner hogyan adhat hozzá egy ügyfelet vagy új előfizetést egy meglévő ügyfélhez, majd létrehozhat egy szolgáltatást a StorSimple virtuális tömb telepítéséhez a CSP-ben.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/08/2017
ms.author: alkohli
ms.openlocfilehash: dc0cf718ee51e23fb749bdf57d5344977de009d8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96021860"
---
# <a name="deploy-storsimple-virtual-array-for-cloud-solution-provider-program"></a>A StorSimple Virtual Array üzembe helyezése a Cloud Solution Provider programhoz

## <a name="overview"></a>Áttekintés

A StorSimple Virtual Array a Cloud Solution Provider (CSP) partnerei által is üzembe helyezhető az ügyfeleiknek. A CSP-partnerek létrehozhatnak egy StorSimple Eszközkezelő szolgáltatást. Ez a szolgáltatás ezután a StorSimple virtuális tömb, valamint a hozzájuk tartozó megosztások, kötetek és biztonsági másolatok üzembe helyezésére és kezelésére használható.

Ez a cikk azt ismerteti, hogy a CSP-partner hogyan adhat hozzá ügyfelet vagy új előfizetést egy meglévő ügyfélhez, majd létrehozhat egy szolgáltatást a StorSimple virtuális tömb telepítéséhez a CSP-ben.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg a következőket:

- A CSP program keretében regisztrálva van.
- Érvényes a [partner Center](https://partnercenter.microsoft.com/) bejelentkezési hitelesítő adatai. A hitelesítő adatok lehetővé teszik, hogy bejelentkezzen a partner portálra új ügyfelek hozzáadásához, ügyfelek kereséséhez, vagy a partner irányítópultján navigáljon egy felhasználói fiókhoz. A CSP működhet StorSimple-rendszergazdaként az ügyfél nevében a Azure Portalban.
                             
## <a name="add-a-customer"></a>Ügyfél hozzáadása

Ha hozzáad egy ügyfelet, a rendszer automatikusan létrehoz egy előfizetést. Ha szeretne hozzáadni egy ügyfelet (és automatikusan létrehoz egy előfizetést), hajtsa végre a következő lépéseket a partner portálon.

1. Lépjen a [partner központba](https://partnercenter.microsoft.com/) , és jelentkezzen be a CSP hitelesítő adataival. Kattintson az **irányítópult** elemre.

     ![Irányítópult a partner Centerben](./media/storsimple-partner-csp-deploy/image1.png)
                              
2. A bal oldali ablaktáblán kattintson az **ügyfelek** elemre. A jobb oldali ablaktáblában kattintson az **ügyfelek hozzáadása** lehetőségre. Adja meg az ügyfél adatait. Kattintson a **Tovább gombra: előfizetések** egy ügyfél-előfizetés létrehozásához.

    ![Ügyfél hozzáadása](./media/storsimple-partner-csp-deploy/image2.png)

3.  Válassza ki **Microsoft Azure** ajánlatot. Görgessen a lap aljára, és kattintson az **Áttekintés** elemre.

    ![Előfizetés adatainak áttekintése](./media/storsimple-partner-csp-deploy/image3.png)
                              
4. Tekintse át az információkat, és kattintson a **Küldés** gombra.

    ![Előfizetés beküldése](./media/storsimple-partner-csp-deploy/image4.png)

5. Mentse a megerősítő információkat későbbi referenciául.

    ![Jóváhagyás mentése](./media/storsimple-partner-csp-deploy/image5.png)

6. Keresse meg vagy navigáljon az imént hozzáadott ügyfélhez. Kattintson a **vállalat nevére** a részletek részletezéséhez.

    ![Az ügyfél keresése](./media/storsimple-partner-csp-deploy/image6.png)  

7. A bal oldali ablaktáblán válassza a **Service Management** elemet. A jobb oldali ablaktáblában, a **szolgáltatások felügyelete** területen kattintson a **a Microsoft Azure felügyeleti portálja** elemre, és jelentkezzen be az ügyfél Azure-rendszergazdájaként.

    ![Bejelentkezés az Azure Portalra](./media/storsimple-partner-csp-deploy/image9.png)

8. StorSimple-Eszközkezelő létrehozásához kattintson az **+ új** elemre, és keressen rá, vagy keresse meg a **StorSimple virtuális eszköz sorozatát**. További információért látogasson el a [StorSimple Eszközkezelő szolgáltatás üzembe helyezése](storsimple-virtual-array-manage-service.md)című témakörre.

    ![StorSimple-Eszközkezelő szolgáltatás létrehozása](./media/storsimple-partner-csp-deploy/image8.png)


## <a name="add-a-subscription"></a>Előfizetés hozzáadása

Bizonyos esetekben előfordulhat, hogy van egy meglévő ügyfele, és hozzá kell adnia egy előfizetést. Ha egy meglévő ügyfélhez szeretne előfizetést hozzáadni, hajtsa végre a következő lépéseket a partner portálon.

1. Lépjen a [partner központba](https://partnercenter.microsoft.com/) , és jelentkezzen be a CSP hitelesítő adataival. Kattintson az **irányítópult** elemre.

     ![Irányítópult a partner Centerben](./media/storsimple-partner-csp-deploy/image1.png)
                              
2. A bal oldali ablaktáblán kattintson az **ügyfelek** elemre. Keresse meg vagy Navigáljon arra az ügyfélre, amelyhez előfizetést szeretne hozzáadni. A ![ kibontási pipa ikonra kattintva ](./media/storsimple-partner-csp-deploy/expand_pane_icon.png) kibonthatja az ügyfélhez tartozó vállalat nevét. A Részletek területen kattintson az **előfizetések hozzáadása** lehetőségre.

    ![Ügyfelek](./media/storsimple-partner-csp-deploy/image10.png)

3. Jelölje be a **Microsoft Azure** az előfizetésben szereplő **legjobb ajánlatokat** , majd kattintson a **Submit (Küldés**) gombra. Ez létrehoz egy új előfizetést.

    ![Új előfizetés hozzáadása](./media/storsimple-partner-csp-deploy/image11.png)

6. Miután létrehozta az új előfizetést, kattintson a **< – ügyfelek** elemre a bal oldali ablaktáblán, és térjen vissza az **ügyfelek** lapra. Keresse meg azt az ügyfelet, akivel nemrég létrehozott egy előfizetést. Kattintson a **vállalat nevére** a részletek részletezéséhez.

    ![Az ügyfél keresése](./media/storsimple-partner-csp-deploy/image6.png)  

7. A bal oldali ablaktáblán válassza a **Service Management** elemet. A jobb oldali ablaktáblában, a **szolgáltatások felügyelete** területen kattintson a **a Microsoft Azure felügyeleti portálja** elemre, és jelentkezzen be az ügyfél Azure-rendszergazdájaként.

    ![Bejelentkezés az Azure Portalra](./media/storsimple-partner-csp-deploy/image9.png)

8. StorSimple-Eszközkezelő létrehozásához kattintson az **+ új** elemre, és keressen rá, vagy keresse meg a **StorSimple virtuális eszköz sorozatát**. További információért látogasson el a [StorSimple Eszközkezelő szolgáltatás üzembe helyezése](storsimple-virtual-array-manage-service.md)című témakörre.

    ![StorSimple-Eszközkezelő szolgáltatás létrehozása](./media/storsimple-partner-csp-deploy/image8.png)

## <a name="next-steps"></a>Következő lépések

- Ha további kérdései vannak a CSP-StorSimple kapcsolatban, lépjen a [StorSimple a CSP-ben: gyakori kérdések](storsimple-partner-csp-faq.md).
- Ha készen áll a StorSimple üzembe helyezésére, lépjen a [StorSimple üzembe helyezése a CSP-ben](storsimple-partner-csp-deploy.md)című lépésre.
