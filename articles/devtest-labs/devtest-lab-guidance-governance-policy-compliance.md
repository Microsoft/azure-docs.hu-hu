---
title: Vállalati házirend és megfelelőség a Azure DevTest Labs
description: Ez a cikk útmutatást nyújt a vállalati szabályzatok és a Azure DevTest Labs infrastruktúra megfelelőségének szabályozásához.
ms.topic: article
ms.date: 06/26/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 89fe380c3a8e0e0a82cc806bf19f9a3454041d94
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97592347"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---company-policy-and-compliance"></a>Azure DevTest Labs infrastruktúra irányítása – a vállalati házirend és a megfelelőség
Ez a cikk útmutatást nyújt a vállalati szabályzatok és a Azure DevTest Labs infrastruktúra megfelelőségének szabályozásához. 

## <a name="public-vs-private-artifact-repository"></a>Nyilvános és privát összetevők tárháza

### <a name="question"></a>Kérdés
Mikor érdemes egy szervezet nyilvános összetevő-tárházat vagy privát összetevőt használni a DevTest Labs szolgáltatásban?

### <a name="answer"></a>Válasz
A [nyilvános összetevők tárháza](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) a leggyakrabban használt szoftvercsomagok kezdeti készletét biztosítja. Segít a gyors üzembe helyezésben anélkül, hogy időt kellene fordítania a közös fejlesztői eszközök és bővítmények ismételt létrehozására. Dönthet úgy is, hogy saját privát tárházat helyez üzembe. A nyilvános és a privát tárházat párhuzamosan is használhatja. Dönthet úgy is, hogy letiltja a nyilvános tárházat. A privát tárház üzembe helyezésének feltételeit a következő kérdések és szempontok alapján kell ellátni:

- A szervezetnek van-e követelménye a vállalati licenccel rendelkező szoftvereknek a DevTest Labs-ajánlat részeként? Ha a válasz igen, akkor létre kell hozni egy privát tárházat.
- A szervezet olyan egyéni szoftvert fejleszt, amely egy adott műveletet biztosít, amely a teljes kiépítési folyamat részeként szükséges? Ha a válasz igen, akkor a rendszer egy privát tárházat helyez üzembe.
- Ha a szervezet irányítási szabályzata elszigetelést igényel, és a külső adattárak nem a szervezet közvetlen konfigurációjának felügyelete alatt állnak, akkor központilag kell telepíteni a privát összetevő tárházát. A folyamat részeként a nyilvános tárház kezdeti másolata átmásolható és integrálható a privát tárházba. Ezután a nyilvános tárházat le lehet tiltani, hogy a szervezeten belül senki ne férhessen hozzá. Ez a megközelítés arra kényszeríti a szervezeten belüli felhasználókat, hogy csak egyetlen, a szervezet által jóváhagyott adattárral rendelkezzenek, és a konfigurációt csökkentsék.

### <a name="single-repository-or-multiple-repositories"></a>Egy adattár vagy több tárház 

### <a name="question"></a>Kérdés
Érdemes egy szervezetet megtervezni egy adott tárházhoz, vagy több tárházat is engedélyezni?

### <a name="answer"></a>Válasz
A szervezet általános irányítási és konfigurációs felügyeleti stratégiájának részeként javasolt központosított tárházat használni. Több tárház használata esetén előfordulhat, hogy az idő múlásával a nem felügyelt szoftverek silói lesznek. A központi tárházban több csapat is használhat a tárházból származó összetevőket a projektekhez. Kikényszeríti a szabványosítást, a biztonságot, a könnyű kezelhetőséget, és kiküszöböli az erőfeszítések ismétlődését. A központosított környezet részeként a következő műveletek ajánlottak a hosszú távú felügyelethez és a fenntarthatósághoz:

- Társítsa az Azure Repos-t ugyanazzal a Azure Active Directory Bérlővel, amelyet az Azure-előfizetés használ a hitelesítéshez és engedélyezéshez.
- Hozzon létre egy, a központilag felügyelt Azure Active Directory **összes DevTest Labs-fejlesztő** nevű csoportot. Az összetevő-fejlesztéshez hozzájáruló fejlesztőknek ebbe a csoportba kell tartoznia.
- Ugyanaz a Azure Active Directory csoport használható az Azure Repos adattárhoz és a laborhoz való hozzáférés biztosításához.
- Az Azure-alapú adattárakban az elágazások és az elágazások különálló, az elsődleges üzemi tárházból származó fejlesztésen alapuló tárházban használhatók. A tartalmat a rendszer csak a fő ág számára adja hozzá egy lekéréses kérelemmel a megfelelő kód felülvizsgálatát követően. Miután a kód-véleményező jóváhagyja a változást, egy vezető fejlesztő, aki felelős a fő ág fenntartásáért, egyesíti a frissített kódot. 

## <a name="corporate-security-policies"></a>Vállalati biztonsági szabályzatok

### <a name="question"></a>Kérdés
Hogyan gondoskodhat a szervezet a vállalati biztonsági házirendekről?

### <a name="answer"></a>Válasz
A szervezetek a következő műveletek végrehajtásával érhetik el:

1. Átfogó biztonsági szabályzat fejlesztése és közzététele. A szabályzat a szoftverekkel, a Felhőbeli objektumokkal kapcsolatos elfogadható használati szabályokat fogalmazza meg. Azt is meghatározza, hogy milyen egyértelműen sértse meg a szabályzatot. 
2. Egyéni rendszerkép, egyéni összetevők és központi telepítési folyamat fejlesztése, amely az Active Directoryval definiált biztonsági tartományon belüli előkészítést teszi lehetővé. Ez a megközelítés kikényszeríti a vállalati határt, és beállítja a környezeti vezérlőelemek közös készletét. Ezeket a környezeteket a fejlesztők úgy tekinthetik meg, mint a fejlesztésük és a biztonságos fejlesztési életciklusuk során, a teljes folyamat részeként. A cél az is, hogy olyan környezetet biztosítson, amely nem túlságosan korlátozó, ami hátráltathatja a fejlesztést, de a vezérlők ésszerű készletét is. A labor virtuális gépeket tartalmazó szervezeti egység (OU) csoportházirendje az éles környezetben található összes csoportházirend részhalmaza lehet. Azt is megteheti, hogy egy további készletet is tartalmaz, hogy megfelelően enyhítse az azonosított kockázatokat.

## <a name="data-integrity"></a>Adatintegritás

### <a name="question"></a>Kérdés
Hogyan biztosítja a szervezet az adatok integritását annak biztosítására, hogy a távelérési szolgáltatás nem tudja eltávolítani a programkódot vagy kártevő vagy nem jóváhagyott szoftvereket bevezetni?

### <a name="answer"></a>Válasz
A DevTest Labs szolgáltatásban való együttműködéshez számos különböző vezérlési réteg áll rendelkezésre a külső tanácsadók, alvállalkozók vagy olyan alkalmazottak fenyegetésének enyhítésére. 

Ahogy azt korábban említettük, az első lépésnek rendelkeznie kell egy, a szabályzatnak megfelelő, meghatározott használati szabályzattal, amely világosan ismerteti a következményeket, amikor valaki sérti a házirendet. 

A távelérési vezérlők első rétege egy távelérési házirend alkalmazása egy olyan VPN-kapcsolaton keresztül, amely nem kapcsolódik közvetlenül a laborhoz. 

A vezérlőelemek második rétege olyan csoportházirend-objektumok készletét alkalmazza, amelyek megakadályozzák a másolást és beillesztést a távoli asztalon. Egy hálózati házirendet úgy lehetne megvalósítani, hogy ne engedélyezze a kimenő szolgáltatásokat a környezetből, például az FTP-és az RDP-szolgáltatásokat. A felhasználó által definiált útválasztás az összes Azure-beli hálózati forgalmat visszakényszerítheti a helyszíni környezetbe, de az Útválasztás nem tudott minden olyan URL-cím esetében, amely lehetővé teszi az adatok feltöltését, kivéve, ha a tartalom és a munkamenetek ellenőrzéséhez proxyn keresztül vezérlik A nyilvános IP-címeket a DevTest Labs szolgáltatást támogató virtuális hálózaton korlátozni lehet, hogy ne engedélyezzék egy külső hálózati erőforrás áthidalóját.

Végső soron ugyanazokat a korlátozásokat kell alkalmazni a szervezeten belül, amelyeknek figyelembe kell venniük a cserélhető adathordozók és a külső URL-címek összes lehetséges módszerét is, amelyek elfogadják a tartalom közzétételét. A biztonsági szabályzat áttekintéséhez és megvalósításához forduljon a biztonsági szakemberekhez. További javaslatok: [Microsoft Cyber Security](https://www.microsoft.com/security/default.aspx?&WT.srch=1&wt.mc_id=AID623240_SEM_sNYnsZDs).


## <a name="next-steps"></a>Következő lépések
Lásd: [alkalmazások áttelepítése és integrációja](devtest-lab-guidance-governance-application-migration-integration.md).
