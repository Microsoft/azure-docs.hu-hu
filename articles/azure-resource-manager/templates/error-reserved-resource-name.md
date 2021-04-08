---
title: Lefoglalt erőforrás neve hibák
description: Ismerteti, Hogyan oldhatók fel hibák a fenntartott szót tartalmazó erőforrások nevének megadásakor.
ms.topic: troubleshooting
ms.date: 11/08/2017
ms.openlocfilehash: e76f4bf9bfee7de6e7523d69acf1388d2dd80e93
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "75477628"
---
# <a name="resolve-reserved-resource-name-errors"></a>Lefoglalt erőforrás-nevek feloldási hibáinak elhárítása

Ez a cikk azt a hibát ismerteti, amikor olyan erőforrást telepít, amely tartalmaz egy fenntartott szót a nevében.

## <a name="symptom"></a>Hibajelenség

Nyilvános végponton keresztül elérhető erőforrás telepítésekor a következő hibaüzenetet kaphatja:

```
Code=ReservedResourceName;
Message=The resource name <resource-name> or a part of the name is a trademarked or reserved word.
```

## <a name="cause"></a>Ok

A nyilvános végponttal rendelkező erőforrások nem használhatják a névben foglalt szavakat vagy védjegyeket.

A következő szavak vannak fenntartva:

* HOZZÁFÉRÉS
* AZURE
* BING
* BIZSPARK
* BIZTALK
* CORTANA
* DIRECTX
* DOTNET
* DYNAMICS
* EXCEL
* EXCHANGE
* FOREFRONT
* GROOVE
* HOLOLENS
* HYPERV
* KINECT
* LYNC
* MSDN
* O365
* OFFICE
* Office 365
* ONEDRIVE
* ONENOTE
* OUTLOOK
* POWERPOINT
* SHAREPOINT
* SKYPE
* VISIO
* VISUALSTUDIO

A következő szavak nem használhatók teljes kifejezésként vagy alsztringként a névben:

* BEJELENTKEZÉSI
* MICROSOFT
* WINDOWS
* XBOX

## <a name="solution"></a>Megoldás

Olyan nevet adjon meg, amely nem használja a fenntartott szavak egyikét.