---
title: Elemzés hibaelhárítása az Azure Application insights segítségével |} A Microsoft Docs
description: 'Problémák az Application Insights analytics? Itt érdemes kezdenie. '
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 9bbd5859-3584-4d80-9b6d-d5910fa48baa
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: mbullwin
ms.openlocfilehash: ecf0638aa999208331603ac30ccf4eb17b3c4500
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60692292"
---
# <a name="troubleshoot-analytics-in-application-insights"></a>Elemzés hibaelhárítása az Application Insights szolgáltatásban
Problémák [Application Insights-elemzési](analytics.md)? Itt érdemes kezdenie. Analytics az Azure Application Insights hatékony keresési eszközben.

## <a name="limits"></a>Limits
* Jelenleg a lekérdezési eredmények korlátozva, csak egy adott héten múltbeli adatok.
* Teszteljük a böngészők: Chrome, a Microsoft Edge és az Internet Explorer legújabb kiadása.

## <a name="known-incompatible-browser-extensions"></a>Nem kompatibilis böngésző ismert kiterjesztések
* Ghostery

A bővítmény letiltása, vagy használjon egy másik böngészőben.

## <a name="e-a"></a> "Nem várt hiba"
![Váratlan hiba képernyő](media/analytics-troubleshooting/010.png)

Belső hiba történt a portál modul nem kezelt kivétel.

* Tisztítsa meg a böngésző gyorsítótárát.

## <a name="e-b"></a>403-as... Próbálkozzon ide az újrabetöltéshez
![403-as... Próbálkozzon ide az újrabetöltéshez](media/analytics-troubleshooting/020.png)

Hitelesítési hiba történt (a hitelesítési eljárás, illetve a hozzáférési jogkivonat létrehozása közben). Előfordulhat, hogy a portálon nem lehet helyreállítani a böngésző beállításainak módosítása nélkül.

* Győződjön meg arról [harmadik féltől származó cookie-k engedélyezve vannak](#cookies) a böngészőben. 

## <a name="authentication"></a>403-as... Ellenőrizze a biztonsági zóna
![403-as.. .verify biztonsági zóna](media/analytics-troubleshooting/030.png)

Hitelesítési hiba történt (a hitelesítési eljárás, illetve a hozzáférési jogkivonat létrehozása közben). Előfordulhat, hogy a portálon nem lehet helyreállítani a böngésző beállításainak módosítása nélkül.

1. Győződjön meg arról [harmadik féltől származó cookie-k engedélyezve vannak](#cookies) a böngészőben. 
2. Kedvenc, könyvjelző vagy mentett hivatkozás használatával próbált hozzáférni az Analytics portálhoz? Más hitelesítő adatokkal jelentkezett be, mint amikor mentette a hivatkozást?
3. Próbáljon privát/inkognitó módban lévő böngészőablakot használni (a többi hasonló ablak bezárása után). Meg kell adnia hitelesítő adatait. 
4. Nyisson meg egy másik (hagyományos) böngészőablakot, és nyissa meg [Azure](https://portal.azure.com). Jelentkezzen ki. Ezután nyissa meg a hivatkozást, és jelentkezzen be a helyes hitelesítő adatokkal.
5. A Microsoft Edge és az Internet Explorer felhasználók is is ez a hibaüzenet, ha a megbízható zóna beállításait nem támogatottak.
   
    Győződjön meg arról is [Analytics-portál](https://portal.azure.com) és [Azure Active Directory portálon](https://portal.azure.com) azonos biztonsági zónában található:
   
   * Az Internet Explorerben nyissa meg a **Internetbeállítások**, **biztonsági**, **megbízható helyek**, **helyek**:
     
     ![Ha egy helyet a megbízható helyek Internetbeállítások párbeszédablak](media/analytics-troubleshooting/033.png)
     
     Ha az alábbi URL-címek bármelyike szerepel a Webhelyek listában, győződjön meg a többi jelenlétéről is:
     
     https://analytics.applicationinsights.io<br/>
     https://login.microsoftonline.com<br/>
     https://login.windows.net

## <a name="e-d"></a>404 ... Nem található az erőforrás
![404... nem található az erőforrás](media/analytics-troubleshooting/040.png)

Alkalmazás-erőforrás törölve lett az Application Insights, és nem használható többé. Ez akkor fordulhat elő, ha az elemzési lap mentett URL-CÍMÉT.

## <a name="e-e"></a>403 ... Nincs engedély
![403-as... nem jogosult](media/analytics-troubleshooting/050.png)

Nincs engedélye az alkalmazás megnyitásához az Analyticsben.

* Jutott el a hivatkozást személy? Kérje meg őket, hogy Ön a [olvasók vagy az erőforráscsoporthoz közreműködő](../../azure-monitor/app/resources-roles-access-control.md).
* Menti a különböző hitelesítő adatok használatával hivatkozást? Nyissa meg a [az Azure portal](https://portal.azure.com), jelentkezzen ki, és ismételje meg ezt a hivatkozást, a megfelelő hitelesítő adatokkal.

## <a name="html-storage"></a>403 ... HTML5 Storage
A portál a HTML5-alapú localStorage és sessionStorage használja.

* Chrome-ban: Beállítások, adatvédelmi, tartalom beállításait.
* Internet Explorer: Internetbeállítások, Speciális lapján, biztonság, a DOM-tároló engedélyezése

![403-as... Próbálja meg engedélyezni a HTML5-alapú tárolás](media/analytics-troubleshooting/060.png)

## <a name="e-g"></a>404 ... Az előfizetés nem található
![404 ... Az előfizetés nem található](media/analytics-troubleshooting/070.png)

Az URL-cím érvénytelen. 

* Nyissa meg az alkalmazás-erőforrást [Application Insights portálon](https://portal.azure.com). Ezután használja az analitika gombra.

## <a name="e-h"></a>404-es... a lap nem található
![404 ... Oldal nem található](media/analytics-troubleshooting/080.png)

Az URL-cím érvénytelen.

* Nyissa meg az alkalmazás-erőforrást [Application Insights portálon](https://portal.azure.com). Ezután használja az analitika gombra.

## <a name="cookies"></a>Cookie-k engedélyezése
  Lásd: [harmadik féltől származó cookie-k letiltása](https://www.digitalcitizen.life/how-disable-third-party-cookies-all-major-browsers), de ellenőriznünk kell a hirdetmény **engedélyezése** őket.


[!INCLUDE [app-insights-analytics-footer](../../../includes/app-insights-analytics-footer.md)]

