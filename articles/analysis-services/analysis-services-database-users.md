---
title: Adatbázis-szerepkörök és-felhasználók kezelése a Azure Analysis Servicesban | Microsoft Docs
description: Ismerje meg, hogyan kezelheti az adatbázis-szerepköröket és a felhasználókat egy Azure-beli Analysis Services-kiszolgálón.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: references_regions
ms.openlocfilehash: 31910e92ba4d5cbb1f133eaff6880fafb809b772
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99054093"
---
# <a name="manage-database-roles-and-users"></a>Adatbázis-szerepkörök és-felhasználók kezelése

A modell adatbázis szintjén minden felhasználónak egy szerepkörhöz kell tartoznia. A szerepkörök meghatározott engedélyekkel rendelkező felhasználókat határoznak meg a modell adatbázisához. A szerepkörhöz hozzáadott összes felhasználónak vagy biztonsági csoportnak rendelkeznie kell egy, a-kiszolgálóval megegyező előfizetésben lévő fiókkal egy Azure AD-bérlőben. 

A szerepkörök definiálása eltérő lehet a használt eszköztől függően, de a hatás ugyanaz.

A szerepkör engedélyei a következők:
*  **Rendszergazda** – a felhasználók teljes körű engedélyekkel rendelkeznek az adatbázishoz. A rendszergazdai engedélyekkel rendelkező adatbázis-szerepkörök eltérnek a kiszolgálói rendszergazdáktól.
*  **Folyamat** – a felhasználók csatlakozhatnak az adatbázishoz, és elvégezhetik a folyamatokat, és elemezhetik a modell adatbázis-adataikat.
*  **Olvasási** – a felhasználók egy ügyfélalkalmazás segítségével csatlakozhatnak a modell adatbázis-adatbázisaihoz, és elemezhetik azokat.

Táblázatos modell projekt létrehozásakor szerepköröket hozhat létre, és felhasználókat vagy csoportokat adhat hozzá ezekhez a szerepkörökhöz a Visual Studióban Analysis Services projektek használatával. A kiszolgálókon való üzembe helyezéskor SQL Server Management Studio (SSMS), [Analysis Services PowerShell-parancsmagok](/analysis-services/powershell/analysis-services-powershell-reference)vagy [táblázatos modell parancsnyelv](/analysis-services/tmsl/tabular-model-scripting-language-tmsl-reference) (TMSL) használatával adhat hozzá vagy távolíthat el szerepköröket és felhasználói tagokat.

**Biztonsági csoport** hozzáadásakor használja a t `obj:groupid@tenantid` .

**Egyszerű szolgáltatásnév** hozzáadásakor `app:appid@tenantid` .

## <a name="to-add-or-manage-roles-and-users-in-visual-studio"></a>Szerepkörök és felhasználók hozzáadása vagy kezelése a Visual Studióban  
  
1.  A **táblázatos modell Explorerben** kattintson a jobb gombbal a **szerepkörök** elemre.  
  
2.  A **szerepkör-kezelőben** kattintson az **új** elemre.  
  
3.  Adja meg a szerepkör nevét.  
  
     Alapértelmezés szerint a rendszer az alapértelmezett szerepkör nevét fokozatosan számozza az egyes új szerepkörökhöz. Javasoljuk, hogy adjon meg egy nevet, amely egyértelműen azonosítja a tag típusát, például: pénzügyi vezetők vagy emberi erőforrások szakemberei.  
  
4.  Válasszon egyet a következő engedélyek közül:  
  
    |Engedély|Description|  
    |----------------|-----------------|  
    |**Nincs**|A tagok nem tudják olvasni vagy módosítani a modell sémáját, és nem tudják lekérdezni az adatlekérdezéseket.|  
    |**Olvasni**|A tagok adatlekérdezéseket végezhetnek (a sorok szűrőinek alapján), de nem módosíthatják a modell sémáját.|  
    |**Olvasás és feldolgozás**|A tagok adatlekérdezéseket végezhetnek (a sor szintű szűrők alapján), és futtathatják a folyamatokat, és az összes műveletet feldolgozzák, de nem módosíthatják a modell sémáját.|  
    |**Folyamat**|A tagok az összes művelet feldolgozását és feldolgozását is futtathatják. A modell sémája nem olvasható vagy nem módosítható, és nem lehet lekérdezni az adatlekérdezéseket.|  
    |**Rendszergazda**|A tagok módosíthatják a modell sémáját, és lekérhetik az összes adatlekérdezést.|   
  
5.  Ha a létrehozandó szerepkör olvasási vagy olvasási és feldolgozási engedéllyel rendelkezik, akkor a DAX-képletek használatával hozzáadhat sorokhoz tartozó szűrőket. Kattintson a **sorok szűrők** fülre, majd jelöljön ki egy táblát, majd kattintson a **DAX-szűrő** mezőre, majd írjon be egy DAX-képletet.
  
6.  Kattintson a **tagok**  >  **külső hozzáadása** elemre.  
  
8.  A **külső tag hozzáadása** területen adja meg a bérlői Azure ad-beli felhasználókat vagy csoportokat e-mail-cím szerint. Miután rákattintott az OK gombra, és lezárta a szerepkör-kezelőt, a szerepkörök és szerepkörök tagjai megjelennek a táblázatos modell Explorerben. 
 
     ![Szerepkörök és felhasználók a táblázatos modell Explorerben](./media/analysis-services-database-users/aas-roles-tmexplorer.png)

9. Telepítse a Azure Analysis Services-kiszolgálóra.


## <a name="to-add-or-manage-roles-and-users-in-ssms"></a>Szerepkörök és felhasználók hozzáadása vagy kezelése a SSMS-ben

Szerepkörök és felhasználók egy központilag telepített modell-adatbázishoz való hozzáadásához kiszolgálói rendszergazdaként vagy már egy rendszergazdai engedélyekkel rendelkező adatbázis-szerepkörben kell csatlakoznia a kiszolgálóhoz.

1. Az Object Exporer kattintson a jobb gombbal a **szerepkörök**  >  **Új szerepkör** elemre.

2. A **szerepkör létrehozása** területen adja meg a szerepkör nevét és leírását.

3. Válasszon ki egy engedélyt.

   |Engedély|Description|  
   |----------------|-----------------|  
   |**Teljes hozzáférés (rendszergazda)**|A tagok módosíthatják a modell sémáját, feldolgozhatják és lekérhetik az összes adatlekérdezést.| 
   |**Adatbázis feldolgozása**|A tagok az összes művelet feldolgozását és feldolgozását is futtathatják. A modell sémája nem módosítható, és nem lehet adatlekérdezést végrehajtani.|  
   |**Olvasni**|A tagok adatlekérdezéseket végezhetnek (a sorok szűrőinek alapján), de nem módosíthatják a modell sémáját.|  
  
4. Kattintson a **tagság** elemre, majd adjon meg egy felhasználót vagy csoportot a bérlő Azure ad-ban e-mail-cím szerint.

     ![Felhasználó hozzáadása](./media/analysis-services-database-users/aas-roles-adduser-ssms.png)

5. Ha a létrehozandó szerepkör olvasási engedéllyel rendelkezik, akkor egy DAX-képlet használatával adhat hozzá sor szűrőket. Kattintson a **sorok szűrők** elemre, jelöljön ki egy táblát, majd írjon be egy DAX-képletet a **DAX-szűrő** mezőbe. 

## <a name="to-add-roles-and-users-by-using-a-tmsl-script"></a>Szerepkörök és felhasználók hozzáadása TMSL-parancsfájl használatával

A TMSL parancsfájlt a SSMS XMLA ablakában vagy a PowerShell használatával futtathatja. Használja a [CreateOrReplace](/analysis-services/tmsl/createorreplace-command-tmsl) parancsot és a [roles](/analysis-services/tmsl/roles-object-tmsl) objektumot.

**Példa TMSL-parancsfájlra**

Ebben a példában egy B2B külső felhasználót és egy csoportot adnak hozzá az elemző szerepkörhöz a SalesBI-adatbázis olvasási engedélyeivel. A külső felhasználónak és a csoportnak ugyanabban a bérlői Azure AD-ben kell lennie.

```
{
  "createOrReplace": {
    "object": {
      "database": "SalesBI",
      "role": "Analyst"
    },
    "role": {
      "name": "Users",
      "description": "All allowed users to query the model",
      "modelPermission": "read",
      "members": [
        {
          "memberName": "user1@contoso.com",
          "identityProvider": "AzureAD"
        },
        {
          "memberName": "group1@adventureworks.com",
          "identityProvider": "AzureAD"
        }
      ]
    }
  }
}
```

## <a name="to-add-roles-and-users-by-using-powershell"></a>Szerepkörök és felhasználók hozzáadása a PowerShell használatával

A [SQLServer](/analysis-services/powershell/analysis-services-powershell-reference) modul a feladat-specifikus adatbázis-kezelési parancsmagokat és az általános célú Invoke-ASCmd parancsmagot tartalmazza, amely egy táblázatos modell PARANCSNYELV (TMSL) lekérdezését vagy parancsfájlját fogadja el. Az adatbázis-szerepkörök és a felhasználók kezeléséhez a következő parancsmagok használhatók.
  
|Parancsmag|Leírás|
|------------|-----------------| 
|[Add-RoleMember](/powershell/module/sqlserver/Add-RoleMember)|Vegyen fel egy tagot egy adatbázis-szerepkörbe.| 
|[Remove-RoleMember](/powershell/module/sqlserver/remove-rolemember)|Egy tag eltávolítása egy adatbázis-szerepkörből.|   
|[Meghívás – ASCmd](/powershell/module/sqlserver/invoke-ascmd)|TMSL-szkript végrehajtása.|

## <a name="row-filters"></a>Sorok szűrői  

A sorok szűrői határozzák meg, hogy egy adott szerepkör tagjai hogyan tudják lekérdezni egy tábla sorait. A sorokhoz tartozó szűrők a modell minden egyes táblájához a DAX-képletek használatával definiálhatók.  
  
A sorok szűrői csak olvasási és olvasási és feldolgozási engedélyekkel rendelkező szerepkörökhöz definiálhatók. Alapértelmezés szerint, ha egy adott táblához nincs megadva egy sor szűrő, a tagok a tábla összes sorát lekérhetik, kivéve, ha a szűrés egy másik táblából történik.
  
 A sorok szűrői egy DAX-képletet igényelnek, amelyet igaz/hamis értékre kell kiértékelni az adott szerepkör tagjai által lekérdezhető sorok definiálásához. Nem lehet lekérdezni a DAX-képletben nem szereplő sorokat. Például az ügyfelek tábla a következő sorcsoport-kifejezéssel, *= ügyfelek [ország] = "USA"*, az értékesítési szerepkör tagjai csak az USA-beli ügyfeleket láthatják.  
  
A sorok szűrői a megadott sorokra és a kapcsolódó sorokra vonatkoznak. Ha egy táblának több kapcsolata van, a szűrők az aktív kapcsolat biztonságát alkalmazzák. A sorok szűrői a kapcsolódó táblákhoz definiált más sorokkal vannak összemetszve, például:  
  
|Tábla|DAX-kifejezés|  
|-----------|--------------------|  
|Régió|= Régió [ország] = "USA"|  
|ProductCategory|= ProductCategory [név] = "kerékpárok"|  
|Tranzakciók|= Tranzakciók [év] = 2016|  
  
 A háló hatására a tagok lekérhetik az adatsorokat, ahol az ügyfél az USA-ban található, a termék kategóriája kerékpárok, az év pedig 2016. A felhasználók nem tudják lekérdezni az Egyesült Államokon kívüli tranzakciókat, a nem kerékpárokat vagy a 2016-es tranzakciókat, kivéve, ha azok egy másik szerepkör tagja, amely ezeket az engedélyeket megadja.
  
 A szűrő, *= false ()* használatával megtagadhatja a hozzáférést egy teljes tábla összes sorához.

## <a name="next-steps"></a>Következő lépések

  [Kiszolgáló-rendszergazdák kezelése](analysis-services-server-admins.md)   
  [Az Azure Analysis Services kezelése a PowerShell-lel](analysis-services-powershell.md)  
  [Táblázatos modell programozási nyelv (TMSL) – dokumentáció](/analysis-services/tmsl/tabular-model-scripting-language-tmsl-reference)
