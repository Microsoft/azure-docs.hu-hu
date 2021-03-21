---
title: FELHASZNÁLÓIFELÜLET-definíciós elemek létrehozása
description: A Azure Portal felhasználói felületi definícióinak összeállításakor használandó elemeket ismerteti.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/27/2020
ms.author: tomfitz
ms.openlocfilehash: 3e8bdd85c112d90fd5e10f6e65e48ca6b3f71b6e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94410742"
---
# <a name="createuidefinition-elements"></a>CreateUiDefinition elemek

Ez a cikk a CreateUiDefinition összes támogatott elemének sémáját és tulajdonságait ismerteti.

## <a name="schema"></a>Séma

A legtöbb elem sémája a következő:

```json
{
  "name": "element1",
  "type": "Microsoft.Common.TextBox",
  "label": "Some text box",
  "defaultValue": "my value",
  "toolTip": "Provide a descriptive name.",
  "constraints": {},
  "options": {},
  "visible": true
}
```

| Tulajdonság | Kötelező | Leírás |
| -------- | -------- | ----------- |
| name | Yes | Egy belső azonosító, amely egy elem adott példányára hivatkozik. Az elem nevének leggyakoribb használata a (z `outputs` ), ahol a megadott elemek kimeneti értékei a sablon paramétereinek megfelelően vannak leképezve. Azt is megteheti, hogy egy elem kimeneti értékét `defaultValue` egy másik elemhez köti. |
| típus | Yes | Az elemhez tartozó FELHASZNÁLÓIFELÜLET-vezérlő. A támogatott típusok listáját lásd: [elemek](#elements). |
| címke | Yes | Az elem megjelenített szövege Egyes elemek több címkét tartalmaznak, így az érték lehet egy több sztringet tartalmazó objektum. |
| defaultValue | No | Az elem alapértelmezett értéke. Egyes elemtípus-típusok összetett alapértelmezett értékeket támogatnak, így az érték lehet egy objektum. |
| toolTip | No | Az elem elemleírásában megjelenítendő szöveg Ehhez hasonlóan `label` egyes elemek több elemleírás-karakterláncot is támogatnak. A beágyazott hivatkozások a Markdown szintaxis használatával ágyazhatók be.
| korlátok | No | Egy vagy több olyan tulajdonság, amely az elem érvényesítési viselkedésének testreszabására szolgál. A megkötések támogatott tulajdonságai elem típusa szerint változnak. Egyes elemtípus-típusok nem támogatják az érvényesítési viselkedés testreszabását, így nincsenek megkötések tulajdonsága. |
| beállítások | No | További tulajdonságok, amelyek testre szabják az elem viselkedését. `constraints`A következőhöz hasonlóan a támogatott tulajdonságok elemtípus szerint változnak. |
| látható | No | Azt jelzi, hogy megjelenik-e az elem. Ha `true` a elem és a megfelelő alárendelt elemek jelennek meg. Az alapértelmezett érték `true`. A tulajdonság értékének dinamikus vezérléséhez használja a [logikai függvényeket](create-uidefinition-functions.md#logical-functions) .

## <a name="elements"></a>Elemek

Az egyes elemek dokumentációja tartalmaz egy felhasználói felületi mintát, sémát, a elem viselkedésére vonatkozó megjegyzéseket (általában az érvényesítéssel és a támogatott testreszabással kapcsolatban) és a minta kimenetével.

- [Microsoft. Common. CheckBox](microsoft-common-checkbox.md)
- [Microsoft.Common.DropDown](microsoft-common-dropdown.md)
- [Microsoft. Common. EditableGrid](microsoft-common-editablegrid.md)
- [Microsoft.Common.FileUpload](microsoft-common-fileupload.md)
- [Microsoft. Common. bezárása](microsoft-common-infobox.md)
- [Microsoft.Common.OptionsGroup](microsoft-common-optionsgroup.md)
- [Microsoft.Common.PasswordBox](microsoft-common-passwordbox.md)
- [Microsoft.Common.Section](microsoft-common-section.md)
- [Microsoft. Common. ServicePrincipalSelector](microsoft-common-serviceprincipalselector.md)
- [Microsoft. Common. Slider](microsoft-common-slider.md)
- [Microsoft. Common. TagsByResource](microsoft-common-tagsbyresource.md)
- [Microsoft. Common. TextBlock](microsoft-common-textblock.md)
- [Microsoft.Common.TextBox](microsoft-common-textbox.md)
- [Microsoft.Compute.CredentialsCombo](microsoft-compute-credentialscombo.md)
- [Microsoft.Compute.SizeSelector](microsoft-compute-sizeselector.md)
- [Microsoft.Compute.UserNameTextBox](microsoft-compute-usernametextbox.md)
- [Microsoft. kulcstartó. KeyVaultCertificateSelector](microsoft-keyvault-keyvaultcertificateselector.md)
- [Microsoft. ManagedIdentity. IdentitySelector](microsoft-managedidentity-identityselector.md)
- [Microsoft.Network.PublicIpAddressCombo](microsoft-network-publicipaddresscombo.md)
- [Microsoft.Network.VirtualNetworkCombo](microsoft-network-virtualnetworkcombo.md)
- [Microsoft. Solutions. ArmApiControl](microsoft-solutions-armapicontrol.md)
- [Microsoft. Solutions. ResourceSelector](microsoft-solutions-resourceselector.md)
- [Microsoft.Storage.MultiStorageAccountCombo](microsoft-storage-multistorageaccountcombo.md)
- [Microsoft.Storage.StorageAccountSelector](microsoft-storage-storageaccountselector.md)
- [Microsoft. Storage. StorageBlobSelector](microsoft-storage-storageblobselector.md)

## <a name="next-steps"></a>Következő lépések

A felhasználói felületi definíciók létrehozásával kapcsolatban lásd: Bevezetés [a CreateUiDefinition](create-uidefinition-overview.md)használatába.
