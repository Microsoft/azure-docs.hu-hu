---
title: Gyakori kérdések ClearDB MySql-adatbázisok Azure App Service
description: Válaszok a ClearDB MySQL-adatbázisok Azure App Service-vel való használatával kapcsolatos gyakori kérdésekre.
documentationcenter: php
services: mysql
author: sunbuild
manager: yochayk
tags: mysql
ms.service: multiple
ms.topic: article
ms.date: 10/27/2016
ms.author: sumuth
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: references_regions
ms.openlocfilehash: d9d4ae746c192e8b4afd1781c1e072fc0dc70422
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98599796"
---
# <a name="faq-for-cleardb-mysql-databases-with-azure-app-service"></a>Gyakori kérdések ClearDB MySql-adatbázisok Azure App Service
Ez a gyakori kérdések az Azure Web Apps ClearDB MySQL-adatbázisainak használatával és megvásárlásával kapcsolatos gyakori kérdésekre adnak választ.

> [!IMPORTANT]
> 2018. június 13-án a ClearDB a Microsoft által jelenleg számlázott Azure-alapú ügyfeleket egy közvetlen számlázási modellel ClearDB. A cikkben szereplő információk elavultak. Az Azure-ban létrehozott ClearDB-adatbázis nem hozható létre vagy nem frissíthető.
>
> További részletek és további lépések: [a ClearDB-szolgáltatási csomagok módosításai](https://w2.cleardb.net/important-change-of-billing-notice-for-all-azure-cleardb-service-plans/).

## <a name="what-options-do-i-have-for-mysql-on-azure"></a>Milyen lehetőségeket használhatok a MySQL-hez az Azure-ban?
A szolgáltatással kapcsolatos legfrissebb információkért tekintse meg a [ClearDB](https://w2.cleardb.net/) . A ClearDB egy MySQL üzemeltetési szolgáltatás, amely a MySQL-infrastruktúrát kezeli. 

Több más lehetőség is van a MySQL Azure-beli üzemeltetéséhez:
* [Azure Database for MySQL](https://azure.microsoft.com/services/mysql/)
* [Azure-beli virtuális gépen futó MySQL-fürt](https://github.com/azure/azure-quickstart-templates/tree/master/mysql-replication)
* [Egy Azure-beli virtuális gépen futó önálló MySQL-példány](/previous-versions/azure/virtual-machines/windows/classic/mysql-2008r2?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)


## <a name="do-i-need-a-credit-card-for-the-web-app--mysql-template-in-the-azure-marketplace"></a>Szükségem van bankkártyára a Web App + MySQL-sablonhoz az Azure piactéren?
Ez a használt előfizetés típusától függ. Íme néhány gyakran használt előfizetés típusa:

* [Fizetés menet](https://azure.microsoft.com/offers/ms-azr-0003p/)közben: hitelkártyát igényel, és fizetős MySQL-adatbázis vásárlása esetén bankkártyáját kell fizetni.
* [Ingyenes próbaverzió](https://azure.microsoft.com/pricing/free-trial/): Microsoft Azure-szolgáltatásokkal való használatra szánt krediteket tartalmaz, de nem engedélyezi harmadik féltől származó erőforrások megvásárlását. Harmadik féltől származó szolgáltatások vagy fizetős MySQL-adatbázis vásárlásához bankkártya-kompatibilis előfizetést kell használnia. Web Apps létrehozhat egy ingyenes ClearDB MySQL-adatbázist.
* Az MSDN- [előfizetés](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/) és az **MSDN dev-teszt** díja: az ingyenes PRÓBAVERZIÓhoz hasonlóan az MSDN-előfizetés használatához bankkártyával kell rendelkeznie a fizetős MySQL-megoldás ClearDB való megvásárlásához.
* [Nagyvállalati szerződés (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/): az nagyvállalati szerződéssel rendelkező ügyfeleket minden negyedévben az Azure Marketplace (harmadik féltől származó) vásárlások után külön, konszolidált számlán számoljuk el. Az Azure-előfizetést (korábban pénzügyi kötelezettségvállalásnak nevezett) kívül a piactéren vásárolt vásárlásokra is kiszámlázjuk. Fontos megjegyezni, hogy jelenleg az Azure áruház nem érhető el az Azerbajdzsánban, Horvátországban, Norvégiában és Puerto Rico-ban regisztrált ügyfelek számára. 

## <a name="why-was-i-charged-350-for-a-web-app--mysql-from-the-azure-marketplace"></a>Miért számolok fel $3,50-ra egy webalkalmazást és egy MySQL-t az Azure piactéren?
Az alapértelmezett adatbázis-beállítás a Titan, amely $3,50. Az adatbázis létrehozása során nem mutatjuk be a költségeket, és előfordulhat, hogy véletlenül megvásárolta a nem kívánt adatbázist. Megpróbáljuk megkeresni a felhasználói élmény javítását, de addig is meg kell adnia a webalkalmazás és az adatbázis kiválasztott díjszabását, mielőtt a **create (létrehozás** ) gombra kattintana, és megkezdi az erőforrások üzembe helyezését.

## <a name="i-am-running-mysql-on-my-own-azure-virtual-machine-can-i-connect-my-azure-web-app-to-my-database"></a>A saját Azure-beli virtuális gépén futtatom a MySQL-t. Összekapcsolhatom az Azure-webalkalmazást az adatbázishoz?
Igen. Ha az Azure-beli virtuális gép távelérést kap a webalkalmazáshoz, a webalkalmazást az adatbázishoz is összekapcsolhatjuk. További információ: a [MySQL telepítése virtuális gépre](/previous-versions/azure/virtual-machines/windows/classic/mysql-2008r2?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="in-which-countriesregions-are-cleardb-premium-mysql-clusters-supported"></a>Mely országokban/régiókban támogatott a prémium szintű MySQL-fürtök ClearDB?
A ClearDB Premium MySQL-fürtök az összes Azure-régióban elérhetők világszerte, India, Ausztrália, Dél-Brazília és Kína kivételével.

## <a name="can-i-create-a-new-cluster-prior-to-creating-a-database-with-cleardb-premium-cluster-solution"></a>Létrehozhatok új fürtöt a ClearDB Premium cluster megoldással rendelkező adatbázis létrehozása előtt?
Nem, üres ClearDB-fürtök létrehozása nem támogatott. A Azure Portal lehetővé teszi adatbázisok létrehozását egy fürtben, amely egy új fürtöt is létrehozhat egyszerre.

## <a name="will-i-be-warned-if-i-try-to-delete-a-cleardb-mysql-database-that-is-in-use-by-one-of-my-applications"></a>Figyelmeztetni fogjuk, ha olyan ClearDB MySQL-adatbázist próbálok törölni, amelyet az egyik alkalmazásom használ?
Nem, az Azure nem figyelmezteti, ha töröl egy piactér-vásárlást, amelytől az alkalmazás függ.

## <a name="which-regions-can-i-create-cleardb-databases-in"></a>Mely régiókban hozhatók létre ClearDB-adatbázisok?
Az Azure Marketplace nem érhető el az Azerbajdzsánban, Horvátországban, Norvégiában vagy Puerto Rico-ban regisztrált ügyfelek számára. A ClearDB nem érhető el ezekben a régiókban.

## <a name="what-pricing-tier-should-i-choose-for-a-production-web-app-and-database"></a>Milyen díjszabási szintet érdemes választani egy éles webalkalmazáshoz és adatbázishoz?
A Web Apps alapszintű vagy magasabb díjszabási szintjét használhatja. A ClearDB esetében a Szaturnusz-vagy a Jupiter-csomagot ajánljuk. Tekintse át a [Web Apps](https://azure.microsoft.com/pricing/details/app-service/) és [ClearDB MySQL-adatbázisok](https://w2.cleardb.net/important-change-of-billing-notice-for-all-azure-cleardb-service-plans/) egyes díjszabási szintjeinek & korlátozásait, és válassza ki az igényeinek leginkább megfelelőt.

## <a name="how-do-i-upgrade-my-cleardb-database-from-one-plan-to-another"></a>Hogyan frissíteni a ClearDB-adatbázist az egyik csomagból a másikba?
A [Azure Portal](https://portal.azure.com)egy megosztott ClearDB-adatbázis vertikális felskálázását teszi elérhetővé. További információért olvassa el ezt a [cikket](https://blogs.msdn.microsoft.com/appserviceteam/2016/10/06/upgrade-your-cleardb-mysql-database-in-azure-portal/) . Jelenleg nem támogatjuk a ClearDB Premium-fürtök frissítését a Azure Portal.

## <a name="i-cant-see-my-cleardb-database-in-azure-portal"></a>Nem látom a ClearDB-adatbázist a Azure Portal?
Ha a klasszikus ClearDB-adatbázist hozott létre, nem fogja tudni megtekinteni az adatbázist a [Azure Portalban](https://portal.azure.com). Ehhez a forgatókönyvhöz nem érhető el munka.

## <a name="who-do-i-contact-for-support-when-my-database-is-down"></a>Kihez kell fordulni a támogatási szolgálatnál, ha az adatbázis leáll?
Forduljon a [ClearDB támogatási szolgálatához](https://www.cleardb.com/developers/help/support) az adatbázissal kapcsolatos problémákkal kapcsolatban. Készüljön fel az Azure-előfizetési adatok megadására.

## <a name="can-i-create-additional-users-for-my-cleardb-mysql-database-cluster-solution"></a>Létrehozhatok további felhasználókat a ClearDB MySQL adatbázis-fürt megoldásához?
Nem. Nem hozhat létre további felhasználókat, de további adatbázisokat is létrehozhat a ClearDB-adatbázis fürtjén.  

## <a name="can-basicpro-series-databases-be-upgraded-in-place-similar-to-planetary-plans-today-on-cleardb-portal"></a>Az alapszintű/Pro sorozatú adatbázisok helyben, hasonló módon frissíthetők a ClearDB-portálon?
Igen, az alapszintű adatsorozat-adatbázisok helyben is frissíthetők (alapszintű 60 alapszintű 500-en keresztül). A Pro Series (Pro 125 – Pro 1000) a Pro 60 kivételével is frissíthető. Jelenleg nem támogatott a Pro 60-adatbázis frissítése. 

## <a name="when-i-migrate-my-resources-from-one-subscription-to-another-does-my-cleardb-mysql-database-get-migrated-as-well"></a>Ha egy előfizetésből egy másikba migrálom az erőforrásokat, a ClearDB MySQL-adatbázis is át lett telepítve?
Ha erőforrás-áttelepítést hajt végre az előfizetések között, bizonyos [korlátozások](azure-resource-manager/management/move-support-resources.md) érvényesek. A ClearDB MySQL-adatbázis egy harmadik féltől származó szolgáltatás, ezért az Azure-előfizetés áttelepítése során nem lesz migrálva. Ha nem felügyeli a MySQL-adatbázis áttelepítését az Azure-erőforrások áttelepítése előtt, a ClearDB MySQL-adatbázisait le lehet tiltani. Először manuálisan telepítse át az adatbázisokat, majd hajtsa végre az Azure-előfizetés áttelepítését a webalkalmazáshoz. 

## <a name="i-hit-the-spending-limit-on-my-subscription-i-removed-the-limit-and-my-app-service-is-online-however-the-database-is-not-accessible-how-do-i-re-enable-the-cleardb-database"></a>Megütöttem az előfizetés költségkeretét. Eltávolítottam a korlátot, és a App Service online állapotú, de az adatbázis nem érhető el. Hogyan újra engedélyezni a ClearDB-adatbázist?
Az adatbázis újbóli engedélyezéséhez forduljon a [ClearDB támogatási szolgálatához](https://www.cleardb.com/developers/help/support) . Adja meg az Azure-előfizetési adatokat és az adatbázis nevét.

## <a name="can-i-transfer-a-cleardb-database-from-a-credit-card-subscription-to-an-ea-subscription"></a>Átvihetek egy ClearDB-adatbázist bankkártyás előfizetésből egy EA-előfizetésre?
A meglévő ClearDB-adatbázisok a meglévő előfizetésekhez társított bankkártyát használják. Ha EA-előfizetést szeretne használni, az adatait át kell telepítenie egy új adatbázisba:

* Vásároljon egy új ClearDB-adatbázist az EA-előfizetésével.
* Migrálja adatait az új adatbázisba.
* Frissítse az alkalmazást az új adatbázis használatára.
* Törölje a régi ClearDB-adatbázist.

Amikor új webalkalmazást hoz létre a MySQL-sel (ClearDB), vagy létrehoz egy MySQL-adatbázist (ClearDB), a kiválasztott előfizetés határozza meg, hogy a szolgáltatásért milyen díjat kell fizetnie. EA-előfizetéssel nem tiltjuk le a harmadik féltől származó szolgáltatások, például a Azure Portal ClearDB beszerzését. Az EA-előfizetések számlázása az Azure-előfizetésen kívül történik, és negyedévente és hátralékban történik. Az EA-ügyfélnek olyan fizetési módot kell beállítania, mint például egy hitelkártyát, amelyet bármely harmadik féltől származó Piactéri szolgáltatásért fizetni kell.

## <a name="where-can-i-see-the-charges-for-cleardb-resources-in-an-ea-subscription"></a>Hol tekinthetem meg a ClearDB-erőforrások díjait az EA-előfizetésekben?
A közvetlen nagyvállalati szerződéssel rendelkező ügyfelek esetében az Azure Marketplace díjai láthatók a Enterprise Portal. Vegye figyelembe, hogy az Azure-előfizetésen kívül minden piactér-vásárlás és-felhasználás számlázása negyedévente és utólag történik. A nagyvállalati szerződéssel rendelkező ügyfeleknek közvetlenül kell fizetniük a harmadik féltől származó szolgáltatók számára, és ezt megtehetik, ha a fizetési módot, például bankkártyát a saját EA-fiókjával szeretné megtenni.

A közvetett nagyvállalati szerződéssel rendelkező ügyfelek megtalálhatják az Azure Marketplace-előfizetéseit a Enterprise Portal- **Előfizetések kezelése** lapján, de a díjszabás rejtve. A Marketplace díjaival kapcsolatos információkért az ügyfeleknek licencmegoldás-szolgáltatójukhoz kell fordulniuk.

Az Azure Marketplace külső szolgáltatásokhoz való hozzáférését az EA Azure regisztrációs rendszergazdái kezelhetik. Letilthatja vagy újból engedélyezheti a külső vásárlásokhoz való hozzáférést a tárolóban a fiókok és előfizetések kezeléséhez a Enterprise Portal fiókok szakaszában.

## <a name="who-do-i-contact-for-questions-about-my-bill-for-cleardb-services-in-my-ea-subscription"></a>Kivel tudok kapcsolatba lépni a ClearDB Services szolgáltatással kapcsolatos felmerülő kérdésekkel kapcsolatban az EA-előfizetésben?
Vegye fel a kapcsolatot a [nagyvállalati ügyfélszolgálattal](https://aka.ms/AzureEntSupport) az EA-regisztráció keretében történő számlázás tekintetében. Az EA portál támogatási csapata válaszol a kérdésére, vagy segít megoldani a problémát.

## <a name="more-information"></a>További információ
[Azure Marketplace – Gyakori kérdések](https://azure.microsoft.com/marketplace/faq/)