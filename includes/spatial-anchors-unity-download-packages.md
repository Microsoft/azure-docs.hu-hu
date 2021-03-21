---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 03/18/2021
ms.author: parkerra
ms.openlocfilehash: d91c0aeda2b7ae2f133d2099cbc9d238fd19d287
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104719623"
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

> [!WARNING]
> Az ASA SDK-2.7.0 a minimális támogatott verzió. Ha az Unity 2020-et használja, az ASA SDK 2.9.0 a támogatott verzió.

Az Azure térbeli horgonyok alapcsomag le lesz töltve arra a mappába, ahová a parancsot futtatta.

Ezt a lépést megismételve töltse le a csomagot minden olyan platformon (Android/iOS/HoloLens), amelyet támogatni szeretne a projektben.

| Platform | Csomag neve                                    |
|----------|-------------------------------------------------|
| Android  | com. microsoft. Azure. térbeli – horgonyok – sdk.android@ <version_number> |
| iOS      | com. microsoft. Azure. térbeli – horgonyok – sdk.ios@ <version_number>     |
| HoloLens | com. microsoft. Azure. térbeli – horgonyok – sdk.windows@ <version_number> |