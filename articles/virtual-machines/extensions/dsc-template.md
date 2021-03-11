---
title: A kívánt állapot konfigurációs bővítménye Azure Resource Manager-sablonokkal
description: Ismerje meg a Resource Manager-sablon definícióját az Azure kívánt State Configuration (DSC) bővítményéhez.
services: virtual-machines
author: mgoedtel
tags: azure-resource-manager
keywords: dsc
ms.assetid: b5402e5a-1768-4075-8c19-b7f7402687af
ms.service: virtual-machines
ms.subservice: extensions
ms.collection: windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 02/09/2021
ms.author: magoedte
ms.openlocfilehash: 6d22e93fe8bd36e24520108bc310d5b08c25120c
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102560021"
---
# <a name="desired-state-configuration-extension-with-azure-resource-manager-templates"></a>A kívánt állapot konfigurációs bővítménye Azure Resource Manager-sablonokkal

Ez a cikk a [kívánt állapot-konfigurációs (DSC) bővítmény kezelője](dsc-overview.md)Azure Resource Manager sablonját ismerteti. Számos példa a **RegistrationURL** (karakterláncként megadott) és a **RegistrationKey** (feltéve, hogy a Azure Automation a bevezetéshez [PSCredential](/dotnet/api/system.management.automation.pscredential) ) használja. Az értékek beszerzésével kapcsolatos részletekért lásd: [bevezetési gépek felügyelethez Azure Automation állapot konfigurálása – biztonságos regisztráció](../../automation/automation-dsc-onboarding.md#enable-machines-securely-using-registration).

> [!NOTE]
> Előfordulhat, hogy némileg eltérő séma-példákkal találkozhat. A séma változása a 2016 októberi kiadásban történt. Részletekért lásd: [korábbi formátum frissítése](#update-from-a-previous-format).

## <a name="template-example-for-a-windows-vm"></a>Sablon – példa Windows rendszerű virtuális géphez

A következő kódrészlet a sablon **erőforrás** szakaszában található.
A DSC-bővítmény örökli az alapértelmezett bővítmény tulajdonságait.
További információ: [VirtualMachineExtension osztály](/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension).

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(parameters('VMName'), '/Microsoft.Powershell.DSC')]",
  "apiVersion": "2018-06-01",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', parameters('VMName'))]"
  ],
  "properties": {
    "publisher": "Microsoft.Powershell",
    "type": "DSC",
    "typeHandlerVersion": "2.77",
    "autoUpgradeMinorVersion": true,
    "protectedSettings": {
      "Items": {
        "registrationKeyPrivate": "[listKeys(resourceId('Microsoft.Automation/automationAccounts/', parameters('automationAccountName')), '2018-06-30').Keys[0].value]"
      }
    },
    "settings": {
      "Properties": [
        {
          "Name": "RegistrationKey",
          "Value": {
            "UserName": "PLACEHOLDER_DONOTUSE",
            "Password": "PrivateSettingsRef:registrationKeyPrivate"
          },
          "TypeName": "System.Management.Automation.PSCredential"
        },
        {
          "Name": "RegistrationUrl",
          "Value": "[reference(concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))).registrationUrl]",
          "TypeName": "System.String"
        },
        {
          "Name": "NodeConfigurationName",
          "Value": "[parameters('nodeConfigurationName')]",
          "TypeName": "System.String"
        }
      ]
    }
  }
}
```

## <a name="template-example-for-windows-virtual-machine-scale-sets"></a>Példa sablonra a Windows rendszerű virtuálisgép-méretezési csoportokhoz

A virtuálisgép-méretezési csoport csomópontja tartalmaz egy **VirtualMachineProfile, extensionProfile** attribútummal rendelkező **Properties** szakaszt.
A **bővítmények** területen adja meg a DSC-bővítmény részleteit.

A DSC-bővítmény örökli az alapértelmezett bővítmény tulajdonságait.
További információ: [VirtualMachineScaleSetExtension osztály](/dotnet/api/microsoft.azure.management.compute.models.virtualmachinescalesetextension).

```json
"extensionProfile": {
    "extensions": [
      {
        "name": "Microsoft.Powershell.DSC",
        "properties": {
          "publisher": "Microsoft.Powershell",
          "type": "DSC",
          "typeHandlerVersion": "2.77",
          "autoUpgradeMinorVersion": true,
          "protectedSettings": {
            "Items": {
              "registrationKeyPrivate": "[listKeys(resourceId('Microsoft.Automation/automationAccounts/', parameters('automationAccountName')), '2018-06-30').Keys[0].value]"
            }
          },
          "settings": {
            "Properties": [
              {
                "Name": "RegistrationKey",
                "Value": {
                  "UserName": "PLACEHOLDER_DONOTUSE",
                  "Password": "PrivateSettingsRef:registrationKeyPrivate"
                },
                "TypeName": "System.Management.Automation.PSCredential"
              },
              {
                "Name": "RegistrationUrl",
                "Value": "[reference(concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))).registrationUrl]",
                "TypeName": "System.String"
              },
              {
                "Name": "NodeConfigurationName",
                "Value": "[parameters('nodeConfigurationName')]",
                "TypeName": "System.String"
              }
            ]
          }
        }
      }
    ]
  }
```

## <a name="detailed-settings-information"></a>Részletes beállítások információi

A Resource Manager-sablonok Azure DSC bővítményének **Beállítások** szakaszában használja az alábbi sémát.

Az alapértelmezett konfigurációs parancsfájlhoz elérhető argumentumok listáját itt tekintheti meg: [alapértelmezett konfigurációs parancsfájl](#default-configuration-script).

```json
"settings": {
    "wmfVersion": "latest",
    "configuration": {
        "url": "http://validURLToConfigLocation",
        "script": "ConfigurationScript.ps1",
        "function": "ConfigurationFunction"
    },
    "configurationArguments": {
        "argument1": "Value1",
        "argument2": "Value2"
    },
    "configurationData": {
        "url": "https://foo.psd1"
    },
    "privacy": {
        "dataCollection": "enable"
    },
    "advancedOptions": {
        "downloadMappings": {
            "customWmfLocation": "http://myWMFlocation"
        }
    }
},
"protectedSettings": {
    "configurationArguments": {
        "parameterOfTypePSCredential1": {
            "userName": "UsernameValue1",
            "password": "PasswordValue1"
        },
        "parameterOfTypePSCredential2": {
            "userName": "UsernameValue2",
            "password": "PasswordValue2"
        }
    },
    "configurationUrlSasToken": "?g!bber1sht0k3n",
    "configurationDataUrlSasToken": "?dataAcC355T0k3N"
}
```

## <a name="details"></a>Részletek

| Tulajdonság neve | Típus | Leírás |
| --- | --- | --- |
| Settings. wmfVersion |sztring |A Windows Management Framework (WMF) azon verzióját adja meg, amelyet telepíteni kell a virtuális gépre. Ha ezt a tulajdonságot a **legújabbra** állítja, a a WMF legújabb verzióját telepíti. Jelenleg a tulajdonság egyetlen lehetséges értéke **4,0**, **5,0**, **5,1** és **Latest**. Ezek a lehetséges értékek a frissítések tárgya. Az alapértelmezett érték a **legújabb**. |
| settings.configszülő. URL |sztring |Azt az URL-címet adja meg, amelyből le szeretné tölteni a DSC Configuration. zip fájlt. Ha a megadott URL-címnek szüksége van egy SAS-tokenre a hozzáféréshez, állítsa a **protectedSettings.configurationUrlSasToken** tulajdonságot az SAS-token értékére. Ez a tulajdonság akkor szükséges, ha **settings.configszülő. script** vagy **settings.configszülő. Function** van definiálva. Ha nem adott meg értéket ezekhez a tulajdonságokhoz, a bővítmény meghívja az alapértelmezett konfigurációs parancsfájlt a Location Configuration Manager (LCD) metaadatainak beállításához, és meg kell adni az argumentumokat. |
| settings.configszülő. script |sztring |A DSC-konfiguráció definícióját tartalmazó parancsfájl fájlnevét adja meg. A szkriptnek a **settings.configszülő. URL** tulajdonság által megadott URL-címről letöltött. zip fájl gyökérkönyvtárában kell lennie. Ez a tulajdonság akkor szükséges, ha **settings.configszülő. URL** vagy **settings.configszülő. script** van definiálva. Ha nem adott meg értéket ezekhez a tulajdonságokhoz, a bővítmény meghívja az alapértelmezett konfigurációs parancsfájlt, amely az LCD-metaadatokat állítja be, és az argumentumokat meg kell adni. |
| settings.configszülő. Function |sztring |Megadja a DSC-konfiguráció nevét. A nevű konfigurációnak szerepelnie kell a parancsfájlban, amely **settings.configszülő. script** definiál. Ez a tulajdonság akkor szükséges, ha **settings.configszülő. URL** vagy **settings.configszülő. Function** van definiálva. Ha nem adott meg értéket ezekhez a tulajdonságokhoz, a bővítmény meghívja az alapértelmezett konfigurációs parancsfájlt, amely az LCD-metaadatokat állítja be, és az argumentumokat meg kell adni. |
| settings.configurationArguments |Gyűjtemény |Meghatározza a DSC-konfigurációnak átadni kívánt paramétereket. Ez a tulajdonság nincs titkosítva. |
| settings.configurationData. URL |sztring |Meghatározza azt az URL-címet, amelyből le szeretné tölteni a DSC-konfiguráció bemenetként használandó konfigurációs adatait (. psd1). Ha a megadott URL-címnek szüksége van egy SAS-tokenre a hozzáféréshez, állítsa a **protectedSettings.configurationDataUrlSasToken** tulajdonságot az SAS-token értékére. |
| Settings. privacy. Datacollection objektumot |sztring |Engedélyezheti vagy letilthatja a telemetria-gyűjteményt. A tulajdonság egyetlen lehetséges értéke a következő: **Engedélyezés**, **Letiltás**, **""** vagy **$Null**. Ha ezt a tulajdonságot üresen hagyja, vagy NULL értékűre teszi a telemetria. Az alapértelmezett érték: **""**. További információ: az [Azure DSC bővítmény adatgyűjtése](https://devblogs.microsoft.com/powershell/azure-dsc-extension-data-collection-2/). |
| Settings. advancedOptions. downloadMappings |Gyűjtemény |Meghatározza azokat a másodlagos helyeket, amelyekről a WMF-t le szeretné tölteni. További információkért lásd: [Azure DSC-bővítmény 2,8 és hogyan lehet leképezni a bővítmény függőségeinek letöltését a saját helyére](https://devblogs.microsoft.com/powershell/azure-dsc-extension-2-8-how-to-map-downloads-of-the-extension-dependencies-to-your-own-location/). |
| protectedSettings.configurationArguments |Gyűjtemény |Meghatározza a DSC-konfigurációnak átadni kívánt paramétereket. Ez a tulajdonság titkosítva van. |
| protectedSettings.configurationUrlSasToken |sztring |Meghatározza az URL-cím eléréséhez használandó SAS-tokent **settings.configszülő. URL** határozza meg. Ez a tulajdonság titkosítva van. |
| protectedSettings.configurationDataUrlSasToken |sztring |Meghatározza az URL-cím eléréséhez használandó SAS-tokent  **settings.configurationData. URL** határozza meg. Ez a tulajdonság titkosítva van. |

## <a name="default-configuration-script"></a>Alapértelmezett konfigurációs parancsfájl

A következő értékekkel kapcsolatos további információkért lásd: [helyi Configuration Manager alapszintű beállítások](/powershell/scripting/dsc/managing-nodes/metaConfig#basic-settings).
A DSC-bővítmény alapértelmezett konfigurációs parancsfájlja csak az alábbi táblázatban felsorolt LCD-tulajdonságok konfigurálására használható.

| Tulajdonság neve | Típus | Leírás |
| --- | --- | --- |
| protectedSettings.configurationArguments. RegistrationKey |PSCredential |Kötelező tulajdonság. Meghatározza azt a kulcsot, amelyet egy csomóponthoz használ a Azure Automation szolgáltatásban egy PowerShell hitelesítőadat-objektum jelszavaként való regisztráláshoz. Ez az érték automatikusan felderíthető az Automation-fiók **listkeys műveletének beolvasása** metódusának használatával.  Lásd a [példát](#example-using-referenced-azure-automation-registration-values). |
| settings.configurationArguments. RegistrationUrl |sztring |Kötelező tulajdonság. Megadja annak az Automation-végpontnak az URL-címét, amelyben a csomópont megpróbál regisztrálni. Ez az érték automatikusan felderíthető az Automation-fiókra vonatkozó **hivatkozási** módszer használatával. |
| settings.configurationArguments. NodeConfigurationName |sztring |Kötelező tulajdonság. Megadja az Automation-fiók csomópont-konfigurációját a csomóponthoz való hozzárendeléshez. |
| settings.configurationArguments.ConfigurationMode |sztring |Megadja az LCD/ChipOnGlas üzemmódot. Az érvényes beállítások a következők: **ApplyOnly**, **ApplyandMonitor** és **ApplyandAutoCorrect**.  Az alapértelmezett érték a **ApplyandMonitor**. |
| settings.configurationArguments. RefreshFrequencyMins | UInt32 | Meghatározza, hogy az LCD-eszköz milyen gyakran próbálkozzon az Automation-fiókkal a frissítésekhez.  Az alapértelmezett érték **30**.  A minimális érték **15**. |
| settings.configurationArguments.ConfigurationModeFrequencyMins | UInt32 | Azt határozza meg, hogy az LCD/ChipOnGlas milyen gyakran ellenőrizze az aktuális konfigurációt. Az alapértelmezett érték **15**. A minimális érték **15**. |
| settings.configurationArguments. RebootNodeIfNeeded | boolean | Meghatározza, hogy a csomópontok automatikusan újraindulnak-e, ha egy DSC-művelet kéri. Az alapértelmezett érték **false (hamis**). |
| settings.configurationArguments. ActionAfterReboot | sztring | Itt adható meg, hogy mi történjen a konfiguráció alkalmazásának újraindításakor. Az érvényes beállítások a következők: **ContinueConfiguration** és **stopconfiguration metódusa**. Az alapértelmezett érték a **ContinueConfiguration**. |
| settings.configurationArguments. AllowModuleOverwrite | boolean | Meghatározza, hogy a LCD-eszközök felülírják-e a csomóponton meglévő modulokat. Az alapértelmezett érték **false (hamis**). |

## <a name="settings-vs-protectedsettings"></a>beállítások és Protectedsettingsfromkeyvault

A rendszer minden beállítást a virtuális gép egy beállítási szövegfájlba ment.
A **Beállítások** területen felsorolt tulajdonságok nyilvános tulajdonságok.
A nyilvános tulajdonságok nem titkosítva vannak a beállítások szövegfájljában.
A **protectedsettingsfromkeyvault** alatt felsorolt tulajdonságok tanúsítvánnyal vannak titkosítva, és nem jelennek meg egyszerű szövegben a virtuális gép beállítási fájljában.

Ha a konfigurációhoz hitelesítő adatokra van szükség, a hitelesítő adatokat a **protectedsettingsfromkeyvault**-ben is megadhatja:

```json
"protectedSettings": {
    "configurationArguments": {
        "parameterOfTypePSCredential1": {
               "userName": "UsernameValue1",
               "password": "PasswordValue1"
        }
    }
}
```

## <a name="example-configuration-script"></a>Példa konfigurációs parancsfájlra

Az alábbi példa a DSC-bővítmény alapértelmezett viselkedését mutatja be, amely az LCD-s és a Automation DSC szolgáltatásban való regisztráláshoz biztosít metaadat-beállításokat.
A konfigurációs argumentumok megadása kötelező.
A konfigurációs argumentumokat a rendszer az alapértelmezett konfigurációs parancsfájlnak adja át az LCD-metaadatok beállításához.

```json
"settings": {
    "configurationArguments": {
        "RegistrationUrl" : "[parameters('registrationUrl1')]",
        "NodeConfigurationName" : "nodeConfigurationNameValue1"
    }
},
"protectedSettings": {
    "configurationArguments": {
        "RegistrationKey": {
            "userName": "NOT_USED",
            "Password": "registrationKey"
        }
    }
}
```

## <a name="example-using-the-configuration-script-in-azure-storage"></a>Példa az Azure Storage konfigurációs parancsfájljának használatával

A következő példa a [DSC bővítmény kezelőjének áttekintése](dsc-overview.md).
Ez a példa Resource Manager-sablonokat használ a bővítmény telepítéséhez a parancsmagok helyett.
Mentse a IisInstall.ps1 konfigurációt, helyezze egy. zip-fájlba (például: `iisinstall.zip` ), majd töltse fel a fájlt egy elérhető URL-címen.
Ez a példa az Azure Blob Storage-t használja, de tetszőleges helyről letöltheti a. zip fájlokat.

A Resource Manager-sablonban a következő kód arra utasítja a virtuális gépet, hogy töltse le a megfelelő fájlt, majd futtassa a megfelelő PowerShell-függvényt:

```json
"settings": {
    "configuration": {
        "url": "https://demo.blob.core.windows.net/iisinstall.zip",
        "script": "IisInstall.ps1",
        "function": "IISInstall"
    }
},
"protectedSettings": {
    "configurationUrlSasToken": "odLPL/U1p9lvcnp..."
}
```

## <a name="example-using-referenced-azure-automation-registration-values"></a>Példa hivatkozott Azure Automation regisztrációs értékeket használó

A következő példa lekéri a **RegistrationUrl** és a **RegistrationKey** az Azure Automation fiók tulajdonságaira való hivatkozással, és a  **listkeys műveletének beolvasása** metódus használatával kéri le az elsődleges kulcsot (0).  Ebben a példában a **automationAccountName** és a **NodeConfigName** paramétereket adtak meg a sablonhoz.

```json
"settings": {
    "RegistrationUrl" : "[reference(concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))).registrationUrl]",
    "NodeConfigurationName" : "[parameters('NodeConfigName')]"
},
"protectedSettings": {
    "configurationArguments": {
        "RegistrationKey": {
            "userName": "NOT_USED",
            "Password": "[listKeys(resourceId('Microsoft.Automation/automationAccounts/', parameters('automationAccountName')), '2018-01-15').Keys[0].value]"
        }
    }
}
```

## <a name="update-from-a-previous-format"></a>Frissítés korábbi formátumból

A bővítmény korábbi formátumában (és a **ModulesUrl**, **ModuleSource**, **ModuleVersion**, **ConfigurationFunction**, **SasToken** vagy **Properties**) lévő beállítások automatikusan alkalmazkodnak a bővítmény aktuális formátumához.
Ugyanúgy futnak, mint korábban.

Az alábbi séma azt mutatja be, hogy az előző beállítások séma milyen módon nézett ki:

```json
"settings": {
    "WMFVersion": "latest",
    "ModulesUrl": "https://UrlToZipContainingConfigurationScript.ps1.zip",
    "SasToken": "SAS Token if ModulesUrl points to private Azure Blob Storage",
    "ConfigurationFunction": "ConfigurationScript.ps1\\ConfigurationFunction",
    "Properties": {
        "ParameterToConfigurationFunction1": "Value1",
        "ParameterToConfigurationFunction2": "Value2",
        "ParameterOfTypePSCredential1": {
            "UserName": "UsernameValue1",
            "Password": "PrivateSettingsRef:Key1"
        },
        "ParameterOfTypePSCredential2": {
            "UserName": "UsernameValue2",
            "Password": "PrivateSettingsRef:Key2"
        }
    }
},
"protectedSettings": {
    "Items": {
        "Key1": "PasswordValue1",
        "Key2": "PasswordValue2"
    },
    "DataBlobUri": "https://UrlToConfigurationDataWithOptionalSasToken.psd1"
}
```

Az előző formátum az aktuális formátumhoz alkalmazkodik:

| Aktuális tulajdonságnév | Korábbi séma-egyenérték |
| --- | --- |
| Settings. wmfVersion |beállítások. WMFVersion |
| settings.configszülő. URL |beállítások. ModulesUrl |
| settings.configszülő. script |settings.ConfigurationFunction első része (előtte \\ \\ ) |
| settings.configszülő. Function |settings.ConfigurationFunction második része (utána \\ \\ ) |
| settings.configuration.module.name | beállítások. ModuleSource |
| settings.configszülő. Module. Version | beállítások. ModuleVersion |
| settings.configurationArguments |beállítások. Tulajdonságok |
| settings.configurationData. URL |Protectedsettingsfromkeyvault. DataBlobUri (SAS-token nélkül) |
| Settings. privacy. Datacollection objektumot |beállítások. Adatvédelem. Datacollection objektumot |
| Settings. advancedOptions. downloadMappings |beállítások. AdvancedOptions.DownloadMappings |
| protectedSettings.configurationArguments |Protectedsettingsfromkeyvault. properties |
| protectedSettings.configurationUrlSasToken |beállítások. SasToken |
| protectedSettings.configurationDataUrlSasToken |SAS-token a Protectedsettingsfromkeyvault. DataBlobUri |

## <a name="troubleshooting"></a>Hibaelhárítás

Íme néhány, a hibák, amelyek bejelentkezhetnek, és hogyan javíthatja azokat.

### <a name="invalid-values"></a>Érvénytelen értékek

"Az adatvédelem. Datacollection objektumot:" " {0} .
Az egyetlen lehetséges érték: "", "Enable" és "Disable" ".
"A WmfVersion:" " {0} .
Csak a lehetséges értékek:... és a "legújabb" ".

**Probléma**: a megadott érték nem engedélyezett.

**Megoldás**: módosítsa az érvénytelen értéket érvényes értékre.
További információ: táblázat, [részletek](#details).

### <a name="invalid-url"></a>Érvénytelen URL-cím

"A ConfigurationData. URL a következő:" " {0} . Ez nem egy érvényes URL-cím: "" DataBlobUri: "" {0} . Ez nem egy érvényes URL-cím: "" Configuration. URL-cím: "" {0} . Ez nem egy érvényes URL-cím: "

**Probléma**: A megadott URL-cím érvénytelen.

**Megoldás**: keresse meg az összes megadott URL-címet.
Győződjön meg arról, hogy az összes URL-cím a bővítmény által a távoli gépen elérhető érvényes helyekre van feloldva.

### <a name="invalid-registrationkey-type"></a>Érvénytelen RegistrationKey-típus

"A PSCredential típusú RegistrationKey paraméter típusa érvénytelen."

**Probléma**: protectedSettings.configurationArguments *RegistrationKey* értéke nem adható meg a PSCredential eltérő típusként.

**Megoldás**: módosítsa protectedSettings.configa RegistrationKey urationArguments bejegyzését egy PSCredential-típusra a következő formátum használatával:

```json
"configurationArguments": {
    "RegistrationKey": {
        "userName": "NOT_USED",
        "Password": "RegistrationKey"
    }
}
```

### <a name="invalid-configurationargument-type"></a>Érvénytelen ConfigurationArgument-típus

"Érvénytelen configurationArguments típus {0} "

**Probléma**: a *ConfigurationArguments* tulajdonság nem oldható fel **kivonatoló tábla** objektumra.

**Megoldás**: a *ConfigurationArguments* tulajdonságot **kivonatoló táblázatként** adja meg.
Kövesse az előző példákban megadott formátumot. Tekintse meg az idézőjeleket, a vesszőket és a kapcsos zárójeleket.

### <a name="duplicate-configurationarguments"></a>Ismétlődő ConfigurationArguments

A "" duplikált argumentumok találhatók {0} a nyilvános és a védett configurationArguments is "

**Probléma**: a nyilvános beállításokban található *ConfigurationArguments* és a védett beállítások *ConfigurationArguments* azonos nevű tulajdonságokkal rendelkeznek.

**Megoldás**: távolítsa el a duplikált tulajdonságok egyikét.

### <a name="missing-properties"></a>Hiányzó tulajdonságok

"settings.Configszülő. Function megköveteli, hogy settings.configszülő. URL vagy settings.configszülő. Module van megadva"

"settings.Configszülő. URL szükséges, hogy a settings.configszülő. script meg legyen adva"

"settings.Configszülő. script szükséges, hogy a settings.configszülő. URL meg legyen adva"

"settings.Configszülő. URL szükséges, hogy a settings.configszülő. Function meg legyen adva"

"protectedSettings.ConfigurationUrlSasToken szükséges, hogy a settings.configszülő. URL meg legyen adva"

"protectedSettings.ConfigurationDataUrlSasToken szükséges, hogy a settings.configurationData. URL meg legyen adva"

**Probléma**: egy definiált tulajdonsághoz egy másik tulajdonságra van szükség, amely hiányzik.

**Megoldások**:

- Adja meg a hiányzó tulajdonságot.
- Távolítsa el a hiányzó tulajdonságot igénylő tulajdonságot.

## <a name="next-steps"></a>Következő lépések

- Ismerje meg [, hogyan használhatja a virtuálisgép-méretezési csoportokat az Azure DSC bővítménnyel](../../virtual-machine-scale-sets/virtual-machine-scale-sets-dsc.md).
- További információ a [DSC biztonságos hitelesítőadat-kezeléséről](dsc-credentials.md).
- Ismerkedjen [meg az Azure DSC bővítmény kezelőjével](dsc-overview.md).
- A PowerShell DSC-vel kapcsolatos további információkért nyissa meg a [PowerShell Dokumentációs központját](/powershell/scripting/dsc/overview/overview).
