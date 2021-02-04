---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 2/3/21
ms.author: parkerra
ms.openlocfilehash: c97067c66296e8980a36b21298a7b2061e0f9b4c
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550386"
---
Futtassa az alábbi parancsot az `<version_number>` aktuális mappába letölteni kívánt Azure térbeli horgonyok verziójának lecserélése után:

```bash
npm pack com.microsoft.azure.spatial-anchors-sdk.core@<version_number> --registry https://api.bintray.com/npm/microsoft/AzureMixedReality-NPM
```

> [!NOTE]
> Az Azure térbeli horgonyok csomag elérhető verzióinak listázásához futtassa a következőt:
>
> ```bash
> npm view com.microsoft.azure.spatial-anchors-sdk.core --registry https://api.bintray.com/npm/microsoft/AzureMixedReality-NPM versions
> ```

Az Azure térbeli horgonyok alapcsomag le lesz töltve arra a mappába, ahová a parancsot futtatta.

Ezt a lépést megismételve töltse le a csomagot minden olyan platformon (Android/iOS/HoloLens), amelyet támogatni szeretne a projektben.

| Platform | Csomag neve                                    |
|----------|-------------------------------------------------|
| Android  | com. microsoft. Azure. térbeli – horgonyok – sdk.android@ <version_number> |
| iOS      | com. microsoft. Azure. térbeli – horgonyok – sdk.ios@ <version_number>     |
| HoloLens | com. microsoft. Azure. térbeli – horgonyok – sdk.windows@ <version_number> |