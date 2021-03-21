---
title: Poster konfigurálása Azure Media Services REST API hívásokhoz
description: Ez a cikk azt ismerteti, hogyan konfigurálható a Poster Media Services REST API-hívásokhoz.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: ef92e772085b1b89388c3f85fb3fdb91df0f6a75
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103012207"
---
# <a name="configure-postman-for-media-services-v2-rest-api-calls"></a>Poster konfigurálása Media Services v2 REST API hívásokhoz

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> A Media Services v2 nem fog bővülni újabb funkciókkal és szolgáltatásokkal. <br/>Tekintse meg a legújabb, [Media Services v3](../latest/index.yml)verziót. Lásd még: [az áttelepítési útmutató v2-től v3-ig](../latest/migrate-v-2-v-3-migration-introduction.md)

Ez az oktatóanyag bemutatja, hogyan konfigurálhatja a **Poster** -t, hogy felhasználható legyen Azure Media Services (AMS) REST API-k meghívására. Az oktatóanyag bemutatja, hogyan importálhat környezeteket és gyűjteményeket a **Poster**-ba. A gyűjtemény a Azure Media Services (AMS) REST API-kat meghívó HTTP-kérések csoportosított definícióit tartalmazza. A környezeti fájl azokat a változókat tartalmazza, amelyeket a gyűjtemény használ.

Ez a környezet és gyűjtemény olyan cikkekben használatos, amelyek bemutatják, hogyan lehet különböző feladatokat elérni Azure Media Services REST API-kkal.

## <a name="prerequisites"></a>Előfeltételek

- Telepítse a [Postman](https://www.getpostman.com/) REST-ügyfelet, hogy végrehajtsa az AMS REST oktatóanyagok egy részében látható REST API-kat. 

    A **Postmant** használjuk, de bármely egyéb REST-eszköz is megfelelő. Más alternatívák: **Visual Studio Code** a REST beépülő modullal vagy a **Telerik Hegedűs**. 

## <a name="configure-the-environment"></a>A környezet konfigurálása 

1. Hozzon létre egy. JSON fájlt, amely tartalmazza az AMS oktatóanyagokban használt környezeti változókat. Nevezze el a fájlt (például **AzureMediaServices.postman_environment.json**). Nyissa meg a fájlt, és illessze be a Poster-környezetet meghatározó kódot a [kód listájából](postman-environment.md). 
2. Nyissa meg a **Postmant**.
3. A képernyő jobb oldalán válassza a **Manage environment (Környezet felügyelete)** lehetőséget.

    ![Képernyőfelvétel: a kiválasztott környezet kezelése lehetőség.](./media/media-services-rest-upload-files/postman-create-env.png)
4. A **Manage environment (Környezet felügyelete)** párbeszédablakban kattintson az **Import (Importálás)** gombra.
5. Tallózással keresse meg és válassza ki a **AzureMediaServices.postman_environment.js** fájlt.
6. A **AzureMedia** -környezet hozzá van adva.
7. Zárja be a párbeszédpanelt.
8. Válassza ki a **AzureMedia** -környezetet.

    ![A képernyőképen a kiválasztott AzureMedia-környezet látható.](./media/media-services-rest-upload-files/postman-choose-env.png)

## <a name="configure-the-collection"></a>A gyűjtemény konfigurálása

1. Hozzon létre egy. JSON-fájlt, amely tartalmazza a **Poster** -gyűjteményt az összes olyan művelettel, amely szükséges a fájlok Media Servicesba való feltöltéséhez. Nevezze el a fájlt (például **AzureMediaServicesOperations.postman_collection.json**). Nyissa meg a fájlt, és illessze be a **Poster** [-gyűjteményt meghatározó kódot a](postman-collection.md)listában.
2. Kattintson az **Import (Importálás)** gombra a gyűjteményfájl importálásához.
3. Válassza ki a **AzureMediaServicesOperations.postman_collection.js** fájlt.

    ![Képernyőfelvétel: az IMPORTÁLÁS párbeszédpanel, ahol a kiválasztott fájlok vannak kiválasztva.](./media/media-services-rest-upload-files/postman-import-collection.png)

## <a name="next-steps"></a>Következő lépések

Tekintse meg az [eszközök feltöltése](media-services-rest-upload-files.md) című cikket.  
