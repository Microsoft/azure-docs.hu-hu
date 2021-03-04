---
author: baanders
description: fájl belefoglalása az Azure Digital Twins-oktatóanyagokhoz – a minta projekt konfigurálása
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: ff9d1f0ff75a6df6c115ecfe2b8c5f71b12049e4
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102036171"
---
## <a name="configure-the-sample-project"></a>A minta projekt konfigurálása

Ezután állítson be egy példa ügyfélalkalmazás-alkalmazást, amely az Azure Digital Twins-példánnyal fog működni.

Navigáljon a gépre az [*Azure Digital Twins teljes körű mintáit*](/samples/azure-samples/digital-twins-samples/digital-twins-samples) korábban letöltött fájlra (és ha még nem tette meg, bontsa ki).

A mappa belsejében navigáljon a _AdtSampleApp_. Nyissa meg a _**AdtE2ESample. SLN**_ alkalmazást a Visual Studio 2019-ben. 

A Visual Studióban válassza ki a _SampleClientApp > **appsettings.jsa**_ fájl megnyitásához a szerkesztési ablakban. Ez egy előre beállított JSON-fájlként fog szolgálni a projekt futtatásához szükséges konfigurációs változók használatával.

A fájl törzsében módosítsa az `instanceUrl` Azure Digital Twins *-példány állomásnév-URL-címére* (az alábbi ábrán látható módon adja hozzá a **_https://_** az *állomásnév* előtt).

```json
{
  "instanceUrl": "https://<your-Azure-Digital-Twins-instance-hostName>"
}
```

Mentse és zárja be a fájlt. 

Ezután konfigurálja a fájl *appsettings.jsa* kimeneti könyvtárba a *SampleClientApp* létrehozásakor. Ehhez kattintson a jobb gombbal a *appsettings.js* fájlra, majd válassza a **Tulajdonságok** elemet. A **Tulajdonságok** ellenőrben keresse meg a *Másolás a kimeneti könyvtárba* tulajdonságot. Módosítsa a másolandó értéket, **Ha az újabb** , ha még nincs beállítva.

:::image type="content" source="../articles/digital-twins/media/includes/copy-config.png" alt-text="Részletek a Visual Studio ablakból: a Megoldáskezelő panel, amelyen a Kiemelt appsettings.jslátható, a Tulajdonságok ablaktábla pedig a &quot;másolás a kimeneti könyvtárba&quot; tulajdonsága a &quot;másolás, ha újabb&quot; értékre van állítva." border="false" lightbox="../articles/digital-twins/media/includes/copy-config.png":::

Tartsa meg a _**AdtE2ESample**_ projektet a Visual Studióban, és folytassa a használatát az oktatóanyagban.

