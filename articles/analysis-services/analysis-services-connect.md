---
title: Csatlakozás Azure Analysis Services kiszolgálókhoz | Microsoft Docs
description: Útmutató az Azure-beli Analysis Services-kiszolgálóról való kapcsolódáshoz és az adatok lekéréséhez.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: references_regions
ms.openlocfilehash: 4abe1e9c6f9d7b62792936f816b9c46a937be41a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96499424"
---
# <a name="connecting-to-servers"></a>Kapcsolódás kiszolgálókhoz

Ez a cikk a kiszolgálóhoz való csatlakozást ismerteti adatmodellezési és-kezelési alkalmazások, például a SQL Server Management Studio (SSMS) vagy a Visual Studio Analysis Services-projektekkel, valamint olyan ügyfél-jelentési alkalmazásokkal, mint a Microsoft Excel, a Power BI Desktop vagy az egyéni alkalmazások használatával. A Azure Analysis Serviceshoz való csatlakozás HTTPS protokollt használ.

## <a name="client-libraries"></a>Ügyfélkódtárak

[A legújabb ügyféloldali kódtárak beszerzése](/analysis-services/client-libraries?view=azure-analysis-services-current&preserve-view=true)

A kiszolgálóhoz való minden kapcsolat, a típustól függetlenül, frissített AMO-, ADOMD.NET-és OLEDB-ügyféloldali kódtárak szükségesek a Analysis Services-kiszolgálóval való csatlakozáshoz és az ahhoz való kapcsolódáshoz. A SSMS, a Visual studióhoz, az Excel 2016-es és újabb verzióihoz, valamint Power BI a legújabb ügyféloldali kódtárakat a rendszer a havi kiadásokkal telepíti vagy frissíti. Bizonyos esetekben azonban lehetséges, hogy egy alkalmazás nem rendelkezik a legújabbal. Ha például a házirendek késleltetik a frissítéseket, vagy Microsoft 365 a frissítések a késleltetett csatornán vannak.

> [!NOTE]
> Az ügyfél-kódtárak nem tudnak csatlakozni a Azure Analysis Services a felhasználónevet és jelszót igénylő proxykiszolgálók használatával. 

## <a name="server-name"></a>Kiszolgálónév

Amikor létrehoz egy Analysis Services kiszolgálót az Azure-ban, meg kell adnia egy egyedi nevet és azt a régiót, ahol a kiszolgálót létre kívánja hozni. Amikor a kiszolgáló nevét adja meg egy kapcsolatban, a kiszolgáló elnevezési sémája a következőket eredményezi:

```
<protocol>://<region>/<servername>
```
 Ahol a protokoll karakterlánc **asazure**, a régió az az URI, ahol a kiszolgáló létrejött (például westus.asazure.Windows.net), a servername pedig a régión belüli egyedi kiszolgáló neve.

### <a name="get-the-server-name"></a>Kiszolgáló nevének lekérése

A **Azure Portal** > Server > **áttekintő**  >  **kiszolgáló neve** területen másolja a teljes kiszolgálónevet. Ha a szervezet többi felhasználója is csatlakozik ehhez a kiszolgálóhoz, ezt a kiszolgálónevet megoszthatja velük. A kiszolgáló nevének megadásakor a teljes elérési utat kell használni.

![A kiszolgáló nevének lekérése az Azure-ban](./media/analysis-services-deploy/aas-deploy-get-server-name.png)

> [!NOTE]
> Az USA 2. keleti régiójának protokollja a **aspaaseastus2**.

## <a name="connection-string"></a>Kapcsolati sztring

Ha a táblázatos objektummodell használatával csatlakozik Azure Analysis Serviceshoz, használja a következő kapcsolati karakterlánc-formátumokat:

###### <a name="integrated-azure-active-directory-authentication"></a>Integrált Azure Active Directory hitelesítés

Ha elérhető, az integrált hitelesítés felveszi a Azure Active Directory hitelesítő adatokat tartalmazó gyorsítótárat. Ha nem, az Azure bejelentkezési ablak jelenik meg.

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;"
```


###### <a name="azure-active-directory-authentication-with-username-and-password"></a>Hitelesítés Azure Active Directory felhasználónévvel és jelszóval

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;User ID=<user name>;Password=<password>;Persist Security Info=True; Impersonation Level=Impersonate;";
```

###### <a name="windows-authentication-integrated-security"></a>Windows-hitelesítés (beépített biztonság)

Használja az aktuális folyamatot futtató Windows-fiókot.

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>; Integrated Security=SSPI;Persist Security Info=True;"
```

## <a name="connect-using-an-odc-file"></a>Kapcsolat. odc-fájl használatával

Az Excel régebbi verzióival a felhasználók Office-adatkapcsolati (. odc) fájl használatával csatlakozhatnak egy Azure Analysis Services-kiszolgálóhoz. További információt az [Office-Adatkapcsolódási (. odc) fájl létrehozása](analysis-services-odc.md)című témakörben talál.

## <a name="connect-as-a-linked-server-from-sql-server"></a>Kapcsolat csatolt kiszolgálóként SQL Server

SQL Server csatlakozhat egy Azure Analysis Services erőforráshoz [csatolt kiszolgálóként](/sql/relational-databases/linked-servers/create-linked-servers-sql-server-database-engine) úgy, hogy megadja a MSOLAP az adatforrás-szolgáltatóként. A csatolt kiszolgáló kapcsolatának konfigurálása előtt mindenképpen telepítse a legújabb [MSOLAP ügyféloldali függvénytárat](/analysis-services/client-libraries?view=azure-analysis-services-current&preserve-view=true) (Provider). 

A Azure Analysis Services csatolt kiszolgáló kapcsolatai esetén a MSOLAP-szolgáltatót a SQL Server folyamaton kívül kell létrehozni. A csatolt kiszolgáló beállításainak konfigurálásakor győződjön meg arról, hogy az **InProcess engedélyezése** beállítás nincs **bejelölve**.

Ha a leválasztás **engedélyezése** lehetőség be van jelölve, és a szolgáltató a SQL Server folyamat során jön létre, a rendszer a következő hibát adja vissza:

```
OLE DB provider "MSOLAP" for linked server "(null)" returned message "The following system error occurred: ".

OLE DB provider "MSOLAP" for linked server "(null)" returned message "The connection failed because user credentials are needed and Sign-In UI is not allowed.".

Msg 7303, Level 16, State 1, Line 2
Cannot initialize the data source object of OLE DB provider "MSOLAP" for linked server "(null)".
```



## <a name="next-steps"></a>Következő lépések

[Az Excelben való kapcsolat](analysis-services-connect-excel.md)    
[Kapcsolat Power BI](analysis-services-connect-pbi.md)   
[A kiszolgáló kezelése](analysis-services-manage.md)