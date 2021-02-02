---
title: Adatkonverzió a FHIR készült Azure API-hoz
description: Az adat$convert-adatvégpont és a Testreszabás – átalakító sablonok segítségével az Azure API-ban FHIR alakíthatja az adattípusokat.
services: healthcare-apis
author: ranvijaykumar
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 01/19/2021
ms.author: ranku
ms.openlocfilehash: 7518f5e2984029c087eec1e6697f3237410bda4b
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430276"
---
# <a name="how-to-convert-data-to-fhir-preview"></a>Az adatátalakítás FHIR (előzetes verzió)

> [!IMPORTANT]
> Ez a képesség nyilvános előzetes verzióban érhető el, nem vonatkozik rá szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A FHIR készült Azure API-ban található $convert-adatok egyéni végpontja a különböző formátumokból a FHIR-re való adatátalakítást jelenti. Az alapértelmezett sablonként a [FHIR Converter](https://github.com/microsoft/FHIR-Converter) -projektben használja a likvid sablon motorját és a sablonokat. Igény szerint testre szabhatja ezeket a konverziós sablonokat. Jelenleg támogatja a HL7v2-t a FHIR átalakításhoz.

## <a name="use-the-convert-data-endpoint"></a>A $convert-adatvégpont használata

`https://<<FHIR service base URL>>/$convert-data`

$convert – az [adatforrást](http://hl7.org/fhir/parameters.html) a kérelem törzsében a következő módon kell megadni:

**Paraméter erőforrása:**

| Paraméter neve      | Leírás | Elfogadott értékek |
| ----------- | ----------- | ----------- |
| inputData      | Konvertálandó adatértékek. | A JSON string adattípusának érvényes értéke|
| inputDataType   | A bemenet adattípusa. | ```HL7v2``` |
| templateCollectionReference | Hivatkozás egy sablon gyűjteményére. Az **alapértelmezett sablonokra** vagy az Azure API-FHIR regisztrált egyéni sablonra mutató hivatkozás lehet. A következő cikkből megtudhatja, hogyan szabhatja testre a sablonokat, hogyan üzemeltetheti az ACR-et, és hogyan regisztrálhat a FHIR készült Azure API-ra.  | ```microsofthealth/fhirconverter:default```, \<RegistryServer\>/\<imageName\>@\<imageDigest\> |
| rootTemplate | Az adatátalakítás során használandó legfelső szintű sablon. | ```ADT_A01```, ```OML_O21```, ```ORU_R01```, ```VXU_V04``` |  

> [!WARNING]
> Az alapértelmezett sablonok segítségével gyorsan elsajátíthatja az első lépéseket. Előfordulhat azonban, hogy ezek frissülnek a FHIR készült Azure API frissítésekor. Ahhoz, hogy konzisztens Adatátalakítási viselkedést lehessen alkalmazni az Azure API különböző verzióiban az FHIR-hoz, a sablonok saját példányát kell üzemeltetni egy Azure Container Registry, regisztrálnia kell a FHIR Azure API-ra, és az API-hívásokban a későbbiekben leírt módon kell használnia azokat.

**Példa a kérelemre:**

```json
{
    "resourceType": "Parameters",
    "parameter": [
        {
            "name": "inputData",
            "valueString": "MSH|^~\\&|SIMHOSP|SFAC|RAPP|RFAC|20200508131015||ADT^A01|517|T|2.3|||AL||44|ASCII\nEVN|A01|20200508131015|||C005^Whittingham^Sylvia^^^Dr^^^DRNBR^PRSNL^^^ORGDR|\nPID|1|3735064194^^^SIMULATOR MRN^MRN|3735064194^^^SIMULATOR MRN^MRN~2021051528^^^NHSNBR^NHSNMBR||Kinmonth^Joanna^Chelsea^^Ms^^CURRENT||19870624000000|F|||89 Transaction House^Handmaiden Street^Wembley^^FV75 4GJ^GBR^HOME||020 3614 5541^HOME|||||||||C^White - Other^^^||||||||\nPD1|||FAMILY PRACTICE^^12345|\nPV1|1|I|OtherWard^MainRoom^Bed 183^Simulated Hospital^^BED^Main Building^4|28b|||C005^Whittingham^Sylvia^^^Dr^^^DRNBR^PRSNL^^^ORGDR|||CAR|||||||||16094728916771313876^^^^visitid||||||||||||||||||||||ARRIVED|||20200508131015||"
        },
        {
            "name": "inputDataType",
            "valueString": "Hl7v2"
        },
        {
            "name": "templateCollectionReference",
            "valueString": "microsofthealth/fhirconverter:default"
        },
        {
            "name": "rootTemplate",
            "valueString": "ADT_A01"
        }
    ]
}
```

**Példa a válaszra:**

```json
{
  "resourceType": "Bundle",
  "type": "transaction",
  "entry": [
    {
      "fullUrl": "urn:uuid:9d697ec3-48c3-3e17-db6a-29a1765e22c6",
      "resource": {
        "resourceType": "Patient",
        "id": "9d697ec3-48c3-3e17-db6a-29a1765e22c6",
        ...
        ...
      "request": {
        "method": "PUT",
        "url": "Location/50becdb5-ff56-56c6-40a1-6d554dca80f0"
      }
    }
  ]
}
```

## <a name="customize-templates"></a>Sablonok testreszabása

A Visual Studio Code-hoz készült [FHIR Converter bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-health-fhir-converter) használatával igényei szerint testre szabhatja a sablonokat. A bővítmény interaktív szerkesztési élményt nyújt, és megkönnyíti a Microsoft által közzétett sablonok és mintaadatok letöltését. A részletekért tekintse meg a bővítmény dokumentációját.

## <a name="host-and-use-templates"></a>Gazdagépek és sablonok használata

Erősen ajánlott a sablonok saját példányának üzemeltetése az ACR-ben. A sablonok saját példányának üzemeltetése és a $convert-adatműveletben való használata négy lépésből áll:

1. Küldje le a sablonokat a Azure Container Registry.
1. Felügyelt identitás engedélyezése az Azure API FHIR-példányához.
1. Az ACR hozzáférésének biztosítása az Azure API-hoz a FHIR által felügyelt identitáshoz.
1. Regisztrálja az ACR-kiszolgálókat a FHIR készült Azure API-ban.

### <a name="push-templates-to-azure-container-registry"></a>Sablonok leküldése Azure Container Registry

Az ACR-példány létrehozása után használhatja a _FHIR Converter: push templates_ parancsot a [FHIR Converter bővítménnyel](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-health-fhir-converter) , hogy a testreszabott sablonokat az ACR-be küldje. Azt is megteheti, hogy erre a célra a [sablon-felügyeleti CLI eszközt](https://github.com/microsoft/FHIR-Converter/blob/main/docs/TemplateManagementCLI.md) használja.

### <a name="enable-managed-identity-on-azure-api-for-fhir"></a>Felügyelt identitás engedélyezése a FHIR készült Azure API-ban

Keresse meg az Azure API FHIR Service-példányát a Azure Portal, és válassza ki az **Identity (identitás** ) panelt.
Módosítsa az állapotot **a on** értékre, hogy engedélyezze a felügyelt identitást a FHIR készült Azure API-ban.

![Felügyelt identitás engedélyezése](media/convert-data/fhir-mi-enabled.png)

### <a name="provide-access-of-the-acr-to-azure-api-for-fhir"></a>Az ACR hozzáférésének biztosítása az FHIR Azure API-hoz

Navigáljon a Access Control (IAM) panelre az ACR-példányban, és válassza a _szerepkör-hozzárendelések hozzáadása_ lehetőséget.

![ACR-szerepkör hozzárendelése](media/convert-data/fhir-acr-role-assignment.png)

Adja meg a AcrPull-szerepkört az Azure API-hoz a FHIR Service-példányhoz.

![Szerepkör hozzáadása](media/convert-data/fhir-acr-role-add.png)

### <a name="register-the-acr-servers-in-azure-api-for-fhir"></a>Az ACR-kiszolgálók regisztrálása az Azure API-ban FHIR

A FHIR Azure API-ban legfeljebb húsz ACR-kiszolgálót regisztrálhat.

Szükség esetén telepítse a healthcareapis CLI-t Azure PowerShellról:

```powershell
az extension add -n healthcareapis
```

Regisztrálja az ACR-kiszolgálókat az Azure API-hoz a FHIR az alábbi példákat követve:

#### <a name="register-a-single-acr-server"></a>Egyetlen ACR-kiszolgáló regisztrálása

```powershell
az healthcareapis acr add --login-servers "fhiracr2021.azurecr.io" --resource-group fhir-test --resource-name fhirtest2021
```

#### <a name="register-multiple-acr-servers"></a>Több ACR-kiszolgáló regisztrálása

```powershell
az healthcareapis acr add --login-servers "fhiracr2021.azurecr.io fhiracr2020.azurecr.io" --resource-group fhir-test --resource-name fhirtest2021
```

### <a name="verify"></a>Ellenőrzés

Hívja meg a $convert-adatapi-t, amely megadja a sablon hivatkozását a templateCollectionReference paraméterben.

`<RegistryServer>/<imageName>@<imageDigest>`

## <a name="known-issues-and-workarounds"></a>Ismert problémák és megkerülő megoldások

- Néhány alapértelmezett sablonfájl UTF-8 ANYAGJEGYZÉKet tartalmaz. Ennek eredményeképpen a generált azonosító értékek AJ-karaktert fognak tartalmazni. Ez problémát okozhat a FHIR-kiszolgálóval. A megkerülő megoldás az, hogy a Microsoft sablonjait a VS Code bővítmény használatával húzza le, és a _ID/_eljárás. Liquid_, _ID/_Proving. liquid_, and _ID/_immunizálás. Liquid_ fájl eltávolítása után LEküldi azokat a saját ACR-hez.

