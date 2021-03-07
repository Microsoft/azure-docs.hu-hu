---
title: Azure Cognitive Services biztonság
titleSuffix: Azure Cognitive Services
description: További információ a Cognitive Services használatának különböző biztonsági szempontjairól.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: erhopf
ms.custom: devx-track-python, devx-track-js, devx-track-csharp
ms.openlocfilehash: 66de81387fe27bd2a81ebcfeab7d86aac7031ff1
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102425294"
---
# <a name="azure-cognitive-services-security"></a>Azure Cognitive Services biztonság

A biztonsági és az összes alkalmazás fejlesztésekor elsődleges prioritásnak kell tekinteni. A mesterséges intelligenciát használó alkalmazások kialakulásával a biztonság még ennél is fontosabb. Ebben a cikkben az Azure Cognitive Services Security különböző szempontjait ismertetjük, mint például a Transport Layer Security, a hitelesítés, a bizalmas adatok biztonságos konfigurálása, valamint az ügyfelek adatelérésének Ügyfélszéf.

## <a name="transport-layer-security-tls"></a>Transport Layer Security (TLS)

A HTTP-n keresztül elérhető összes Cognitive Services-végpont TLS 1,2. A kényszerített biztonsági protokoll használatával az Cognitive Services végpontot meghívó felhasználóknak be kell tartaniuk az alábbi irányelveket:

* Az ügyfél operációs rendszerének (OS) támogatnia kell a TLS 1,2-et
* A HTTP-híváshoz használt nyelv (és platform) a kérelem részeként meg kell határoznia a TLS 1,2-as értéket.
  * A nyelvtől és a platformtól függően a TLS meghatározása implicit módon vagy explicit módon történik

.NET-felhasználók esetén vegye figyelembe a <a href="https://docs.microsoft.com/dotnet/framework/network-programming/tls" target="_blank">Transport Layer Security ajánlott eljárásokat </a>.

## <a name="authentication"></a>Hitelesítés

A hitelesítés megvitatásakor számos gyakori tévhit van. A hitelesítés és az engedélyezés gyakran zavaros egymás számára. Az identitás a biztonság egyik fő összetevője is. Az identitás egy <a href="https://en.wikipedia.org/wiki/Principal_(computer_security)" target="_blank">rendszerbiztonsági tag </a>információinak gyűjteménye. Az Identity Providers (identitásszolgáltató) hitelesítő szolgáltatásokhoz biztosít identitásokat. A hitelesítés a felhasználó identitásának ellenőrzése. Az engedélyezés az adott identitáshoz tartozó erőforrásokhoz való hozzáférési jogosultságok és jogosultságok meghatározása. A Cognitive Services számos ajánlata tartalmaz Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC). Az Azure RBAC felhasználható a rendszerbiztonsági tag manuális kezelésével foglalkozó néhány ünnepség leegyszerűsítésére. További részletek: [Azure szerepköralapú hozzáférés-vezérlés az Azure-erőforrásokhoz](../role-based-access-control/overview.md).

Az előfizetési kulcsokkal, a hozzáférési jogkivonatokkal és a Azure Active Directorysal (HRE) való hitelesítéssel kapcsolatos további információkért lásd: <a href="https://docs.microsoft.com/azure/cognitive-services/authentication" target="_blank">kérések hitelesítése az Azure Cognitive Servicesban</a>.

## <a name="environment-variables-and-application-configuration"></a>Környezeti változók és alkalmazás konfigurációja

A környezeti változók név-érték párok, amelyek egy adott környezetben tárolódnak. A bizalmas adatok hardcoded értékeinek használata biztonságosabb alternatíva a környezeti változók használata. A hardcoded értékei nem biztonságosak, ezért kerülendő.

> [!CAUTION]
> Ne **használjon hardcoded** -értékeket a bizalmas adatokhoz, így ez jelentős biztonsági rést jelent.

> [!NOTE]
> Míg a környezeti változók egyszerű szövegként vannak tárolva, azok el vannak különítve egy környezetbe. Ha egy környezet biztonsága sérül, akkor a környezet változói is.

### <a name="set-environment-variable"></a>Környezeti változó beállítása

A környezeti változók megadásához használja a következő parancsokat – ahol a a `ENVIRONMENT_VARIABLE_KEY` megnevezett kulcs, és a `value` környezeti változóban tárolt érték.

# <a name="command-line"></a>[Parancssor](#tab/command-line)

Megőrzött környezeti változó létrehozása és kiosztása a megadott értékkel.

```CMD
:: Assigns the env var to the value
setx ENVIRONMENT_VARIABLE_KEY="value"
```

A **parancssor** új példányában olvassa el a környezeti változót.

```CMD
:: Prints the env var value
echo %ENVIRONMENT_VARIABLE_KEY%
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Megőrzött környezeti változó létrehozása és kiosztása a megadott értékkel.

```powershell
# Assigns the env var to the value
[System.Environment]::SetEnvironmentVariable('ENVIRONMENT_VARIABLE_KEY', 'value', 'User')
```

A **Windows PowerShell** új példányában olvassa el a környezeti változót.

```powershell
# Prints the env var value
[System.Environment]::GetEnvironmentVariable('ENVIRONMENT_VARIABLE_KEY')
```

# <a name="bash"></a>[Bash](#tab/bash)

Megőrzött környezeti változó létrehozása és kiosztása a megadott értékkel.

```Bash
# Assigns the env var to the value
echo export ENVIRONMENT_VARIABLE_KEY="value" >> /etc/environment && source /etc/environment
```

A **bash** új példányában olvassa el a környezeti változót.

```Bash
# Prints the env var value
echo "${ENVIRONMENT_VARIABLE_KEY}"

# Or use printenv:
# printenv ENVIRONMENT_VARIABLE_KEY
```

---

> [!TIP]
> A környezeti változók beállítása után indítsa újra az integrált fejlesztői környezetet (IDE), és győződjön meg arról, hogy az újonnan hozzáadott környezeti változók elérhetők.

### <a name="get-environment-variable"></a>Környezeti változó beolvasása

Környezeti változó beszerzéséhez a memóriába kell beolvasni. A használt nyelvtől függően vegye figyelembe az alábbi kódrészleteket. Ezek a kódrészletek bemutatják, hogyan kérheti le a környezeti változót `ENVIRONMENT_VARIABLE_KEY` , és hogyan rendelhet hozzá egy nevű változóhoz `value` .

# <a name="c"></a>[C#](#tab/csharp)

További információ: <a href="https://docs.microsoft.com/dotnet/api/system.environment.getenvironmentvariable" target="_blank"> `Environment.GetEnvironmentVariable` </a>.

```csharp
using static System.Environment;

class Program
{
    static void Main()
    {
        // Get the named env var, and assign it to the value variable
        var value =
            GetEnvironmentVariable(
                "ENVIRONMENT_VARIABLE_KEY");
    }
}
```

# <a name="c"></a>[C++](#tab/cpp)

További információ: <a href="https://docs.microsoft.com/cpp/c-runtime-library/reference/getenv-wgetenv" target="_blank"> `getenv` </a>.

```cpp
#include <stdlib.h>

int main()
{
    // Get the named env var, and assign it to the value variable
    auto value =
        getenv("ENVIRONMENT_VARIABLE_KEY");
}
```

# <a name="java"></a>[Java](#tab/java)

További információ: <a href="https://docs.oracle.com/javase/7/docs/api/java/lang/System.html#getenv(java.lang.String)" target="_blank"> `System.getenv` </a>.

```java
import java.lang.*;

public class Program {
   public static void main(String[] args) throws Exception {
    // Get the named env var, and assign it to the value variable
    String value =
        System.getenv(
            "ENVIRONMENT_VARIABLE_KEY")
   }
}
```

# <a name="nodejs"></a>[Node.js](#tab/node-js)

További információ: <a href="https://nodejs.org/api/process.html#process_process_env" target="_blank"> `process.env` </a>.

```javascript
// Get the named env var, and assign it to the value variable
const value =
    process.env.ENVIRONMENT_VARIABLE_KEY;
```

# <a name="python"></a>[Python](#tab/python)

További információ: <a href="https://docs.python.org/2/library/os.html#os.environ" target="_blank"> `os.environ` </a>.

```python
import os

# Get the named env var, and assign it to the value variable
value = os.environ['ENVIRONMENT_VARIABLE_KEY']
```

# <a name="objective-c"></a>[Objective-C](#tab/objective-c)

További információ: <a href="https://developer.apple.com/documentation/foundation/nsprocessinfo/1417911-environment?language=objc" target="_blank"> `environment` </a>.

```objectivec
// Get the named env var, and assign it to the value variable
NSString* value =
    [[[NSProcessInfo processInfo]environment]objectForKey:@"ENVIRONMENT_VARIABLE_KEY"];
```

---

## <a name="customer-lockbox"></a>Ügyfélszéf

[A Ügyfélszéf for Microsoft Azure](../security/fundamentals/customer-lockbox-overview.md) egy felületet biztosít az ügyfelek számára, hogy áttekintsék, jóváhagyják vagy elutasítsák az ügyféladatok hozzáférési kérelmeit. Olyan esetekben használatos, amikor egy Microsoft-mérnöknek egy támogatási kérelem kezelése során hozzá kell férnie az ügyfél adataihoz. További információ a Ügyfélszéf kérelmek elindításáról, nyomon követéséről és a későbbi felülvizsgálatok és auditálások tárolásáról: [Ügyfélszéf](../security/fundamentals/customer-lockbox-overview.md). 

Ügyfélszéf érhető el ehhez a kognitív szolgáltatáshoz:

* Translator

A következő szolgáltatások esetében a Microsoft Engineers nem fér hozzá a E0-szinten található összes ügyfél-adatszolgáltatáshoz: 

* Language Understanding
* Face
* Tartalommoderátor
* Personalizer

> [!IMPORTANT]
> Az **űrlap-felismerő** esetében a Microsoft Engineers nem fér hozzá a 2020. július 10. után létrehozott erőforrásokhoz.

Ha a E0 SKU használatára van lehetősége, töltse ki és küldje el ezt a [kérési űrlapot](https://aka.ms/cogsvc-cmk). Körülbelül 3-5 munkanapot vesz igénybe, hogy visszahallgassa a kérés állapotát. Igénytől függően előfordulhat, hogy egy várólistába helyezi, és a rendszer jóváhagyja a helyet, és elérhetővé válik. Miután jóváhagyta a E0 SKU-val a LUIS-t, létre kell hoznia egy új erőforrást a Azure Portal, és a E0 kell választania az árképzési szinten. A felhasználók nem fognak tudni frissíteni a F0 az új E0 SKU-ra.

A beszédfelismerési szolgáltatás jelenleg nem támogatja a Ügyfélszéf. A vásárlói adatait azonban a saját tároló (BYOS) használatával is tárolhatja, így a Ügyfélszéfhoz hasonló adatellenőrzéseket érhet el. Ne feledje, hogy a beszédfelismerési szolgáltatás adatmennyisége és feldolgozása abban a régióban történik, ahol a beszédfelismerési erőforrás létrejött. Ez a nyugalmi állapotban tárolt adatokra és a tranzitban tárolt adatokra is vonatkozik. A testreszabási funkciók, például a Custom Speech és az egyéni hang használatakor az összes ügyfél-adat átvitele, tárolása és feldolgozása ugyanabban a régióban történik, ahol a BYOS (ha használatban van) és a Speech Service-erőforrás található.

> [!IMPORTANT]
> A Microsoft **nem** használja fel az ügyféladatokat, hogy javítsa a beszédfelismerési modelljeit. Emellett, ha a végpont-naplózás le van tiltva, és nem használ testreszabást, akkor a rendszer nem tárolja az ügyféladatokat. 

## <a name="next-steps"></a>Következő lépések

* Ismerkedjen meg a különböző [Cognitive Servicesokkal](./what-are-cognitive-services.md)
* További információ a [Cognitive Services Virtual Networks](cognitive-services-virtual-networks.md) szolgáltatásról