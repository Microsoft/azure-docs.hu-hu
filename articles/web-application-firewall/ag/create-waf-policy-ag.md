---
title: Webalkalmazási tűzfal (WAF) szabályzatok létrehozása a Application Gatewayhoz
description: Megtudhatja, hogyan hozhat létre webalkalmazási tűzfal-házirendeket a Application Gatewayhoz.
services: web-application-firewall
ms.topic: conceptual
author: vhorne
ms.service: web-application-firewall
ms.date: 02/08/2020
ms.author: victorh
ms.openlocfilehash: 26078c3757e42c3e290a5f4122461b287582fb80
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96518821"
---
# <a name="create-web-application-firewall-policies-for-application-gateway"></a>Webalkalmazási tűzfal házirendjeinek létrehozása a Application Gatewayhoz

A WAF szabályzatok a figyelőkkel való társítása lehetővé teszi, hogy a különböző szabályzatok által védett, egyetlen WAF mögötti helyek is védve legyenek. Ha például öt hely van a WAF mögött, öt különálló WAF-szabályzat közül választhat (egyet az egyes figyelőknél) a kizárások, az egyéni szabályok és a felügyelt szabályrendszerek egy adott helyhez való testreszabásához anélkül, hogy a másik négyet kellene végrehajtania. Ha egyetlen szabályzatot szeretne alkalmazni az összes webhelyre, egyszerűen társíthatja a szabályzatot a Application Gatewayhoz, nem pedig az egyes figyelőkhöz, hogy az alkalmazás globálisan legyen alkalmazva. A házirendek egy elérésiút-alapú útválasztási szabályra is alkalmazhatók. 

Tetszőleges számú szabályzatot készíthet. Miután létrehozta a szabályzatot, ahhoz társítania kell egy Application Gateway, hogy az érvénybe lépjen, de az Application Gateway és a figyelők tetszőleges kombinációjával társítható. 

Ha a Application Gateway szabályzatot alkalmaz, majd egy másik szabályzatot alkalmaz egy figyelőre az adott Application Gateway, akkor a figyelő házirendje érvénybe lép, csak azokhoz a figyelőkhöz, amelyekhez hozzá van rendelve. A Application Gateway szabályzat továbbra is érvényes minden olyan figyelőre, amely nem rendelkezik a hozzájuk rendelt házirenddel. 

   > [!NOTE]
   > Miután egy tűzfalszabály társítva van egy WAF, mindig az adott WAF tartozó házirendnek kell lennie. Felülírhatja ezt a házirendet, de a WAF teljes mértékben nem támogatja a szabályzatok társítását. 

A webalkalmazási tűzfal minden új WAF-beállítása (egyéni szabályok, felügyelt rulset konfigurációk, kizárások stb.) egy WAF-szabályzaton belül él. Ha rendelkezik meglévő WAF, ezek a beállítások továbbra is előfordulhatnak a WAF-konfigurációban. Az új WAF-házirendre való áttérés lépéseiért lásd a jelen cikk későbbi, a [WAF-konfiguráció áttelepítésének WAF-házirendjére](#migrate) című témakörét. 

## <a name="create-a-policy"></a>Szabályzat létrehozása

Először hozzon létre egy alapszintű WAF szabályzatot egy felügyelt alapértelmezett szabálykészlet (DRS) használatával a Azure Portal segítségével.

1. A portál bal felső részén válassza az **erőforrás létrehozása** lehetőséget. Keresse meg a **WAF**, válassza a **webalkalmazási tűzfal** elemet, majd kattintson a **Létrehozás** gombra.
2. A **WAF házirend létrehozása** lap **alapok** lapján adja meg vagy válassza ki a következő adatokat, fogadja el az alapértelmezett értékeket a többi beállításnál, majd válassza a **felülvizsgálat + létrehozás**:

   |Beállítás  |Érték  |
   |---------|---------|
   |Házirend a következőhöz:     |Regionális WAF (Application Gateway)|
   |Előfizetés     |Adja meg az előfizetés nevét|
   |Erőforráscsoport     |Az erőforráscsoport kiválasztása|
   |Házirend neve     |Adjon egyedi nevet a WAF-házirendnek.|
3. A **társítás** lapon adja meg a következő beállítások egyikét, majd válassza a **Hozzáadás** lehetőséget:

   |Beállítás  |Érték  |
   |---------|---------|
   |Application Gateway hozzárendelése     |Válassza ki a Application Gateway profil nevét.|
   |Figyelők hozzárendelése     |Válassza ki a Application Gateway figyelő nevét, majd válassza a **Hozzáadás** lehetőséget.|

   > [!NOTE]
   > Ha olyan házirendet rendel hozzá a Application Gatewayhoz (vagy figyelőhöz), amely már rendelkezik szabályzattal, a rendszer felülírja az eredeti szabályzatot, és felülírja az új házirendet.
4. Válassza a **Felülvizsgálat + létrehozás**, majd a **Létrehozás** lehetőséget.

   ![WAF szabályzat alapjai](../media/create-waf-policy-ag/waf-policy-basics.png)

## <a name="configure-waf-rules-optional"></a>WAF-szabályok konfigurálása (nem kötelező)

WAF szabályzat létrehozásakor alapértelmezés szerint *észlelési* módban van. Észlelési módban a WAF nem blokkolja a kérelmeket. Ehelyett a rendszer naplózza a megfelelő WAF-szabályokat a WAF-naplókba. A WAF működés közbeni megtekintéséhez módosíthatja a mód beállításait a *megelőzés* lehetőségre. A megelőzési módban a kiválasztott CRS-szabályokban meghatározott egyezési szabályokat a rendszer letiltja és/vagy naplózza a WAF-naplókban.

## <a name="managed-rules"></a>Felügyelt szabályok

Az Azure által felügyelt OWASP-szabályok alapértelmezés szerint engedélyezve vannak. Ha le szeretne tiltani egy szabály csoportjának egy adott szabályát, bontsa ki a szabály csoporton belüli szabályokat, jelölje be a szabály száma előtt található jelölőnégyzetet, majd válassza a **Letiltás** lehetőséget a fenti lapon.

[![Felügyelt szabályok ](../media/create-waf-policy-ag/managed-rules.png)](../media/create-waf-policy-ag/managed-rules-lrg.png#lightbox)

## <a name="custom-rules"></a>Egyéni szabályok

Egyéni szabály létrehozásához válassza az egyéni **szabály hozzáadása** lehetőséget az **Egyéni szabályok** lapon. Ekkor megnyílik az egyéni szabály konfigurációja lap. Az alábbi képernyőfelvételen egy olyan egyéni szabály látható, amely egy kérelem blokkolására van konfigurálva, ha a lekérdezési karakterlánc tartalmazza a szöveges *blockme*.

[![Egyéni szabály ](../media/create-waf-policy-ag/edit-custom-rule.png) szerkesztése](../media/create-waf-policy-ag/edit-custom-rule-lrg.png#lightbox)

## <a name="migrate-your-waf-config-to-a-waf-policy"></a><a name="migrate"></a>WAF-konfiguráció migrálása WAF-házirendre

Ha rendelkezik meglévő WAF, előfordulhat, hogy a portálon néhány változást észlelt. Először meg kell határoznia, hogy milyen típusú házirendet engedélyez a WAF. Három lehetséges állapot létezik:

- Nincs WAF szabályzat
- Csak egyéni szabályok házirend
- WAF szabályzat

Megtudhatja, hogy melyik állapotban van a WAF, ha megtekinti a portálon. Ha a WAF beállításai láthatók, és a Application Gateway nézetből módosíthatók, a WAF az 1. állapotú.

[![WAF-konfiguráció ](../media/create-waf-policy-ag/waf-configure.png)](../media/create-waf-policy-ag/waf-configure-lrg.png#lightbox)

Ha a **webalkalmazási tűzfal** lehetőséget választja, és egy kapcsolódó házirendet jelenít meg, akkor a WAF 2. vagy 3. állapotú. Ha a házirendbe való navigálás után **csak** az egyéni szabályokat és a társított Application Gateway átjárókat jeleníti meg, akkor csak az egyéni szabályok érvényesek.

![WAF egyéni szabályok](../media/create-waf-policy-ag/waf-custom-rules.png)

Ha a házirend-beállításokat és a felügyelt szabályokat is megjeleníti, akkor ez egy teljes webalkalmazási tűzfal házirend. 

![WAF házirend-beállítások](../media/create-waf-policy-ag/waf-policy-settings.png)

## <a name="migrate-to-waf-policy"></a>Migrálás a WAF szabályzatba

Ha egyéni szabályok csak WAF szabályzattal rendelkeznek, akkor érdemes lehet áttérni az új WAF-házirendre. A jövőben a tűzfalszabályok támogatják a WAF házirend-beállításait, a felügyelt szabályrendszerek, a kivételeket és a letiltott szabálykészlet-csoportokat. A korábban a Application Gatewayon elvégzett összes WAF-konfiguráció mostantól a WAF-szabályzaton keresztül történik. 

Az egyéni szabály szerkesztése csak a WAF házirend le van tiltva. A WAF beállításainak, például a szabályok letiltásának, a kizárások hozzáadásának és más beállításoknak a szerkesztéséhez át kell térnie egy új, legfelső szintű tűzfalszabály-erőforrásra.

Ehhez hozzon létre egy *webalkalmazási tűzfal-házirendet* , és rendelje hozzá az Ön által választott Application Gateway (ok) hoz és figyelőhöz. Az új szabályzatnak pontosan meg kell egyeznie a jelenlegi WAF-konfigurációval, ami azt jelenti, hogy minden egyéni szabályt, kizárást, letiltott szabályt stb. át kell másolni a létrehozandó új szabályzatba. Ha rendelkezik a Application Gatewayhoz tartozó szabályzattal, akkor folytathatja a WAF-szabályok és-beállítások módosítását. Ezt Azure PowerShell is elvégezheti. További információ: WAF- [házirend hozzárendelése meglévő Application Gatewayhoz](associate-waf-policy-existing-gateway.md).

Igény szerint áttelepítési parancsfájlt is használhat egy WAF-házirendbe való áttelepítéshez. További információ: [webalkalmazási tűzfal házirendjeinek Áttelepítése Azure PowerShell használatával](migrate-policy.md).

## <a name="force-mode"></a>Kényszerített mód

Ha nem szeretne mindent másolni egy olyan szabályzatba, amely pontosan megegyezik a jelenlegi konfigurációval, beállíthatja a WAF "Force" módba. Futtassa a következő Azure PowerShell kódot, és a WAF kényszerítési módban lesz. Ezt követően bármilyen WAF szabályzatot hozzárendelhet a WAF, még akkor is, ha nem ugyanazokkal a beállításokkal rendelkezik, mint a konfiguráció. 

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway -Name <your Application Gateway name> -ResourceGroupName <your Resource Group name>
$appgw.ForceFirewallPolicyAssociation = $true
```

Ezután ProCeeS a WAF szabályzatnak az Application gatewayhez való hozzárendelésének lépéseit. További információ: WAF- [házirend hozzárendelése meglévő Application Gatewayhoz.](associate-waf-policy-existing-gateway.md)

## <a name="next-steps"></a>Következő lépések

További információ a [webalkalmazási tűzfalon keresztüli CRS-szabályok csoportjairól és szabályairól](application-gateway-crs-rulegroups-rules.md).
