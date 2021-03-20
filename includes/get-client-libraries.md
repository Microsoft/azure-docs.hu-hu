---
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 11/25/2018
ms.author: spelluru
ms.openlocfilehash: 9e9057073c8a661e2f3382333abc7ac2778c4ee3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "84680303"
---
### <a name="install-via-composer"></a>Telepítés a Zeneszerzőn keresztül
1. Hozzon létre egy **composer.js** nevű fájlt a projekt gyökérkönyvtárában, és adja hozzá a következő kódot:
   
    ```json
    {
      "require": {
        "microsoft/windowsazure": "*"
      }
    }
    ```
2. A **[zeneszerző. farmakovigilancia][composer-phar]** letöltése a projekt gyökerében.
3. Nyisson meg egy parancssort, és hajtsa végre a következő parancsot a projekt gyökerében
   
    ```
    php composer.phar install
    ```

[php-sdk-github]: https://github.com/Azure/azure-storage-php
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[download-SDK-PHP]: https://github.com/Azure/azure-sdk-for-php
[composer-phar]: http://getcomposer.org/composer.phar
