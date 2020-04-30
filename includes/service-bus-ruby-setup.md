---
author: spelluru
ms.service: service-bus
ms.topic: include
ms.date: 11/09/2018
ms.author: spelluru
ms.openlocfilehash: aec13c6beb8dbfcdd5f38e7f96b86bf03e42fa37
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80986674"
---
## <a name="create-a-ruby-application"></a>Ruby-alkalmazás létrehozása
Útmutatásért lásd: [Ruby-alkalmazás létrehozása az Azure](../articles/virtual-machines/linux/classic/ruby-rails-web-app.md)-ban.

## <a name="configure-your-application-to-use-service-bus"></a>Az alkalmazás konfigurálása Service Bus használatára
Service Bus használatához töltse le és használja az Azure Ruby-csomagot, amely a tárolási REST-szolgáltatásokkal kommunikáló kényelmi könyvtárakat tartalmaz.

### <a name="use-rubygems-to-obtain-the-package"></a>A csomag beszerzése a RubyGems használatával
1. Használjon egy parancssori felületet, mint például a **PowerShell** (Windows), a **Terminal** (Mac), vagy a **Bash** (Unix).
2. A gem és a függőségek telepítéséhez írja be a "gem install Azure" parancsot a parancsablakban.

### <a name="import-the-package"></a>A csomag importálása
A kedvenc szövegszerkesztővel adja hozzá a következőt azon Ruby-fájl elejéhez, amelyben a Storage-t szeretné használni:

```ruby
require "azure"
```

## <a name="set-up-a-service-bus-connection"></a>Service Bus-kapcsolatok beállítása
A következő kód használatával állíthatja be a névtér értékeit, a kulcs, a kulcs, az aláíró és a gazdagép nevét:

```ruby
Azure.configure do |config|
  config.sb_namespace = '<your azure service bus namespace>'
  config.sb_sas_key_name = '<your azure service bus access keyname>'
  config.sb_sas_key = '<your azure service bus access key>'
end
signer = Azure::ServiceBus::Auth::SharedAccessSigner.new
sb_host = "https://#{Azure.sb_namespace}.servicebus.windows.net"
```

Állítsa a névtér értékét a létrehozott értékre a teljes URL-cím helyett. Használja például a **"yourexamplenamespace"** és a "yourexamplenamespace.servicebus.Windows.net" kifejezést.

Több névtér használata esetén a kulcsot és annak nevét átadhatja a konstruktornak az objektumok létrehozásakor `SharedAccessSigner` .

```ruby
sb_namespace = '<your azure service bus namespace>'
sb_sas_key_name = '<your azure service bus access keyname>'
sb_sas_key = '<your azure service bus access key>'

signer = Azure::ServiceBus::Auth::SharedAccessSigner.new(sb_sas_key_name, sb_sas_key)
sb_host = "https://#{sb_namespace}.servicebus.windows.net"
```
