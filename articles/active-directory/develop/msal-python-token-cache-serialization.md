---
title: Egyéni jogkivonat-gyorsítótár szerializálási (MSAL Python) | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogyan szerializálhatja a token-gyorsítótárat a MSAL for Python számára
services: active-directory
author: rayluo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 11/13/2019
ms.author: rayluo
ms.reviewer: nacanuma
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: 7aaf59377949101ba8dd5c9454b89229e925e859
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87846190"
---
# <a name="custom-token-cache-serialization-in-msal-for-python"></a>Egyéni jogkivonat-gyorsítótár szerializálása a MSAL-ben a Pythonhoz

A MSAL Pythonban a memóriabeli jogkivonat-gyorsítótár, amely az alkalmazás-munkamenet időtartamára is fennmarad, alapértelmezés szerint a [ClientApplication](https://msal-python.readthedocs.io/en/latest/#confidentialclientapplication)példányának létrehozásakor biztosít.

A jogkivonat-gyorsítótár szerializálása, hogy az alkalmazás különböző munkamenetei hozzáférhessenek hozzá, nincs megadva a "kívülről". Ennek oka, hogy a MSAL Python olyan alkalmazás-típusokban használható, amelyek nem férnek hozzá a fájlrendszerhez – például webalkalmazásokhoz. Ha állandó jogkivonat-gyorsítótárat szeretne MSAL Python-alkalmazásban, meg kell adnia az egyéni jogkivonat-gyorsítótár szerializálását.

A jogkivonat-gyorsítótár szerializálásának stratégiái eltérnek attól függően, hogy egy nyilvános ügyfélalkalmazás (asztali) vagy egy bizalmas ügyfélalkalmazás (webalkalmazás, webes API vagy Daemon-alkalmazás) van-e írva.

## <a name="token-cache-for-a-public-client-application"></a>Jogkivonat-gyorsítótár egy nyilvános ügyfélalkalmazás számára

A nyilvános ügyfélalkalmazások egy felhasználó eszközén futnak, és egyetlen felhasználó jogkivonatait kezelhetik. Ebben az esetben a teljes gyorsítótárat egy fájlba szerializálhatja. Ne feledje, hogy a fájl zárolását adja meg, ha az alkalmazás vagy egy másik alkalmazás is egyszerre fér hozzá a gyorsítótárhoz. A jogkivonat-gyorsítótár zárolás nélküli fájlba történő szerializálásának egyszerű példáját a [SerializableTokenCache](https://msal-python.readthedocs.io/en/latest/#msal.SerializableTokenCache) osztály dokumentációjában találja.

## <a name="token-cache-for-a-web-app-confidential-client-application"></a>Jogkivonat-gyorsítótár webalkalmazásokhoz (bizalmas ügyfélalkalmazás)

Webalkalmazások vagy webes API-k esetén használhatja a munkamenetet vagy egy Redis-gyorsítótárat, vagy egy adatbázist a jogkivonat-gyorsítótár tárolására. Felhasználónként egy jogkivonat-gyorsítótárnak kell lennie (fiókkal), ezért ügyeljen arra, hogy a jogkivonat-gyorsítótárat a fiók alapján szerializálja.

## <a name="next-steps"></a>Következő lépések

Lásd: [MS-Identity-Python-WebApp](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/master/app.py#L64-L72) , amelyből megtudhatja, hogyan használható a jogkivonat-gyorsítótár egy Windows-vagy Linux-alapú webalkalmazáshoz vagy webes API-hoz. A példa egy olyan webalkalmazásra mutat, amely meghívja a Microsoft Graph API-t.
