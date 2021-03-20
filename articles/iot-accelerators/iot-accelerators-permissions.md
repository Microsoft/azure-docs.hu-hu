---
title: Az Azure IoT Solutions webhelyének használata – Azure | Microsoft Docs
description: Ismerteti, hogyan használható a AzureIoTSolutions.com webhely a megoldás-gyorsító üzembe helyezéséhez.
author: dominicbetts
manager: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: dobett
ms.openlocfilehash: 5a088cb07fa74b65e497de54be8c97bd51d31c16
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97387836"
---
# <a name="use-the-azureiotsolutionscom-site-to-deploy-your-solution-accelerator"></a>A megoldás-gyorsító üzembe helyezése a azureiotsolutions.com webhely használatával

Az Azure IoT megoldás-gyorsítók üzembe helyezhetők az Azure-előfizetésben a [AzureIoTSolutions.com](https://www.azureiotsolutions.com/Accelerators)-ből. A AzureIoTSolutions.com a Microsoft nyílt forráskódú és partneri megoldásának gyorssegédeit is üzemelteti. Ezek a megoldás-gyorsítók az [Azure IoT hivatkozási architektúrával](/azure/architecture/reference-architectures/iot)vannak összehangolva. A hely használatával gyorsan üzembe helyezhet egy megoldás-gyorssegédet bemutatóként vagy éles környezetben.

:::image type="content" source="media/iot-accelerators-permissions/iotsolutionscom.png" alt-text="IoT-megoldások kezdőlapja":::

> [!TIP]
> Ha nagyobb mértékű vezérlésre van szüksége a telepítési folyamat felett, a parancssori felület használatával üzembe helyezhet egy megoldás-gyorsító.

A megoldás-gyorsítók a következő konfigurációkon helyezhetők üzembe:

* **Standard**: kibővített infrastruktúra üzembe helyezése éles környezet fejlesztéséhez. A Azure Container Service üzembe helyezi a szolgáltatásait több Azure-beli virtuális gépen. A Kubernetes koordinálja az egyes mikroszolgáltatásokat üzemeltető Docker-tárolókat.
* **Alapszintű**: csökkentett Cost-verzió egy bemutatóhoz vagy egy üzemelő példány teszteléséhez. Mindegyik mikroszolgáltatás üzembe helyezhető egy Azure-beli virtuális gépen.
* **Helyi**: helyi számítógép-telepítés teszteléshez és fejlesztéshez. Ez a megközelítés telepíti a-szolgáltatásokat egy helyi Docker-tárolóba, és a felhőben csatlakozik a IoT Hubhoz, Azure Cosmos DBhoz és az Azure Storage szolgáltatásokhoz.

A megoldás-gyorsítók mindegyike különböző Azure-szolgáltatások (például IoT Hub, Azure Stream Analytics és Cosmos DB kombinációját használja. További információért látogasson el a [AzureIoTSolutions.com](https://www.azureiotsolutions.com/Accelerators) webhelyre, és válassza ki a megoldás-gyorsító elemet.

## <a name="sign-in-at-azureiotsolutionscom"></a>Bejelentkezés a azureiotsolutions.com-on

A megoldás-gyorsító üzembe helyezése előtt be kell jelentkeznie a AzureIoTSolutions.com-be az Azure-előfizetéshez társított hitelesítő adatok használatával. Ha a fiókja több Microsoft Azure Active Directory (AD) bérlőhöz van társítva, a **fiók kiválasztása legördülő menüből** kiválaszthatja a használni kívánt könyvtárat.

A megoldás-gyorsítók üzembe helyezéséhez, a felhasználók kezeléséhez és az Azure-szolgáltatások kezeléséhez szükséges engedélyek a kiválasztott könyvtár szerepköreitől függenek. A megoldás-gyorssegédekhez társított általános Azure AD-szerepkörök a következők:

* **Globális rendszergazda**: az Azure ad-bérlőn számos [globális rendszergazda](../active-directory/roles/permissions-reference.md) lehet:

  * Amikor létrehoz egy Azure AD-bérlőt, alapértelmezés szerint a bérlő globális rendszergazdája lesz.
  * A globális rendszergazda telepítheti az alapszintű és a standard megoldás-gyorssegédeket.

* **Tartományi felhasználó**: az Azure ad-bérlőn számos tartományi felhasználó lehet. Egy tartományi felhasználó üzembe helyezhet egy alapszintű megoldás-gyorsító szolgáltatást.

* **Vendég felhasználó**: az Azure ad-bérlő számos vendég felhasználója lehet. A vendég felhasználók nem telepíthetnek megoldás-gyorssegédet az Azure AD-bérlőben.

Az Azure AD-beli felhasználókról és szerepkörökről az alábbi forrásokban talál további információt:

* [Felhasználók létrehozása Azure Active Directory](../active-directory/fundamentals/active-directory-users-profile-azure-portal.md)
* [Felhasználók kiosztása alkalmazásokhoz](../active-directory/manage-apps/assign-user-or-group-access-portal.md)

## <a name="choose-your-device"></a>Eszköz kiválasztása

A AzureIoTSolutions.com-hely a [IoT-eszközök katalógusához tartozó Azure Certified minősítésre](https://catalog.azureiotsolutions.com/)hivatkozik.

A katalógus felsorolja a több száz tanúsított IoT hardvereszközöket, amelyekkel csatlakozhat a megoldás-gyorssegédekhez, és megkezdheti a IoT-megoldás felkészítését.

![Eszköz katalógusa](media/iot-accelerators-permissions/devicecatalog.png)

Ha Ön hardver gyártója, kattintson a partnerré **válás** lehetőségre a Microsofttal való partneri együttműködés megismeréséhez a Certified for IoT programban.

## <a name="next-steps"></a>Következő lépések

Ha szeretné kipróbálni az egyik IoT-megoldás-gyorssegédet, tekintse meg a gyors üzembe helyezési útmutatót: [próbáljon ki egy csatlakoztatott gyári megoldást](quickstart-connected-factory-deploy.md).
