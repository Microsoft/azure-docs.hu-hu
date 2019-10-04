---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 467e09f9bd46df6d888d82f2961c5aed9cca4ab5
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178920"
---
Ez a minta használata szükséges a [Twilio](https://www.twilio.com/) SMS küldése mobiltelefonra szolgáltatást. Az Azure Functions már rendelkezik a Twilio segítségével támogatása a [Twilio kötés](https://docs.microsoft.com/azure/azure-functions/functions-bindings-twilio), és a mintát használja ezt a funkciót.

Először is szüksége egy Twilio-fiók. Létrehozhat egy ingyenes, https://www.twilio.com/try-twilio. Ha már rendelkezik fiókkal, adja hozzá a következő három **Alkalmazásbeállítások** a függvényalkalmazáshoz.

| Alkalmazásbeállítás neve | Érték Leírás |
| - | - |
| **TwilioAccountSid**  | A Twilio-fiók SID-jét |
| **TwilioAuthToken**   | A hitelesítési tokent a Twilio-fiók |
| **TwilioPhoneNumber** | A Twilio-fiókjához társított telefonszám. Ez szolgál, az SMS-EK küldéséhez. |