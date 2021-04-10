---
title: Kapcsolódás a Azure Media Services V3 API-hoz – Java
description: Ez a cikk azt ismerteti, hogyan csatlakozhat a Javához Azure Media Services V3 API-hoz.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/17/2020
ms.custom: devx-track-java
ms.author: inhenkel
ms.openlocfilehash: 06923d7c198edc324d85b726cf91694d8cf7e1ca
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105961353"
---
# <a name="connect-to-media-services-v3-api---java"></a>Kapcsolódás a Media Services V3 API-hoz – Java

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Ez a cikk bemutatja, hogyan csatlakozhat a Azure Media Services v3 Java SDK-hoz az egyszerű szolgáltatás bejelentkezési metódusának használatával.

Ebben a cikkben a Visual Studio Code-ot használjuk a minta alkalmazás fejlesztéséhez.

## <a name="prerequisites"></a>Előfeltételek

- A következő lépésekkel telepítheti a [Java-t a Visual Studio Code](https://code.visualstudio.com/docs/java/java-tutorial) használatával:

   - JDK
   - Apache Maven
   - Java kiterjesztési csomag
- Ügyeljen rá, hogy a `JAVA_HOME` és a környezeti változók legyenek beállítva `PATH` .
- [Hozzon létre egy Media Services fiókot](./account-create-how-to.md). Ügyeljen arra, hogy jegyezze fel az erőforráscsoport nevét és a Media Services fiók nevét.
- Kövesse az API-k [elérését](./access-api-howto.md) ismertető témakör lépéseit. Jegyezze fel az előfizetés-azonosítót, az alkalmazás AZONOSÍTÓját (ügyfél-azonosítót), a hitelesítő kulcsot (Secret) és a bérlő AZONOSÍTÓját, amelyre szüksége van egy későbbi lépésben.

Tekintse át a következőket is:

- [Java a Visual Studio Code-ban](https://code.visualstudio.com/docs/languages/java)
- [Java projektmenedzsment a VS Code-ban](https://code.visualstudio.com/docs/java/java-project)

> [!IMPORTANT]
> Tekintse át az [elnevezési konvenciókat](media-services-apis-overview.md#naming-conventions).

## <a name="create-a-maven-project"></a>Maven-projekt létrehozása

Nyisson meg egy parancssori eszközt és `cd` egy olyan könyvtárat, amelyben létre szeretné hozni a projektet.
    
```
mvn archetype:generate -DgroupId=com.azure.ams -DartifactId=testAzureApp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

A parancs futtatásakor a, a `pom.xml` `App.java` és más fájlok jönnek létre. 

## <a name="add-dependencies"></a>Függőségek hozzáadása

1. A Visual Studio Code-ban nyissa meg azt a mappát, ahol a projekt
1. Keresse meg és nyissa meg a `pom.xml`
1. Adja hozzá a szükséges függőségeket.

   Lásd: `pom.xml` a [videó kódolási](https://github.com/Azure-Samples/media-services-v3-java/blob/master/VideoEncoding/EncodingWithMESCustomPreset/pom.xml) mintája.

## <a name="connect-to-the-java-client"></a>Kapcsolódás a Java-ügyfélhez

1. Nyissa meg a `App.java` fájlt `src\main\java\com\azure\ams` , és győződjön meg róla, hogy a csomag felül van:

    ```java
    package com.azure.ams;
    ```
1. A Package utasítás alatt adja hozzá a következő importálási utasításokat:
   
   ```java
   import com.microsoft.azure.AzureEnvironment;
   import com.microsoft.azure.credentials.ApplicationTokenCredentials;
   import com.microsoft.azure.management.mediaservices.v2018_07_01.implementation.MediaManager;
   import com.microsoft.rest.LogLevel;
   ```
1. A kérésekhez szükséges Active Directory hitelesítő adatok létrehozásához adja hozzá a következő kódot az App osztály fő metódusához, és állítsa be a [hozzáférési API](./access-api-howto.md)-k által kapott értékeket:
   
   ```java
   final String clientId = "00000000-0000-0000-0000-000000000000";
   final String tenantId = "00000000-0000-0000-0000-000000000000";
   final String clientSecret = "00000000-0000-0000-0000-000000000000";
   final String subscriptionId = "00000000-0000-0000-0000-000000000000";

   try {
      ApplicationTokenCredentials credentials = new ApplicationTokenCredentials(clientId, tenantId, clientSecret, AzureEnvironment.AZURE);
      credentials.withDefaultSubscriptionId(subscriptionId);

      MediaManager manager = MediaManager
              .configure()
              .withLogLevel(LogLevel.BODY_AND_HEADERS)
              .authenticate(credentials, credentials.defaultSubscriptionId());
      System.out.println("signed in");
   }
   catch (Exception e) {
      System.out.println("Exception encountered.");
      System.out.println(e.toString());
   }
   ```
1. Futtassa az alkalmazást.

## <a name="see-also"></a>Lásd még

- [Media Services fogalmak](concepts-overview.md)
- [Java SDK](https://aka.ms/ams-v3-java-sdk)
- [Java-referenciák](/java/api/overview/azure/mediaservices/management)
- [com.microsoft.azure.mediaservices.v2018_07_01: Azure-mgmt-Media](https://search.maven.org/artifact/com.microsoft.azure.mediaservices.v2018_07_01/azure-mgmt-media/1.0.0-beta/jar)

## <a name="next-steps"></a>Következő lépések

Mostantól belefoglalhatja `import com.microsoft.azure.management.mediaservices.v2018_07_01.*;` és megkezdheti az entitások módosítását.

További példákat a [Java SDK-minták](/samples/azure-samples/media-services-v3-java/azure-media-services-v3-samples-using-java/) tárházában talál.
