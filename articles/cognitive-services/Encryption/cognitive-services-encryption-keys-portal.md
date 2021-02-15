---
title: Cognitive Services Customer-Managed kulcsai
titleSuffix: Cognitive Services
description: Megtudhatja, hogyan konfigurálhatja az ügyfél által felügyelt kulcsokat a Azure Key Vault segítségével a Azure Portal használatával. Az ügyfél által felügyelt kulcsok lehetővé teszik a hozzáférés-vezérlések létrehozását, elforgatását, letiltását és visszavonását.
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 05/28/2020
ms.author: egeaney
ms.openlocfilehash: fca3fc80411432f2e82a68faa3b6f297b8ca5f9c
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/15/2021
ms.locfileid: "100522002"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-for-cognitive-services"></a>Ügyfél által felügyelt kulcsok konfigurálása a Azure Key Vault Cognitive Services

A Customer-Managed kulcsoknak a Cognitive Services Azure Key Vault használatával történő engedélyezésének folyamata termékenként változó. A következő hivatkozásokat használhatja a szolgáltatásra vonatkozó utasításokhoz:

## <a name="vision"></a>Látás

* [Custom Vision inaktív adatok titkosítása](../Custom-Vision-Service/encrypt-data-at-rest.md)
* [Szembenéző szolgáltatások titkosítása inaktív adatok esetén](../Face/encrypt-data-at-rest.md)
* [Űrlap-felismerő titkosítása inaktív adatok esetén](../form-recognizer/encrypt-data-at-rest.md)

## <a name="language"></a>Nyelv

* [Inaktív adatok Language Understanding szolgáltatás titkosítása](../LUIS/encrypt-data-at-rest.md)
* [QnA Maker inaktív adatok titkosítása](../QnAMaker/encrypt-data-at-rest.md)
* [Inaktív adatok Translator-titkosítása](../translator/encrypt-data-at-rest.md)

## <a name="decision"></a>Döntés

* [Content Moderator inaktív adatok titkosítása](../Content-Moderator/encrypt-data-at-rest.md)
* [A REST-adatok személyre szabása](../personalizer/encrypt-data-at-rest.md)

## <a name="next-steps"></a>Következő lépések

* [Mi az Azure Key Vault](../../key-vault/general/overview.md)?
* [Cognitive Services Customer-Managed kulcs kérésének űrlapja](https://aka.ms/cogsvc-cmk)