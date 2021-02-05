---
title: ServiceNow összekötése IT-szolgáltatásmenedzsmenti csatoló
description: Ismerje meg, hogyan csatlakoztathatók a ServiceNow a IT-szolgáltatásmenedzsmenti csatoló (ITSMC) Azure Monitor a ITSM-munkaelemek központilag figyeléséhez és kezeléséhez.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/21/2020
ms.openlocfilehash: 222257f5045984a71c2aee9de83b5fa420306728
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99573415"
---
# <a name="connect-servicenow-with-it-service-management-connector"></a>ServiceNow összekötése IT-szolgáltatásmenedzsmenti csatoló

Ez a cikk bemutatja, hogyan konfigurálható a ServiceNow-példányok és a IT-szolgáltatásmenedzsmenti csatoló (ITSMC) közötti kapcsolat a Log Analyticsban, így központilag kezelheti az informatikai szolgáltatások felügyeleti (ITSM) munkaelemeit.

## <a name="prerequisites"></a>Előfeltételek
Győződjön meg arról, hogy megfelel a következő előfeltételeknek a kapcsolatban:

### <a name="itsmc-installation"></a>ITSMC-telepítés

További információ a ITSMC telepítéséről: [a it-szolgáltatásmenedzsmenti csatoló megoldás hozzáadása](./itsmc-definition.md#add-it-service-management-connector).

> [!NOTE]
> A ITSMC csak a ServiceNow-től származó, szolgáltatásként nyújtott hivatalos szoftveres (SaaS) szolgáltatást támogatja. A ServiceNow privát telepítései nem támogatottak.

### <a name="oauth-setup"></a>OAuth-telepítő

A ServiceNow támogatott verziói a következők: Párizs, Orlandó, New York, Madrid, London, Kingston, Jakarta, Isztambul, Helsinki és Genf.

A ServiceNow-rendszergazdáknak a ServiceNow-példányhoz meg kell adniuk az ügyfél-azonosítót és az ügyfél titkos kulcsát. Szükség szerint tekintse meg a következő információkat:

- [OAuth beállítása Párizshoz](https://docs.servicenow.com/bundle/paris-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [OAuth beállítása az Orlando-hoz](https://docs.servicenow.com/bundle/orlando-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [A New York-i OAuth beállítása](https://docs.servicenow.com/bundle/newyork-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [OAuth beállítása Madridhoz](https://docs.servicenow.com/bundle/madrid-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [A OAuth beállítása Londonban](https://docs.servicenow.com/bundle/london-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [A OAuth beállítása a Kingston számára](https://docs.servicenow.com/bundle/kingston-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [OAuth beállítása a Jakarta számára](https://docs.servicenow.com/bundle/jakarta-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Az OAuth beállítása Isztambulhoz](https://docs.servicenow.com/bundle/istanbul-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [A OAuth beállítása Helsinkihez](https://docs.servicenow.com/bundle/helsinki-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [A OAuth beállítása Genfben](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/security/task/t_SettingUpOAuth.html)

A OAuth beállításának részeként a következőket javasoljuk:

1. [Hozzon létre egy végpontot az ügyfelek számára a példány eléréséhez](https://docs.servicenow.com/bundle/newyork-platform-administration/page/administer/security/task/t_CreateEndpointforExternalClients.html).

1. Frissítse a frissítési jogkivonat élettartamát:

   1. A **ServiceNow** ablaktáblán keressen a **System OAuth** kifejezésre, majd válassza az **alkalmazás beállításjegyzéke** elemet. 
   1. Válassza ki a definiált OAuth nevét, és módosítsa a **frissítési token élettartamát** **7 776 000 másodpercre** (90 nap). 
   1. Válassza a **Frissítés** lehetőséget. 

1. Hozzon létre egy belső eljárást, amely biztosítja, hogy a kapcsolat életben maradjon. Néhány nappal a frissítési jogkivonat élettartamának várható lejárata előtt végezze el a következő műveleteket:

   1. [Fejezze be a manuális szinkronizálási folyamatot a ITSM-összekötő konfigurálásához](./itsmc-resync-servicenow.md).

   1. Visszavonás a régi frissítési jogkivonatra. Biztonsági okokból nem ajánlott a régi kulcsok megőrzése. 
   
      1. A **ServiceNow** ablaktáblán keressen a **System OAuth** kifejezésre, majd válassza a **tokenek kezelése** lehetőséget. 
      
      1. A OAuth neve és a lejárat dátuma szerint válassza ki a régi tokent a listából.

         ![Képernyőkép, amely a OAuth jogkivonatok listáját jeleníti meg.](media/itsmc-connections/snow-system-oauth.png)
      1. Válassza a **hozzáférés** visszavonása Visszavonás lehetőséget  >  .

## <a name="install-the-user-app-and-create-the-user-role"></a>A felhasználói alkalmazás telepítése és a felhasználói szerepkör létrehozása

Az alábbi eljárással telepítheti a szolgáltatás most felhasználói alkalmazást, és létrehozhatja az integrációs felhasználói szerepkört. Ezeket a hitelesítő adatokat fogja használni az Azure-beli ServiceNow-kapcsolatok létrehozásához.

> [!NOTE]
> A ITSMC csak a ServiceNow-tárolóból letöltött Microsoft Log Analytics-integrációhoz használható hivatalos felhasználói alkalmazást támogatja. A ITSMC nem támogatja a kód betöltését a ServiceNow oldalon vagy bármely olyan alkalmazásban, amely nem része a hivatalos ServiceNow megoldásnak. 

1. Látogasson el a [ServiceNow áruházba](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1) , és telepítse a **ServiceNow és a Microsoft OMS integrációjának felhasználói alkalmazását** a ServiceNow-példányban.
   
   >[!NOTE]
   >A Microsoft Operations Management Suite (OMS) és a Azure Monitor közötti folyamatos áttérés részeként a rendszer mostantól a következőt Log Analytics hívja: OMS.     
2. A telepítést követően nyissa meg a ServiceNow-példány bal oldali navigációs sávját, majd keresse meg és válassza ki a **Microsoft OMS-integrátor** elemet.  
3. Válassza a **telepítési ellenőrzőlista** lehetőséget.

   Az állapot  **nem befejezettként** jelenik meg, mert a felhasználói szerepkör még nincs létrehozva.

4. Az **integrációs felhasználó létrehozása** elem melletti szövegmezőbe írja be annak a felhasználónak a nevét, aki CSATLAKOZHAT a ITSMC az Azure-ban.
5. Adja meg a felhasználó jelszavát, majd kattintson **az OK gombra**.  

Az újonnan létrehozott felhasználó megjelenik a hozzárendelt alapértelmezett szerepkörökkel:

- personalize_choices
- import_transformer
- x_mioms_microsoft. user
- ITIL
- template_editor
- view_changer

Miután sikeresen létrehozta a felhasználót, a **telepítési ellenőrzőlista** állapotának állapota **Befejezve** értékre vált, és felsorolja az alkalmazáshoz létrehozott felhasználói szerepkör részleteit.

> [!NOTE]
> A ITSMC az ServiceNow-példányra telepített más modulok nélkül is küldhet incidenseket a ServiceNow. Ha a EventManagement modult használja a ServiceNow-példányban, és az összekötő használatával szeretne eseményeket vagy riasztásokat létrehozni a ServiceNow-ben, adja hozzá a következő szerepköröket az integrációs felhasználóhoz:
> 
> - evt_mgmt_integration
> - evt_mgmt_operator  

## <a name="create-a-connection"></a>Kapcsolat létrehozása
ServiceNow-kapcsolatok létrehozásához kövesse az alábbi eljárást.

> [!NOTE]
> A Azure Monitor által elküldett riasztások a következő elemek egyikét hozhatják létre a ServiceNow-ben: események, incidensek vagy riasztások. 

1. A Azure Portalban lépjen a **minden erőforrás** elemre, és keresse meg a **ügyfélszolgálati (YourWorkspaceName)**.

2. A **munkaterület-adatforrások** területen válassza az **ITSM kapcsolatok** elemet.

   ![Az adatforrás kijelölését bemutató képernyőkép.](media/itsmc-connections/add-new-itsm-connection.png)

3. A jobb oldali ablaktábla tetején válassza a **Hozzáadás** lehetőséget.

4. Adja meg az adatokat az alábbi táblázatban leírtak szerint, majd válassza az **OK** gombot.

   | **Mező** | **Leírás** |
   | --- | --- |
   | **Kapcsolat neve**   | Adja meg annak a ServiceNow-példánynak a nevét, amelyhez csatlakozni kíván a ITSMC. Ezt a nevet később Log Analytics a ITSM-munkaelemek konfigurálásakor és részletes elemzések megtekintésekor is használhatja. |
   | **Partner típusa**   | Válassza a **ServiceNow** lehetőséget. |
   | **Kiszolgáló URL-címe**   | Adja meg annak az ServiceNow-példánynak az URL-címét, amelyhez csatlakozni szeretne a ITSMC. Az URL-címnek egy támogatott SaaS-verzióra kell mutatnia, az utótag *. servicenow.com* (például `https://XXXXX.service-now.com/` ).|
   | **Felhasználónév**   | Adja meg a ServiceNow alkalmazásban létrehozott integrációs felhasználónevet, hogy támogassa a ITSMC való kapcsolódást.|
   | **Jelszó**   | Adja meg a felhasználónévhez tartozó jelszót. **Megjegyzés**: a Felhasználónév és a jelszó csak hitelesítési tokenek létrehozásához használatos. Ezeket a rendszer nem tárolja bárhol a ITSMC szolgáltatáson belül.  |
   | **Ügyfél-azonosító**   | Adja meg a korábban létrehozott OAuth2-hitelesítéshez használni kívánt ügyfél-azonosítót. Az ügyfél-azonosító és a titkos kód létrehozásával kapcsolatos további információkért tekintse meg a [OAuth beállítása](https://old.wiki/index.php/OAuth_Setup)című témakört. |
   | **Ügyfél titka**   | Adja meg az AZONOSÍTÓhoz generált ügyfél-titkos kulcsot.   |
   | **Adatszinkronizálás hatóköre (nap)** | Adja meg az elmúlt napok számát, amelyből az adatok származnak. A korlát 120 nap. |
   | **Szinkronizálandó munkaelemek**   | Válassza ki azokat a ServiceNow munkaelemeket, amelyeket szinkronizálni szeretne az Azure Log Analytics a ITSMC keresztül. A kijelölt értékeket a rendszer a Log Analyticsba importálja. A lehetőségek az incidensek és a módosítási kérelmek.|
   | **Új konfigurációs elemek létrehozása a ITSM-termékben** | Akkor válassza ezt a lehetőséget, ha a ITSM termékben szeretné létrehozni a konfigurációs elemeket. Ha be van jelölve, a ITSMC konfigurációs elemeket hoz létre (ha nincs ilyen) a támogatott ITSM-rendszeren. Alapértelmezés szerint le van tiltva. |

![Képernyőkép a ServiceNow-kapcsolatok hozzáadására szolgáló mezőkről és lehetőségekről.](media/itsmc-connections/itsm-connection-servicenow-connection-latest.png)

Sikeres csatlakozás és szinkronizálás esetén:

- A ServiceNow-példány kiválasztott munkaelemeit a rendszer a Log Analyticsba importálja. A munkaelemek összegzését a **it-szolgáltatásmenedzsmenti csatoló** csempén tekintheti meg.

- Az incidenseket Log Analytics riasztásokból vagy naplókból, illetve az Azure-riasztásokból is létrehozhatja ebben az ServiceNow-példányban.

> [!NOTE]
> A ServiceNow óránkénti kérelmekre érvényesek. A korlát konfigurálásához adja meg a **bejövő REST API ráta korlátozását** a ServiceNow-példányon.

## <a name="next-steps"></a>Következő lépések

* [ITSM-csatoló áttekintése](itsmc-overview.md)
* [ITSM-munkaelemek létrehozása az Azure-riasztásokból](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)
* [Hibaelhárítási problémák a ITSM-csatoló](./itsmc-resync-servicenow.md)
