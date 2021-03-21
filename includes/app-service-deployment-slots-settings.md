---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 09/18/2019
ms.author: cephalin
ms.openlocfilehash: 922a34bd1a147d407041d2945b14df18a18d7287
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "104761233"
---
Ha másik üzembe helyezési pontról klónozott konfigurációt telepít, a klónozott konfiguráció szerkeszthető. Egyes konfigurációs elemek követik a tartalmat a swap (nem tárolóhely-specifikus), míg az egyéb konfigurációs elemek ugyanazon a tárolóhelyen maradnak a swap (tárolóhely-specifikus) után. Az alábbi listában a tárolóhelyek cseréjekor megjelenő beállítások láthatók.

**Felcserélt beállítások**:

* Általános beállítások, mint például a keretrendszer verziója, 32/64 bites, webes szoftvercsatornák
* Alkalmazásbeállítások (beállítható egy tárolóhelyre való ragasztásra)
* Kapcsolódási karakterláncok (beállítható egy tárolóhelyre való ragasztásra)
* Kezelő-hozzárendelések
* Nyilvános tanúsítványok
* Webjobs-tartalom
* Hibrid kapcsolatok *
* Szolgáltatási végpontok *
* Azure Content Delivery Network *

A csillaggal (*) jelölt funkciók nem lesznek lecserélve. 

**Nem felcserélt beállítások**:

* Közzétételi végpontok
* Egyéni tartománynevek
* Nem nyilvános tanúsítványok és TLS/SSL-beállítások
* Méretezési beállítások
* Webjobs-ütemező
* IP-korlátozások
* Always on
* Diagnosztikai beállítások
* Eltérő eredetű erőforrások megosztása (CORS)
* Virtuális hálózat integrációja
* Elérésiút-megfeleltetések

> [!NOTE]
> A beállítások megváltoztatásához adja hozzá az alkalmazás `WEBSITE_OVERRIDE_PRESERVE_DEFAULT_STICKY_SLOT_SETTINGS` minden egyes tárolóhelyén található alkalmazást, és állítsa az értékét a következőre: `0` vagy `false` . Ezek a beállítások vagy mind cserélhetők, akár egyáltalán nem. Csak néhány beállítás cserélhető, és nem.

> A nem cserélt beállításokra vonatkozó bizonyos Alkalmazásbeállítások nem lesznek felcserélve. Mivel például a diagnosztikai beállítások nincsenek felcserélve, a kapcsolódó Alkalmazásbeállítások, például `WEBSITE_HTTPLOGGING_RETENTION_DAYS` a és a nem lesznek `DIAGNOSTICS_AZUREBLOBRETENTIONDAYS` lecserélve, még akkor is, ha nem jelennek meg tárolóhely-beállításokként.
>
