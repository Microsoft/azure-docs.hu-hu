---
title: App Service Environment áttekintése
description: A App Service Environment áttekintése
author: ccompy
ms.assetid: 3d37f007-d6f2-4e47-8e26-b844e47ee919
ms.topic: article
ms.date: 03/02/2021
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 23b23340550ded3642d19500270f06cfb6faf8cb
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101735097"
---
# <a name="app-service-environment-overview"></a>App Service Environment áttekintése 

> [!NOTE]
> Ez a cikk a App Service Environment v3 (előzetes verzió)
> 

Az Azure App Service Environment egy Azure App Service-funkció, amely teljesen elkülönített és dedikált környezetet biztosít az App Service-alkalmazások biztonságos, nagy léptékű futtatásához. Ez a szolgáltatás a következők üzemeltetésére képes:

- Windows-webalkalmazások
- Linux-webalkalmazások
- Docker-tárolók
- Functions

Az App Service Environment (ASE) a következő igényekkel rendelkező összes alkalmazási számítási feladat elvégzésére használható:

- Nagy léptékű.
- Elkülönítés és biztonságos hálózati hozzáférés.
- Magas memóriakihasználtság.
- Magas kérelmek/másodperc (RPS). Több ASE is létrehozhat egyetlen Azure-régióban vagy több Azure-régión belül. Ez a rugalmasság lehetővé teszi, hogy a ASE ideális legyen az állapot nélküli, magas RPS-követelményekkel rendelkező alkalmazások számára.

A beszállítói alkalmazások csak egy ügyfélből származnak, és az egyik virtuális hálózatok. Az ügyfelek teljes mértékben szabályozhatják az alkalmazás bejövő és kimenő hálózati adatforgalmát. Az alkalmazások nagy sebességű, biztonságos VPN-kapcsolatokat létesíthetnek a helyszíni vállalati erőforrásokkal.

A ASEv3 a saját díjszabási szintjével, elkülönített v2-mel rendelkezik.
A App Service Environments v3 környezetet biztosít az alkalmazások biztonságának biztosításához a hálózat egy alhálózatán, és az Azure App Service saját privát üzembe helyezését teszi lehetővé.
Több ASE is felhasználható a horizontális skálázásra. Az ASE környezetekben futó alkalmazások hozzáférésében sorompós kapcsolatok alakíthatók ki alsóbb rétegbeli eszközök, például webalkalmazás-tűzfalak (WAF-ok) segítségével. További információ: Webalkalmazás-tűzfal (WAF).

## <a name="usage-scenarios"></a>Használati forgatókönyvek

A App Service Environment számos felhasználási esettel rendelkezik, beleértve a következőket:

- Belső üzletági alkalmazások
- Több mint 30 ASP-példányt igénylő alkalmazások
- Önálló bérlői rendszer a belső megfelelőségi vagy biztonsági követelmények kielégítése érdekében
- Hálózati elkülönített alkalmazás üzemeltetése
- Többrétegű alkalmazások

Számos hálózati funkció lehetővé teszi, hogy az alkalmazások a több-bérlős App Service a hálózat izolált erőforrásainak elérésére vagy a hálózat elkülönítésére legyenek képesek. Ezek a funkciók az alkalmazás szintjén engedélyezettek.  A benyújtó nem rendelkezik további konfigurációval az alkalmazásokban, hogy azok a VNet legyenek. Az alkalmazások olyan hálózati elkülönített környezetbe vannak telepítve, amely már egy VNet van. A hálózatról elkülönített, különálló alkalmazásokat üzemeltető szolgáltatón kívül egy egybérlős rendszer is működik. Nincsenek más ügyfelek, akik a közszolgáltatást használják. Ha valóban szüksége van egy teljes elkülönítési történetre, akkor azt is megteheti, hogy dedikált hardverre helyezi üzembe a bevezetését. A hálózati elkülönített alkalmazások, az önálló bérletek és a 

## <a name="dedicated-environment"></a>Dedikált környezet
A kiegészítő csomag kizárólag egyetlen előfizetéshez van hozzárendelve, és a 200-es számú App Service több App Service csomagra is képes tárolni. A "példány" szó App Service vízszintes skálázási tervre hivatkozik. Az egyes példányok egy feldolgozói szerepkörrel egyenértékűek. Míg a központnak 200 összes példánya lehet, egy elkülönített v2 App Service csomag 100 példányt tartalmazhat. A kiegészítő 100 csomag két App Service csomagot képes tárolni az egyes, 200 egypéldányos App Service csomagok esetében, vagy minden a kettő között.

Egy ASE előtérrendszerekből és feldolgozókból áll. Az előtérrendszerek a HTTP/HTTPS-végződtetésért, valamint az alkalmazáskérések egy ASE-n belüli automatikus terheléselosztásáért felelősek. Az előtérrendszerek az ASE App Service-csomagjainak felskálázásakor automatikusan hozzáadódnak.

A feldolgozók az ügyfélalkalmazások üzemeltetéséért felelős szerepkörök. A feldolgozók három állandó méretben érhetők el:

- Két vCPU/8 GB RAM
- Négy vCPU/16 GB RAM
- Nyolc vCPU/32 GB RAM

Az ügyfeleknek nincs szükségük az előtér-és a munkavégzők felügyeletére. Minden infrastruktúra automatikusan történik. Mivel az App Service-csomagok egy ASE-n belül vannak létrehozva vagy skálázva, ezért a szükséges infrastruktúra a helyzetnek megfelelően adható hozzá vagy távolítható el.

Az elkülönített v2 App Service csomag példányaiért díjat számítunk fel. Ha nem rendelkezik App Service csomaggal a teljes vállalaton belül, akkor számítunk fel díjat, mintha egyetlen App Service megvásárolta volna a két fő feldolgozó egyik példányát.

## <a name="virtual-network-support"></a>Virtuális hálózatok támogatása
A bevezetési funkció a Azure App Service közvetlen üzembe helyezése az ügyfél Azure Resource Manager virtuális hálózatában. A virtuális hálózatok alhálózatában mindig van egy bekapcsoló. A virtuális hálózatok biztonsági funkciói segítségével szabályozhatja az alkalmazásai bejövő és kimenő hálózati kommunikációját.

A hálózati biztonsági csoportok korlátozzák az ASE alhálózatára beérkező hálózati kommunikációt. A hálózati biztonsági csoportok használatával futtathatja az alkalmazásait alsóbb rétegbeli eszközök és szolgáltatások, valamint WAF-ok és hálózati SaaS-szolgáltatók mögött.

Az alkalmazásoknak gyakran kell hozzáférniük vállalati erőforrásokhoz, például belső adatbázisokhoz vagy webes szolgáltatásokhoz. Ha olyan virtuális hálózatban telepíti az ASE környezetet, amely VPN-kapcsolatban van a helyszíni hálózattal, akkor az ASE környezeten belüli alkalmazások hozzáférhetnek a helyszíni erőforrásokhoz. Ez mind a helyek közötti, mind az Azure ExpressRoute VPN-kapcsolatok esetében igaz.

## <a name="preview"></a>Előnézet
A App Service Environment v3 nyilvános előzetes verzióban érhető el.  Néhány funkció az előzetes verzió alatt lesz hozzáadva. A ASEv3 jelenlegi korlátai a következők:

- A App Service-csomag 50 példányon túli méretezésének lehetősége
- Nem lehet lekérni egy tárolót egy privát beállításjegyzékből
- A jelenleg nem támogatott App Service funkciók nem képesek az ügyfél VNet
- Nincs külső üzembe helyezési modell internetről elérhető végponttal
- Nincs parancssori támogatás (AZ CLI és a PowerShell)
- Nincs frissítési képesség a ASEv2 és a ASEv3 között
- Nincs FTP-támogatás
- Nem támogatott néhány App Service-funkció az ügyfél VNet. Backup/Restore, Key Vault referenciák az Alkalmazásbeállítások között, egy privát tároló-beállításjegyzék használatával, valamint a tárolóhoz való diagnosztikai naplózás nem fog működni a szolgáltatási végpontokkal vagy privát végpontokkal.
    
### <a name="asev3-preview-architecture"></a>ASEv3 előzetes verziójának architektúrája
A ASEv3 előzetes verziójában a szolgáltató privát végpontokat használ a bejövő forgalom támogatásához. A privát végpontot a GA a Load Balancer váltja fel. Az előzetes verzióban a kiegészítő szolgáltatás nem támogatja az internetről elérhető végpontok támogatását. Ilyen célra hozzáadhat egy Application Gateway. A kiegészítő adatforrásnak két alhálózaton kell lennie.  A bejövő forgalom egy privát végponton keresztül áramlik. A magánhálózati végpont bármely alhálózatba helyezhető, feltéve, hogy van olyan elérhető címe, amelyet privát végpontok használhatnak.  A kimenő alhálózatnak üresnek kell lennie, és delegálni kell a Microsoft. Web/hostingEnvironments. A kiegészítő szolgáltatás használata közben a kimenő alhálózat nem használható semmi máshoz.

A ASEv3 esetében nincsenek bejövő vagy kimenő hálózati követelmények a bemenő alhálózaton. A forgalmat a hálózati biztonsági csoportokkal és útválasztási táblázatokkal szabályozhatja, és ez csak az alkalmazás forgalmára lesz hatással. Ne törölje a beadáshoz társított privát végpontot, mert a művelet nem vonható vissza. A központhoz használt privát végpontot a kiegészítő csomag összes alkalmazásához használják. 
