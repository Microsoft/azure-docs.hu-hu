---
title: AdatCustom Vision Service megtekintése és törlése
titleSuffix: Azure Cognitive Services
description: Teljes körű irányítást tart az adatai felett. Ez a cikk azt ismerteti, hogyan tekintheti meg, exportálhatja vagy törölheti az adatait a Custom Vision Serviceban.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: pafarley
ms.openlocfilehash: fe17fa4349085be47201974b418493fe8d912ece
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "90527394"
---
# <a name="view-or-delete-user-data-in-custom-vision"></a>Felhasználói adatCustom Visionek megtekintése és törlése

Custom Vision gyűjti a felhasználói adatokat a szolgáltatás működtetéséhez, de az ügyfelek az Custom Vision [betanítási API](https://go.microsoft.com/fwlink/?linkid=865446)-kkal teljes mértékben szabályozzák az adataik megtekintését és törlését.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

A következő táblázatból megtudhatja, hogyan tekintheti meg és törölheti a Custom Visionban lévő felhasználói információkat:

| Adatok | Művelet megtekintése | Művelet törlése |
| ---- | ---------------- | ---------------- |
| Fiókadatok (előfizetési kulcsok) | [GetAccountInfo](https://go.microsoft.com/fwlink/?linkid=865446) | Törlés a Azure Portal használatával (Azure-előfizetések). Vagy használja a "fiók törlése" gombot a CustomVision.ai-beállítások lapon (Microsoft-fiók előfizetések) | 
| Iteráció részletei | [GetIteration](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Iterációs teljesítmény részletei | [GetIterationPerformance](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Iterációk listája | [GetIterations](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Projektek és projekt részletei | [GetProject](https://go.microsoft.com/fwlink/?linkid=865446) és [GetProjects](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteProject](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Képcímkék | [GetTag](https://go.microsoft.com/fwlink/?linkid=865446) és [GetTags](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteTag](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Képek | [GetTaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (URI-t biztosít a képletöltéshez) és a [GetUntaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (URI-t biztosít a rendszerképek letöltéséhez) | [DeleteImages](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Exportált iterációk | [GetExports](https://go.microsoft.com/fwlink/?linkid=865446) | Fiók törlésekor törölve |
