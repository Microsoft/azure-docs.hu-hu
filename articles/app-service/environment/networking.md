---
title: App Service Environment hálózatkezelés
description: App Service Environment hálózatkezelés részletei
author: ccompy
ms.assetid: 6f262f63-aef5-4598-88d2-2f2c2f2bfc24
ms.topic: article
ms.date: 11/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 61059c3e0f9737df6ace338f4252a338ea1f200c
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2020
ms.locfileid: "94663848"
---
# <a name="app-service-environment-networking"></a>App Service Environment hálózatkezelés

> [!NOTE]
> Ez a cikk a App Service Environment v3 (előzetes verzió)
> 

A App Service Environment (bevezetési) a webalkalmazások, az API-alkalmazások és a functions-alkalmazások üzemeltetéséhez használt Azure App Service egyetlen bérlős telepítése. A bevezetési szolgáltatás telepítésekor ki kell választania az Azure-Virtual Network (VNet), amelyet telepíteni kíván. Az összes bejövő és kimenő forgalmi alkalmazás a megadott VNet belül lesz.  

A ASEv3 két alhálózatot használ.  A rendszer egy alhálózatot használ a bejövő forgalmat kezelő privát végponthoz. Ez lehet egy korábban létező alhálózat vagy egy újat.  A bejövő alhálózat más célra is használható, mint a privát végpont. A kimenő alhálózat csak a központból kimenő forgalomhoz használható. Semmi más nem mehet a kihelyezett kimenő alhálózatba.

## <a name="addresses"></a>Címek 
A központilag a következő címeket kell létrehozni:

| Címek típusa | leírás |
|--------------|-------------|
| Bejövő címek | A bejövő címek a szolgáltató által használt magánhálózati végponti címek. |
| Kimenő alhálózat | A kimenő alhálózat egyben a bemenő alhálózat is. Az előzetes verzió ideje alatt ez az alhálózat csak a kimenő forgalomhoz használatos. |
| Windows kimenő címe | Az ebben a központban található Windows-alkalmazások ezt a címeket használják alapértelmezés szerint, amikor kimenő hívásokat küldenek az internetre. |
| Linux kimenő címe | Az ebben a központban található Linux-alkalmazásokban ez a címe alapértelmezés szerint az internetre irányuló kimenő hívásokat fogja használni. |

Ha törli a szolgáltató által használt privát végpontot, akkor nem érheti el az alkalmazásait a szolgáltatónál. Ne törölje a beadáshoz társított Azure DNS privát zónát.  

A szolgáltató a kimenő alhálózaton található címeket használja a szolgáltató által használt infrastruktúra támogatásához. A beApp Servicei csomagok méretezése során több címet is használhat. A központon belüli alkalmazások nem rendelkeznek dedikált címmel a kimenő alhálózaton. Az alkalmazás által a kimenő alhálózaton használt címek az idő múlásával változnak.

## <a name="ports"></a>Portok

A bejelentési adatforgalom a 80-es és a 443-es porton érkezik.  Nincsenek bejövő vagy kimenő portokra vonatkozó követelmények a kiegészítő szolgáltatáshoz. 

## <a name="extra-configurations"></a>További konfigurációk

A ASEv2 eltérően a ASEv3 segítségével megadhatja a hálózati biztonsági csoportokat (NSG) és az útválasztási táblákat (UDR), ahogy azt a korlátozás nélkül megtekintheti. Ha szeretné kényszeríteni az alagút összes kimenő forgalmát egy NVA-eszközre. A WAF-eszközöket a központhoz tartozó összes bejövő forgalom elé helyezheti. 

## <a name="dns"></a>DNS

A szolgáltatón belüli alkalmazások azt a DNS-t fogják használni, amelyhez a VNet konfigurálva van. Ha azt szeretné, hogy egyes alkalmazások más DNS-kiszolgálót használjanak, az Alkalmazásbeállítások WEBSITE_DNS_SERVER és WEBSITE_DNS_ALT_SERVER használatával manuálisan is beállíthatja az alkalmazáson alapuló beállításokat. Az Alkalmazásbeállítás WEBSITE_DNS_ALT_SERVER konfigurálja a másodlagos DNS-kiszolgálót. A másodlagos DNS-kiszolgálót csak akkor használja a rendszer, ha az elsődleges DNS-kiszolgálótól nem érkezik válasz. 

## <a name="preview-limitation"></a>Előzetes verzió korlátozása

Vannak néhány olyan hálózatkezelési funkció, amely nem érhető el a ASEv3-ben.  A ASEv3-ben nem elérhető dolgok a következők:

• FTP • távoli hibakeresés • külső terheléselosztó üzembe helyezése – a tárolók központi telepítésére való hozzáférés lehetősége, amely lehetővé teszi, hogy a globálisan felhasználható virtuális hálózatok – a szolgáltatás-végponttal vagy a privát végponttal védett tárolóval való biztonsági mentés/visszaállítás lehetősége fiók • a szolgáltatás-végpont vagy a privát végpontok által védett kulcstartó-fiókokra vonatkozó beállítások, amelyek lehetővé teszik, hogy a BYOS egy szolgáltatási végpont vagy egy privát végpont által védett Storage-fiókkal rendelkezzenek, a kimenő forgalom Network Watcher vagy NSG folyamatának használatával.
    
    

