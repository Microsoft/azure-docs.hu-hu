---
author: nikuklic
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/14/2020
ms.author: nikuklic
ms.openlocfilehash: ee48311647a73c08f8784792cc5d81cb47279c9d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101749922"
---
[!INCLUDE [Emergency Calling Notice](../../../includes/emergency-calling-notice-include.md)]
## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Egy központilag telepített kommunikációs szolgáltatások erőforrása. [Hozzon létre egy kommunikációs szolgáltatások erőforrást](../../create-communication-resource.md).
- A kommunikációs szolgáltatások erőforrásában beszerzett telefonszám. [telefonszám beszerzése](../../telephony-sms/get-phone-number.md).
- A a `User Access Token` hívási ügyfél engedélyezéséhez. További információ a [beszerzéséről `User Access Token` ](../../access-tokens.md)
- Az [alkalmazáshoz való telefonálás hozzáadásával kapcsolatos első lépések](../getting-started-with-calling.md) elvégzése

### <a name="prerequisite-check"></a>Előfeltételek ellenőrzése

- A kommunikációs szolgáltatások erőforrásához tartozó telefonszámok megtekintéséhez jelentkezzen be a [Azure Portalba](https://portal.azure.com/), keresse meg a kommunikációs szolgáltatások erőforrását, és nyissa meg a **telefonszámok** lapot a bal oldali navigációs ablaktáblán.

## <a name="setting-up"></a>Beállítás

### <a name="add-pstn-functionality-to-your-app"></a>PSTN-funkciók hozzáadása az alkalmazáshoz

Adja hozzá a `PhoneNumber` típust az alkalmazáshoz a **MainActivity. Java** módosításával:


```java
import com.azure.android.communication.common.PhoneNumberIdentifier;
```

<!--
> [!TBD]
> Namespace based on input from Komivi Agbakpem. But it does not correlates with other use namespaces in Calling Quickstart. E.g: "com.azure.communication.calling.CommunicationUserIdentifier" or "com.azure.communication.common.client.CommunicationTokenCredential". Double-chek this.
-->

## <a name="start-a-call-to-phone"></a>Telefonhívás indítása telefonra

Adja meg a kommunikációs szolgáltatások erőforrásán belül beszerzett telefonszámot. Ezt a rendszer a hívás elindítására fogja használni:

> [!WARNING]
> Vegye figyelembe, hogy a telefonszámokat E. 164 nemzetközi szabvány formátumban kell megadni. (például: + 12223334444)

Módosítsa `startCall()` az eseménykezelőt a **MainActivity. Java**-ban, hogy kezelje a telefonhívásokat:

```java
    private void startCall() {
        EditText calleePhoneView = findViewById(R.id.callee_id);
        String calleePhone = calleePhoneView.getText().toString();
        PhoneNumberIdentifier callerPhone = new PhoneNumberIdentifier("+12223334444");
        StartCallOptions options = new StartCallOptions();
        options.setAlternateCallerId(callerPhone);
        options.setVideoOptions(new VideoOptions(null));
        call = agent.startCall(
                getApplicationContext(),
                new PhoneNumberIdentifier[] {new PhoneNumberIdentifier(calleePhone)},
                options);
    }
```

## <a name="launch-the-app-and-call-the-echo-bot"></a>Indítsa el az alkalmazást, és hívja meg az ECHO robotot

Az alkalmazás mostantól az eszköztár Run app (alkalmazás futtatása) gombjával (Shift + F10) indítható el. Telefonhívást hívhat meg úgy, hogy telefonszámot biztosít a hozzáadott szövegmezőben, és a **hívás** gombra kattint.
> [!WARNING]
> Vegye figyelembe, hogy a telefonszámokat E. 164 nemzetközi szabvány formátumban kell megadni. (például: + 12223334444)

![A befejezett alkalmazást bemutató képernyőkép.](../media/android/quickstart-android-call-pstn.png)
