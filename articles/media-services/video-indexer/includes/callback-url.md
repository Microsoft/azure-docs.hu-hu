---
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 11/13/2020
ms.author: juliako
ms.openlocfilehash: fbc77b960cac0db2d077345c74d64485bd7ad8bb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95994493"
---
Egy URL-cím, amely az ügyfél (POST-kérelem használatával) értesítésére szolgál az alábbi eseményekről:

- Az indexelési állapot változása: 
    - Tulajdonságok:    
    
        |Név|Leírás|
        |---|---|
        |id|A videó azonosítója|
        |állapot|A videó állapota|  
    - Például: https: \/ /test.com/notifyme?projectName=MyProject&ID = 1234abcd&State = Processed
- A videóban azonosított személy:
  - Tulajdonságok
    
      |Név|Leírás|
      |---|---|
      |id| A videó azonosítója|
      |faceId|A videó indexében megjelenő Arcfelismerés|
      |knownPersonId|Az arc-modellen belül egyedi személy azonosítója|
      |personName|A személy neve|
        
    - Például: https: \/ /test.com/notifyme?projectName=MyProject&ID = 1234abcd&faceid = 12&knownPersonId = CCA84350-89B7-4262-861C-3CAC796542A5&personName = Inigo_Montoya 
