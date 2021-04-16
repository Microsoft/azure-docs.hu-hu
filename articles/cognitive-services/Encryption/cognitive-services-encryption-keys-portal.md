---
title: Customer-Managed kulcsok a Cognitive Services
titleSuffix: Cognitive Services
description: Ismerje meg, hogyan konfigurálhatja az ügyfelek által Azure Portal kulcsokat a Azure Key Vault. Az ügyfél által kezelt kulcsokkal hozzáférés-vezérlést hozhat létre, forgathat, tilthat le és vonhat vissza.
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 04/07/2021
ms.author: egeaney
ms.openlocfilehash: 7c7476a3ab885e9c127cbd571ad723864bf0d898
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534567"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-for-cognitive-services"></a>Felhasználó által kezelt kulcsok konfigurálása Azure Key Vault Cognitive Services

A kulcskulcsok Customer-Managed folyamat termékenként Azure Key Vault Cognitive Services függ. A szolgáltatásspecifikus utasításokhoz használja az alábbi hivatkozásokat:

## <a name="vision"></a>Látás

* [Custom Vision adatok titkosítása](../custom-vision-service/encrypt-data-at-rest.md)
* [Face Services – Az adatok titkosítása az adatáttitkosítás során](../face/encrypt-data-at-rest.md)
* [Form Recognizer adatok titkosítása](../form-recognizer/encrypt-data-at-rest.md)

## <a name="language"></a>Nyelv

* [Language Understanding adatok szolgáltatástitkosításának használata](../LUIS/encrypt-data-at-rest.md)
* [QnA Maker adatok titkosítása](../QnAMaker/encrypt-data-at-rest.md)
* [Az adattitkosítás fordítója](../translator/encrypt-data-at-rest.md)

## <a name="speech"></a>Speech

* [Az adatok beszédtitkosítása az adatáttitkosítás során](../speech-service/speech-encryption-of-data-at-rest.md)

## <a name="decision"></a>Döntés

* [Content Moderator adatok titkosítása](../Content-Moderator/encrypt-data-at-rest.md)
* [Az adattitkosítás személyre szabása az adatokon](../personalizer/encrypt-data-at-rest.md)
* [Metrics Advisor adatok titkosítása](../metrics-advisor/encryption.md)

## <a name="next-steps"></a>Következő lépések

* [Mi az a Azure Key Vault?](../../key-vault/general/overview.md)
* [Cognitive Services Customer-Managed kérelem űrlapja](https://aka.ms/cogsvc-cmk)