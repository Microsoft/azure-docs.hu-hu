---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: b4a1891eadf2e36bcb94b9f605d91f227fa83a3f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026078"
---
Ez a minta a [Twilio](https://www.twilio.com/) szolgáltatás használatával SMS-üzeneteket küld a mobiltelefonjára. Azure Functions már támogatja a Twilio-t a [Twilio-kötésen](../articles/azure-functions/functions-bindings-twilio.md)keresztül, és a minta ezt a szolgáltatást használja.

Az első dolog, amire szüksége van egy Twilio-fiók. Létrehozhat egy ingyenes at https://www.twilio.com/try-twilio . Ha már rendelkezik fiókkal, adja hozzá a következő három **alkalmazás-beállítást** a Function alkalmazáshoz.

| Alkalmazás-beállítás neve | Érték leírása |
| - | - |
| **TwilioAccountSid**  | A Twilio-fiókhoz tartozó SID |
| **TwilioAuthToken**   | A Twilio-fiók hitelesítési jogkivonata |
| **TwilioPhoneNumber** | A Twilio-fiókhoz társított telefonszám. Ez az SMS-üzenetek küldésére szolgál. |