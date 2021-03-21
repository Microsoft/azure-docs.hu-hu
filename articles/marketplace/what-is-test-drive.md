---
title: Mit jelent a tesztverzió? Microsoft kereskedelmi piactér
description: A Marketplace Test Drive funkció magyarázata
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: trkeya
ms.author: trkeya
ms.date: 06/19/2020
ms.openlocfilehash: 193e7bd78046a1d73cb55171c15c9cb6a7278297
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96490040"
---
# <a name="what-is-a-test-drive"></a>Mit jelent a tesztverzió?

A test Drive nagyszerű lehetőséget nyújt arra, hogy ajánlatot nyújtson a potenciális ügyfelek számára azáltal, hogy a vásárlás előtt megpróbál kipróbálni, magas minősítésű érdeklődőket generál, és nagyobb átalakítást eredményez. A tesztelési meghajtó valós megvalósítási forgatókönyvben teszi lehetővé a termék életbe lépését. Azok az ügyfelek, akik kipróbálják a terméket, egyértelmű szándékot mutatnak a hasonló megoldás megvásárlására. Használja ki az előnyeit a fejlettebb érdeklődők követésével.

Az ügyfelek kihasználhatják a tesztelési meghajtókat is. Azáltal, hogy először kipróbálják a terméket, csökkenti a vásárlási folyamat súrlódását. Emellett a test Drive előre kiépített, azaz az ügyfeleknek nem kell letölteniük, beállítaniuk vagy konfigurálniuk a terméket.

## <a name="how-does-it-work"></a>Hogyan működik?

A tesztelési meghajtók olyan felügyelt példányok, amelyek igény szerint elindítják a megoldást vagy az alkalmazást a kérelmező ügyfelek számára. A test Drive-példány hozzárendelése után az adott ügyfél számára elérhetővé válik egy meghatározott időtartamra. Az időszak lejárta után a rendszer törli a helyet egy másik ügyfél számára.

Közzétevőként kezelheti és konfigurálhatja a tesztelési meghajtó beállításait a partner Centerben. A technikai konfiguráció részletei az ajánlat típusától függően változnak. Részletes útmutatásért tekintse meg a [Test Drive technikai konfigurációját](./test-drive-technical-configuration.md).

A potenciális ügyfelek a [AppSource](https://appsource.microsoft.com/en-US/)-on keresztül, az Ön ajánlatán keresztül felderítik a teszt-meghajtót. Megadják a kapcsolattartási adataikat, és elfogadják az ajánlat használati feltételeit és adatvédelmi szabályzatát, majd hozzáférhetnek az előre konfigurált környezethez, hogy kipróbálják azt egy rögzített időtartamra. Az ügyfelek a termék főbb funkcióit és előnyeit igénybe vevő, saját irányítású próbaidőszakot kapnak, és értékes érdeklődőket kapnak.

## <a name="types-of-test-drives"></a>A tesztelési meghajtók típusai

A kereskedelmi piactéren különböző tesztelési meghajtók érhetők el, a termékek, a forgatókönyvek és a piactér típusától függően:

- Azure Resource Manager
    - Azure-alkalmazások
    - SaaS
    - Virtual Machines
- Szolgáltatott tesztelési meghajtó
    - Dynamics 365 for Business Central (jelenleg nem támogatott)
    - Dynamics 365 for Customer Engagement
    - Dynamics 365 for Operations
- Logikai alkalmazás (csak támogatási módban)
- Power BI

A tesztelési meghajtók egyikének konfigurálásával kapcsolatos részletekért tekintse meg a [meghajtó technikai konfigurációjának tesztelése](./test-drive-technical-configuration.md)című témakört. 

### <a name="azure-resource-manager-test-drive"></a>Azure Resource Manager tesztelési meghajtó

Ez a központi telepítési sablon tartalmazza a megoldását alkotó összes Azure-erőforrást. Az ehhez a forgatókönyvhöz illeszkedő termékek csak az Azure-erőforrásokat használják. A Azure Resource Manager tesztelési meghajtó elérhető az alábbi típusú ajánlatokhoz: 

- Azure-alkalmazások
- SaaS
- Virtual machines (Virtuális gépek)

>[!NOTE]
>Ez az egyetlen tesztelési mód a virtuális gépekhez és az Azure-alkalmazások ajánlatához.

### <a name="hosted-test-drive-recommended"></a>Üzemeltetett tesztelési meghajtó (ajánlott)

Az üzemeltetett tesztvezetés eltávolítja a telepítő összetettségét azáltal, hogy a Microsoft gazdagépet és karbantartja a tesztelési meghajtó felhasználójának kiépítési és kiépítés utáni szolgáltatását. Ha Microsoft AppSource ajánlata van, készítse el a tesztelési meghajtót a Dynamics AX/CRM-példánnyal való kapcsolódáshoz. A következő AppSource-ajánlatokat használhatja:

- Használja a Dynamics 365-et a [Customer engagement](partner-center-portal/create-new-customer-engagement-offer.md) szolgáltatáshoz, például az értékesítés, a szolgáltatás, a Project Service és a Field Service szolgáltatáshoz.
- A [Dynamics 365 for Operations](partner-center-portal/create-new-operations-offer.md) for Finance and Operations vállalati erőforrás-tervezési rendszer, például a pénzügy, a műveletek és a gyártás, az ellátási lánc használata.

### <a name="logic-app-test-drive"></a>Logic app Test Drive

Az ilyen típusú tesztelési meghajtót nem a Microsoft üzemelteti, és Azure Resource Manager (ARM) sablonokat használ a Dynamics AX/CRM-ajánlatok típusaihoz. A szükséges erőforrások létrehozásához futtatnia kell az ARM-sablont az Azure-előfizetésében. A Logic app Test Drive jelenleg csak támogatási módban érhető el, és a Microsoft nem ajánlja a Logic app test-meghajtó konfigurálásával kapcsolatos részletekért lásd: a [Test Drive technikai konfigurálása](./test-drive-technical-configuration.md).

### <a name="power-bi-test-drive"></a>Power BI tesztelési meghajtó

Ez egyszerűen egy egyéni kialakítású irányítópultra mutató beágyazott hivatkozás. Minden olyan termék, amely csak interaktív Power BI vizualizációt mutat be, ezt a típusú tesztelési meghajtót kell használnia.

## <a name="transforming-examples"></a>Példák átalakítása

Az erőforrások egy tesztelési meghajtóba való bekapcsolásának folyamata ijesztő lehet. Tekintse át ezeket a példákat a jelenlegi architektúrák legjobb átalakításához.

[Webhely-sablon átalakítása tesztelési meghajtóra](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Website-Deployment-Template-for-Test-Drive)

[Virtuálisgép-sablon átalakítása tesztelési meghajtóra](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Virtual-Machine-Deployment-Template-for-Test-Drive)

[Meglévő Resource Manager-sablon átalakítása tesztelési meghajtóra](https://github.com/Azure/AzureTestDrive/wiki/Deploying-Existing-Solutions)

## <a name="generate-leads-from-your-test-drive"></a>Érdeklődők előállítása a test Drive-ból

A kereskedelmi Piactéri tesztvezetés nagyszerű eszköz a marketingesek számára. Azt javasoljuk, hogy a piacra jutást a piaci megjelenéssel kapcsolatos erőfeszítésekben érdemes beépíteni. Részletes útmutatásért tekintse [meg a kereskedelmi Marketplace-ajánlat ügyfeleinek vezetőit](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads.md).

Ha lezárta a tesztelési meghajtóval rendelkező üzletet, ügyeljen arra, hogy regisztrálja azt a [Microsoft partner Sales kapcsolódási](https://support.microsoft.com/help/3155788/getting-started-with-microsoft-partner-sales-connect)szolgáltatásban. Azt is szeretnénk hallani az ügyfél-WINS-ről, hogy a tesztvezetés milyen szerepet játszott.

## <a name="other-resources"></a>Egyéb erőforrások

További tesztelési meghajtó erőforrásai:

- [Tesztelési meghajtó – ajánlott eljárások](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Áttekintés](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (PDF; ellenőrizze, hogy ki van-e kapcsolva az előugró ablakok blokkolása)

## <a name="next-step"></a>Következő lépés

- [Tesztverzió műszaki konfigurálása](test-drive-technical-configuration.md)