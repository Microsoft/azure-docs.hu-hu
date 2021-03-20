---
title: fájl belefoglalása
description: fájl belefoglalása
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: egeaney
ms.custom: include
ms.openlocfilehash: 90d5aae559a317ed509d04c8db3310ff8a5de026
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89069909"
---
## <a name="about-cognitive-services-encryption"></a>Tudnivalók a Cognitive Services titkosításról

Az adattitkosítás és visszafejtés az [FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) -kompatibilis [256 bites AES-](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) titkosítás használatával történik. A titkosítás és a visszafejtés átlátható, ami azt jelenti, hogy a titkosítás és a hozzáférés felügyelve van. Az adatok alapértelmezés szerint védettek, a titkosítás használatához pedig sem a kódot, sem az alkalmazást nem kell módosítania.

## <a name="about-encryption-key-management"></a>A titkosítási kulcsok kezelése

Alapértelmezés szerint az előfizetés a Microsoft által felügyelt titkosítási kulcsokat használja. Az előfizetést az ügyfél által felügyelt kulcsok (CMK) nevű saját kulcsokkal is kezelheti. A CMK nagyobb rugalmasságot biztosít a hozzáférés-vezérlések létrehozásához, forgatásához, letiltásához és visszavonásához. Továbbá az adatok védelméhez használt titkosítási kulcsok naplózására is lehetősége van. Ha az előfizetéshez CMK van konfigurálva, a rendszer dupla titkosítást biztosít, amely második védelmi réteget kínál, miközben lehetővé teszi a titkosítási kulcs vezérlését a Azure Key Vaulton keresztül.