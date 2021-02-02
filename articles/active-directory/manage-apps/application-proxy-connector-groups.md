---
title: Alkalmazások közzététele különálló hálózatokon összekötő csoportok használatával – Azure AD
description: Bemutatja, hogyan hozhatók létre és kezelhetők összekötők csoportjai az Azure AD Application Proxyban.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: kenwith
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ca42137192fefbc669fca775b0deff2f5226c5c
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99253832"
---
# <a name="publish-applications-on-separate-networks-and-locations-using-connector-groups"></a>Alkalmazások közzététele különálló hálózatokon és helyszíneken összekötő csoportok használatával

Az ügyfelek egyre több forgatókönyvhöz és alkalmazáshoz használják az Azure AD Application proxyját. Így a további topológiák engedélyezésével még rugalmasabbak lettek az alkalmazás-proxyk. Alkalmazásproxy-összekötő csoportokat úgy hozhat létre, hogy konkrét összekötőket rendeljen hozzá adott alkalmazások kiszolgálásához. Ezzel a képességgel hatékonyabban vezérelheti az alkalmazásproxy üzembe helyezését.

Minden alkalmazásproxy-összekötő hozzá van rendelve egy összekötő-csoporthoz. Az ugyanahhoz az összekötő csoporthoz tartozó összes összekötő külön egységként működik a magas rendelkezésre állás és a terheléselosztás érdekében. Minden összekötő egy összekötő csoporthoz tartozik. Ha nem hoz létre csoportokat, az összes összekötő alapértelmezett csoportba kerül. A rendszergazda új csoportokat hozhat létre és összekötőket rendelhet hozzájuk a Azure Portal.

Minden alkalmazás hozzá van rendelve egy összekötő csoporthoz. Ha nem hoz létre csoportokat, az összes alkalmazás hozzá van rendelve egy alapértelmezett csoporthoz. Ha azonban csoportokba rendezi az összekötőket, beállíthatja, hogy az egyes alkalmazások egy adott összekötő-csoporthoz működjenek. Ebben az esetben csak az adott csoport összekötői szolgálnak az alkalmazás kérésére. Ez a funkció akkor hasznos, ha az alkalmazások különböző helyszíneken futnak. A hely alapján létrehozhat összekötő csoportokat, így az alkalmazások mindig a fizikailag közel lévő összekötők számára lesznek kiszolgálva.

> [!TIP]
> Ha nagyméretű alkalmazásproxy-telepítéssel rendelkezik, ne rendeljen hozzá alkalmazásokat az alapértelmezett összekötő-csoporthoz. Így az új összekötők nem kapnak élő forgalmat, amíg hozzá nem rendeli őket egy aktív összekötő-csoporthoz. Ez a konfiguráció lehetővé teszi az összekötők üresjárati módba helyezését azáltal, hogy visszahelyezi őket az alapértelmezett csoportba, így a felhasználókra gyakorolt hatás nélkül végezheti el a karbantartást.

## <a name="prerequisites"></a>Előfeltételek

Az összekötők csoportosításához meg kell győződnie arról, hogy [több összekötőt telepített](application-proxy-add-on-premises-application.md). Új összekötő telepítésekor automatikusan csatlakozik az **alapértelmezett** összekötő-csoporthoz.

## <a name="create-connector-groups"></a>Összekötő-csoportok létrehozása

Az alábbi lépések segítségével tetszőleges számú összekötő-csoportot hozhat létre.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza **Azure Active Directory**  >  **vállalati alkalmazások**  >  **alkalmazásproxy lehetőséget**.
1. Válassza az **új összekötő csoport** lehetőséget. Megjelenik az új összekötő-csoport panel.

   ![Megjeleníti az új összekötő csoport kiválasztására szolgáló képernyőt](./media/application-proxy-connector-groups/new-group.png)

1. Adjon nevet az új összekötő csoportnak, majd a legördülő menüben válassza ki, hogy mely összekötők tartoznak ebbe a csoportba.
1. Kattintson a **Mentés** gombra.

## <a name="assign-applications-to-your-connector-groups"></a>Alkalmazások társítása az összekötő-csoportokhoz

Ezeket a lépéseket minden alkalmazásproxy használatával közzétett alkalmazáshoz használhatja. Amikor először tesz közzé egy alkalmazást egy összekötő-csoporthoz, vagy az alábbi lépésekkel módosíthatja a hozzárendelést, amikor csak szeretné.

1. A címtár felügyeleti irányítópultján válassza a **vállalati alkalmazások**  >  **minden alkalmazás** lehetőséget, > az alkalmazáshoz hozzárendelni kívánt alkalmazást > **alkalmazásproxy**.
1. Az **összekötő csoport** legördülő menüjéből válassza ki azt a csoportot, amelyet használni szeretne az alkalmazáshoz.
1. A módosítás alkalmazásához válassza a **Mentés** lehetőséget.

## <a name="use-cases-for-connector-groups"></a>Összekötő-csoportok használati esetei

Az összekötő-csoportok különböző forgatókönyvekhez hasznosak, többek között:

### <a name="sites-with-multiple-interconnected-datacenters"></a>Több összekapcsolt adatközponttal rendelkező helyek

Számos szervezet több összekapcsolt adatközpontot tartalmaz. Ebben az esetben az adatközpontban a lehető legtöbb forgalmat szeretné megőrizni, mert az adatközpontok közötti kapcsolatok költségesek és lassúak. Az egyes adatközpontokban lévő összekötőket csak az adatközponton belüli alkalmazások kiszolgálására lehet telepíteni. Ez a megközelítés lekicsinyíti az adatközpontok közötti kapcsolatokat, és teljes mértékben átlátható élményt nyújt a felhasználók számára.

### <a name="applications-installed-on-isolated-networks"></a>Elszigetelt hálózatokra telepített alkalmazások

Az alkalmazások olyan hálózatokban is tárolhatók, amelyek nem részei a fő vállalati hálózatnak. Az összekötő csoportok használatával dedikált összekötőket telepíthet az elkülönített hálózatokon, hogy az alkalmazások a hálózatra is elkülönítve legyenek. Ez általában akkor fordul elő, ha egy külső gyártó egy adott alkalmazást tart fenn a szervezet számára.

Az összekötő csoportok lehetővé teszik dedikált összekötők telepítését azokhoz a hálózatokhoz, amelyek csak bizonyos alkalmazásokat tesznek közzé, így egyszerűbbé és biztonságosabbá teszik a külső gyártóktól származó alkalmazások felügyeletét.

### <a name="applications-installed-on-iaas"></a>A IaaS telepített alkalmazások

A IaaS-re telepített alkalmazások esetében az összekötő csoportok közös szolgáltatást biztosítanak az összes alkalmazáshoz való hozzáférés biztonságossá tételéhez. Az összekötő-csoportok nem hoznak létre további függőséget a vállalati hálózatban, vagy feldarabolják az alkalmazás élményét. Az összekötők minden Felhőbeli adatközpontban telepíthetők, és csak az adott hálózaton található alkalmazásokat szolgálják fel. A magas rendelkezésre állás érdekében több összekötőt is telepíthet.

Példaként vegyen fel egy olyan szervezetet, amely több virtuális géppel csatlakozik a saját IaaS üzemeltetett virtuális hálózathoz. Annak engedélyezéséhez, hogy az alkalmazottak használhassák ezeket az alkalmazásokat, ezek a magánhálózatok helyek közötti VPN-kapcsolattal csatlakoznak a vállalati hálózathoz. Ez jó élményt nyújt a helyszínen található alkalmazottak számára. Előfordulhat azonban, hogy nem ideális a távoli alkalmazottak számára, mivel további helyszíni infrastruktúrát igényel a hozzáférés irányításához, ahogy az alábbi ábrán látható:

![Az Azure AD IaaS-hálózatot bemutató diagram](./media/application-proxy-connector-groups/application-proxy-iaas-network.png)
  
Az Azure AD Application Proxy-összekötő csoportjaival engedélyezheti a közös szolgáltatást, hogy minden alkalmazáshoz hozzáférjen, anélkül, hogy további függőséget hozna létre a vállalati hálózatban:

![Több felhőalapú gyártó Azure AD-IaaS](./media/application-proxy-connector-groups/application-proxy-multiple-cloud-vendors.png)

### <a name="multi-forest--different-connector-groups-for-each-forest"></a>Multi-Forest – különböző összekötő-csoportok minden erdőhöz

A legtöbb telepített alkalmazásproxy az egyszeri bejelentkezés (SSO) képességeit használja a Kerberos által korlátozott delegálás (KCD) végrehajtásával. Ennek eléréséhez az összekötő számítógépeit olyan tartományhoz kell csatlakoztatni, amely delegálhatja a felhasználókat az alkalmazás felé. A KCD támogatja az erdők közötti képességeket. A különböző többerdős környezetekkel rendelkező vállalatok esetében azonban egyetlen összekötő nem használható az összes erdőhöz. 

Ebben az esetben az adott összekötőket erdőn belül lehet üzembe helyezni, és úgy kell beállítani, hogy az csak az adott erdő felhasználóinak kiszolgálására közzétett alkalmazásokat szolgálja ki. Minden összekötő csoport egy másik erdőt jelöl. Míg a bérlő és a legtöbb felhasználói felület az összes erdő esetében egységes, a felhasználók az Azure AD-csoportok használatával hozzárendelhetők az erdő alkalmazásaihoz.

### <a name="disaster-recovery-sites"></a>Vész-helyreállítási helyek

A webhelyek megvalósításának módjától függően két különböző megközelítéssel elvégezhető a vész-helyreállítási (DR) hely:

* Ha a DR-hely aktív-aktív módban van, ahol pontosan ugyanúgy, mint a fő hely, és ugyanazokkal a hálózatkezelési és AD-beállításokkal rendelkezik, akkor a DR-helyen is létrehozhatja az összekötőket a fő hellyel azonos összekötő csoportban. Ez lehetővé teszi az Azure AD számára a feladatátvételek észlelését.
* Ha a DR-hely elkülönül a fő helytől, létrehozhat egy másik összekötő csoportot a DR-helyen, és 1) a biztonsági mentési alkalmazásokkal vagy 2.) manuálisan átirányíthatja a meglévő alkalmazást a DR-összekötő csoportba, igény szerint.

### <a name="serve-multiple-companies-from-a-single-tenant"></a>Több vállalat kiszolgálása egyetlen bérlőből

Számos különböző módon lehet megvalósítani egy olyan modellt, amelyben egyetlen szolgáltató üzembe helyezi és karbantartja az Azure AD-hez kapcsolódó szolgáltatásokat több vállalat számára. Az összekötő csoportok segítenek a rendszergazdának az összekötők és az alkalmazások különböző csoportokba való elkülönítésében. Az egyik módszer, amely alkalmas a kisvállalatok számára, egyetlen Azure AD-Bérlővel rendelkezik, míg a különböző vállalatok saját tartománynevet és hálózatokat biztosítanak. Ez az M&is igaz, és olyan helyzetekben, amikor egyetlen informatikai részleg számos vállalatot biztosít szabályozási vagy üzleti okokból.

## <a name="sample-configurations"></a>Minta konfiguráció

Néhány példa, amelyet megvalósíthat, belefoglalhatja a következő összekötő-csoportokat.

### <a name="default-configuration--no-use-for-connector-groups"></a>Alapértelmezett konfiguráció – nincs használatban összekötő csoportok számára

Ha nem használ összekötő csoportokat, a konfiguráció a következőképpen fog kinézni:

![Példa az Azure AD-re – nincs összekötő-csoport](./media/application-proxy-connector-groups/application-proxy-sample-config-1.png)

Ez a konfiguráció elegendő a kis-és nagyvállalati környezetek és tesztek számára. Emellett akkor is jól működik, ha a szervezete rendelkezik egy egyszerű hálózati topológiával.

### <a name="default-configuration-and-an-isolated-network"></a>Alapértelmezett konfiguráció és elszigetelt hálózat

Ez a konfiguráció az alapértelmezett érték fejlődése, amelyben egy adott alkalmazás egy elkülönített hálózaton, például a IaaS virtuális hálózaton fut:

![Példa az Azure AD-ben nincs összekötő-csoport és egy elkülönített hálózat](./media/application-proxy-connector-groups/application-proxy-sample-config-2.png)

### <a name="recommended-configuration--several-specific-groups-and-a-default-group-for-idle"></a>Ajánlott konfiguráció – több meghatározott csoport és alapértelmezett csoport tétlen

A nagyméretű és összetett szervezetek ajánlott konfigurációja az, hogy az alapértelmezett összekötő csoportot olyan csoportként használja, amely nem tartalmaz alkalmazásokat, és az üresjárati vagy újonnan telepített összekötők esetében használatos. Minden alkalmazás testreszabott összekötő-csoportokkal szolgál. Ez lehetővé teszi a fent ismertetett forgatókönyvek összetettségét.

Az alábbi példában a vállalat két, az A és B adatközpontot tartalmaz, amelyek mindegyike két összekötővel rendelkezik, amelyek mindegyike az egyes helyek kiszolgálására szolgál. Minden helyhez különböző alkalmazások futnak.

![Példa 2 adatközpontot és 2 összekötőt tartalmazó vállalatra](./media/application-proxy-connector-groups/application-proxy-sample-config-3.png)

## <a name="next-steps"></a>Következő lépések

* [Az Azure AD Application Proxy-összekötők ismertetése](application-proxy-connectors.md)
* [Egyszeri bejelentkezés engedélyezése](what-is-single-sign-on.md)
