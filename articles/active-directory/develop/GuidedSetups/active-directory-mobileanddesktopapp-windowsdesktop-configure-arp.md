---
title: Azure AD v2 Windows asztali Első lépések – konfiguráció | Microsoft Docs
description: Egy Windowsos asztali .NET-(XAML-) alkalmazás hozzáférési jogkivonatának beszerzése és egy Azure Active Directory v2-végpont által védett API meghívása.
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: ryanwi
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a058024f8d6bdf7399e222c134f9f24c4ddffee8
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68835561"
---
# <a name="add-the-applications-registration-information-to-your-app"></a>Adja hozzá az alkalmazás regisztrációs adatait az alkalmazáshoz
Ebben a lépésben hozzá kell adnia az alkalmazás azonosítóját a projekthez.

1.  Nyissa meg `App.xaml.cs` és cserélje le a `ClientId` -t tartalmazó sort:

```csharp
private static string ClientId = "[Enter the application Id here]";
```

### <a name="what-is-next"></a>Mi a következő lépés

[!INCLUDE [Test and Validate](../../../../includes/active-directory-develop-guidedsetup-windesktop-test.md)]
