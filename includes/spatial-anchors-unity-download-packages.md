---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 2/3/21
ms.author: parkerra
ms.openlocfilehash: f6c2780ccbb914228a9870cb3b5fe4b0e3d0b214
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2021
ms.locfileid: "99569647"
---
A szükséges csomagok letöltéséhez telepítenie kell a <a href="https://www.npmjs.com/get-npm" target="_blank">NPM</a> .

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