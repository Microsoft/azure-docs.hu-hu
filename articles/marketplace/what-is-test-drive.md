---
title: Mit jelent a tesztverzió? Microsoft kereskedelmi piactér
description: A Marketplace teszt meghajtó funkció magyarázata
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: trkeya
ms.author: trkeya
ms.date: 06/19/2020
ms.openlocfilehash: e44d5d94a8dc172962a26f3e0dae9ccbb7f8a865
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818889"
---
# <a name="what-is-a-test-drive"></a>Mit jelent a tesztverzió?

A tesztverzió remek módja annak, hogy bemutassa ajánlatát a potenciális ügyfeleknek azáltal, hogy lehetőséget ad nekik, hogy a vásárlás előtt kipróbálják a vásárlást, magas minősítésű érdeklődőket generálva, és nagyobb konverziót eredményezve. Egy teszt meghajtó valós megvalósítási forgatókönyvben kelti életre a terméket. Azok az ügyfelek, akik kipróbálják a terméket, egyértelmű szándékot mutatnak egy hasonló megoldás vásárlására. Ezt a speciálisabb érdeklődőket követő lépések segítségével saját használhatja ki az Ön előnyét.

Az ügyfelek számára is előnyös egy tesztteszt. Ha engedélyezi számukra, hogy először kipróbálják a terméket, azzal csökkenti a vásárlási folyamat súrlódását. Emellett a teszt meghajtó előre ki van építve, vagyis az ügyfeleknek nem kell letölteniük, beállítaniuk vagy konfigurálni a terméket.

## <a name="how-does-it-work"></a>Hogyan működik?

A tesztmeghajtók felügyelt példányok, amelyek igény szerint indítják el a megoldást vagy az alkalmazást az azt kérelmező ügyfelek számára. Miután hozzárendelt egy teszt meghajtópéldányt, az adott ügyfél egy meghatározott ideig használhatja. Az időszak vége után a rendszer törli, hogy helyet hozzon létre egy másik ügyfélnek.

Közzétevőként kezelheti és konfigurálhatja a teszt meghajtó beállításait a Partnerközpont. A technikai konfiguráció részletei az ajánlat típusától függően változnak. Részletes útmutatásért tekintse meg a [Teszt meghajtó műszaki konfigurációját.](./test-drive-technical-configuration.md)

A lehetséges ügyfelek CTA-ként fedezhetik fel a teszt meghajtóját az [AppSource-on.](https://appsource.microsoft.com/en-US/) Meg kell adniuk a kapcsolattartási adataikat, és meg kell adniuk az ajánlat feltételeit és adatvédelmi szabályzatát, majd hozzáférést kapnak az előre konfigurált környezethez, hogy egy meghatározott ideig kipróbálják. Az ügyfelek a termék legfontosabb funkcióinak és előnyeinek gyakorlati, önkiszolgáló próbaverzióját kapják meg, Ön pedig értékes érdeklődőt kap.

## <a name="types-of-test-drives"></a>A tesztmeghajtók típusai

A kereskedelmi piactéren különböző tesztmeghajtók érhetők el a kiválasztott ajánlatokhoz attól függően, hogy milyen terméktípust, forgatókönyvet és piacteret használt:

- Azure Resource Manager
    - Azure-alkalmazások
    - SaaS
    - Virtual Machines
- Üzemeltetett teszt meghajtó
    - Dynamics 365 for Business Central (jelenleg nem támogatott)
    - Dynamics 365 for Customer Engagement
    - Dynamics 365 for Operations
- Logikai alkalmazás (csak támogatási módban)
- Power BI

A tesztmeghajtók konfigurálásával kapcsolatos részletekért lásd: A meghajtó [technikai konfigurációjának tesztelése.](./test-drive-technical-configuration.md) 

### <a name="azure-resource-manager-test-drive"></a>Azure Resource Manager teszt meghajtó

Ez az üzembe helyezési sablon tartalmazza a megoldást tartalmazó összes Azure-erőforrást. Az ehhez a forgatókönyvhöz illeszkedő termékek csak Azure-erőforrásokat használnak. A Azure Resource Manager teszt meghajtó a következő ajánlattípusokhoz érhető el: 

- Azure-alkalmazások
- SaaS
- Virtual machines (Virtuális gépek)

>[!NOTE]
>Ez az egyetlen teszt meghajtó lehetőség a virtuális gép és az Azure-alkalmazás ajánlatokhoz.

### <a name="hosted-test-drive-recommended"></a>Üzemeltetett teszt meghajtó (ajánlott)

Az üzemeltetett teszt meghajtó megszünteti a telepítés összetettségét azáltal, hogy a Microsoft üzemelteti és karbantartja a teszt meghajtót használó felhasználóáthelyezést és -kiépítést végző szolgáltatást. Ha ajánlata van a Microsoft AppSource, készítse el teszt meghajtóját a Dynamics AX/CRM-példányhoz való csatlakozáshoz. A következő AppSource-ajánlatok típusait használhatja:

- A [Dynamics 365 for Customer Engagement és](dynamics-365-customer-engage-offer-setup.md) a Power Apps olyan Customer Engagement-rendszerekhez használhatók, mint az értékesítés, a szolgáltatás, a projektszolgáltatás és a helyszíni szolgáltatás.
- A [Dynamics 365 for Operations](partner-center-portal/create-new-operations-offer.md) használata a Finance and Operations nagyvállalati erőforrás-tervezési rendszerhez, például a pénzügyhez, az üzemeltetéshez és a gyártáshoz, az ellátási lánchoz.

### <a name="logic-app-test-drive"></a>Logikai alkalmazás teszt meghajtója

Az ilyen típusú teszt meghajtót nem a Microsoft üzemelteti, és Azure Resource Manager (ARM) sablonokat használ a Dynamics AX/CRM-ajánlattípusokhoz. Az ARM-sablon futtatásával létre kell hoznia a szükséges erőforrásokat az Azure-előfizetésben. A Logic App Test Drive jelenleg csak támogatási módban érhető el, és a Microsoft nem támogatja. A Logikai alkalmazás teszt meghajtójának konfigurálásával kapcsolatos részletekért lásd: A meghajtó műszaki [konfigurációjának tesztelése.](./test-drive-technical-configuration.md)

### <a name="power-bi-test-drive"></a>Power BI meghajtó tesztelése

Ez egyszerűen egy egyéni irányítópultra mutató beágyazott hivatkozás. Minden olyan terméknek, amely csak interaktív Power BI mutat be, ezt a teszt meghajtótípust kell használnia.

## <a name="transforming-examples"></a>Példák átalakítása

Az erőforrások architektúrája teszt meghajtóként való fordítása ijesztő folyamat lehet. Tekintse meg ezeket a példákat a jelenlegi architektúrák legjobb átalakításáról.

[Webhelysablon átalakítása teszt meghajtóvá](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Website-Deployment-Template-for-Test-Drive)

[Virtuálisgép-sablon átalakítása teszt meghajtóvá](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Virtual-Machine-Deployment-Template-for-Test-Drive)

[Meglévő Resource Manager átalakítása teszt meghajtóvá](https://github.com/Azure/AzureTestDrive/wiki/Deploying-Existing-Solutions)

## <a name="generate-leads-from-your-test-drive"></a>Érdeklődők létrehozása a teszt meghajtóról

A kereskedelmi piactér teszteszköze remek eszköz a marketingesek számára. Javasoljuk, hogy a bevezetési tevékenységbe foglalja bele, amikor további érdeklődőket hoz létre a vállalkozása számára. Részletes útmutatásért tekintse meg a kereskedelmi [piactéri ajánlatban érdeklődőket.](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads.md)

Ha egy tesztvezetővel kötött ajánlatot, regisztrálja a [Microsoft Partner Sales Connectben.](https://support.microsoft.com/help/3155788/getting-started-with-microsoft-partner-sales-connect) Azt is szeretnénk, ha az ügyfél nyerne, ha egy teszt meghajtó szerepet játszott volna.

## <a name="other-resources"></a>Egyéb erőforrások

További teszt meghajtó-erőforrások:

- [Teszt meghajtó – ajánlott eljárások](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Áttekintés](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (PDF; győződjön meg arról, hogy az előugró ablak blokkoló ki van kapcsolva)

## <a name="next-step"></a>Következő lépés

- [Tesztverzió műszaki konfigurálása](test-drive-technical-configuration.md)