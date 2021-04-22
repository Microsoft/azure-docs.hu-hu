---
title: A hatások hatásának a hatása
description: Azure Policy definícióknak különböző hatásaik vannak, amelyek meghatározzák a megfelelőség kezelése és jelentése mikéntját.
ms.date: 04/19/2021
ms.topic: conceptual
ms.openlocfilehash: e0d6eb5fb37ecf1b13edd945de52398b1e12f192
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861552"
---
# <a name="understand-azure-policy-effects"></a>A Azure Policy hatásainak

Az Azure Policyban minden egyes szabályzatdefiníció egyetlen hatással bír. Ez a hatás határozza meg, hogy mi történik, ha a rendszer kiértékeli, hogy a szabály megfelel-e a szabályzatnak. A hatások másképp viselkednek, ha új erőforrásra, frissített erőforrásra vagy meglévő erőforrásra vannak hatással.

Ezek a hatások jelenleg a szabályzatdefiníciókban támogatottak:

- [Append](#append)
- [Audit](#audit)
- [AuditIfNotExists](#auditifnotexists)
- [Deny](#deny)
- [DeployIfNotExists](#deployifnotexists)
- [Disabled](#disabled)
- [Modify](#modify)

A következő hatások _elavultak:_

- [AzOPAConstraint kényszerítés](#enforceopaconstraint)
- [EnforceRegoPolicy](#enforceregopolicy)

> [!IMPORTANT]
> A **EnforceOPAConstraint** vagy **a EnforceRegoPolicy** hatás  helyére használja a _naplózást_ és a megtagadás erőforrás-szolgáltató módban való `Microsoft.Kubernetes.Data` használatát. Frissültek a beépített szabályzatdefiníciók. A beépített szabályzatdefiníciók meglévő szabályzat-hozzárendelésének  módosításakor az hatásparamétert a frissített _allowedValues_ listában található értékre kell módosítani.

## <a name="order-of-evaluation"></a>Kiértékelési sorrend

Az erőforrás létrehozására vagy frissítésére vonatkozó kéréseket a rendszer először Azure Policy kiértékeli. Azure Policy létrehoz egy listát az erőforrásra vonatkozó összes hozzárendelésről, majd kiértékeli az erőforrást az egyes definíciók alapján. A Resource Manager [módban](./definition-structure.md#resource-manager-modes)a Azure Policy feldolgoz néhány hatást, mielőtt a kérést a megfelelő erőforrás-szolgáltatónak ad át. Ez a sorrend megakadályozza, hogy az erőforrás-szolgáltató szükségtelenül feldolgozzanak, ha egy erőforrás nem felel meg a vállalat tervezett cégirányítási Azure Policy. Az [erőforrás-szolgáltató módban](./definition-structure.md#resource-provider-modes)az erőforrás-szolgáltató kezeli a kiértékelést és az eredményt, és jelenti az eredményeket a Azure Policy.

- **A** rendszer először a Disabled (Letiltva) megállapításához ellenőrzi, hogy a rendszer kiértékeli-e a szabályzatszabályt.
- **A hozzáfűzés** **és** a módosítás ezután ki lesz értékelve. Mivel bármelyik módosíthatja a kérést, a módosítás megakadályozhatja a naplózási vagy megtagadási hatás aktiválását. Ezek a hatások csak a Resource Manager érhetőek el.
- **A megtagadás** ezután ki lesz értékelve. Ha kiértékeli a megtagadás naplózás előtti kiértékelését, a rendszer megakadályozza a nem szükséges erőforrások dupla naplózását.
- **A** naplózás legutóbb van kiértékelve.

Miután az erőforrás-szolgáltató sikeres kódot ad vissza egy Resource Manager módú kérésre, az **AuditIfNotExists** és a **DeployIfNotExists** kiértékeli, hogy szükség van-e további megfelelőségi naplózásra vagy műveletre.

Emellett a csak a kapcsolódó mezőket módosító kérelmek a kapcsolódó mezőket vizsgálják feltételeket tartalmazó szabályzatok `PATCH` `tags` szabályzatértékelését `tags` korlátozzák.

## <a name="append"></a>Append (Hozzáfűzés)

Az Append (Hozzáfűzés) segítségével további mezőket adhat hozzá a kért erőforráshoz a létrehozás vagy frissítés során. Gyakori példa az engedélyezett IP-k megadása egy tárolási erőforráshoz.

> [!IMPORTANT]
> A Hozzáfűzés nem címketulajdonságokkal való használatra szolgál. Bár a Hozzáfűzés címkéket adhat egy erőforráshoz egy létrehozási [](#modify) vagy frissítési kérés során, javasoljuk, hogy inkább a Módosítás effektust használja a címkékhez.

### <a name="append-evaluation"></a>Kiértékelés hozzáfűzése

A Hozzáfűzés kiértékeli, mielőtt az erőforrás-szolgáltató feldolgozta volna a kérést az erőforrás létrehozása vagy frissítése során. A Hozzáfűzés mezőket ad hozzá az erőforráshoz, ha teljesül **a** szabályzatszabály if feltétele. Ha a hozzáfűző hatás felülírná az eredeti kérelemben egy másik értékkel megadott értéket, akkor megtagadásként viselkedne, és elutasítná a kérelmet. Új érték meglévő tömbhöz való hozzáfűzése az alias **\[\*\]** verziójával.

Ha a hozzáfűzési hatást használó szabályzatdefiníciót egy kiértékelési ciklus részeként futtatja, nem módosítja a már létező erőforrásokat. Ehelyett nem megfelelőként jelöli meg az **if** feltételnek megfelelő összes erőforrást.

### <a name="append-properties"></a>Tulajdonságok hozzáfűzése

A hozzáfűző hatás csak részleteket **tartalmazó tömböt** tartalmaz, amelyre szükség van. Mivel **a részletek** tömbök, egyetlen **mező/érték** pár vagy többszörös is lehet. Az elfogadható [mezők listájáért](definition-structure.md#fields) tekintse meg a definícióstruktúrát.

### <a name="append-examples"></a>Példák hozzáfűzése

1. példa: Egyetlen **mező/érték** pár, amely nem aliast használ tömbértékkel a tárfiók **\[\*\]** 
 [](definition-structure.md#aliases) IP-szabályainak beállításhoz.  Ha a nem alias egy tömb, a hatás hozzáfűzi az értéket **\[\*\]** a teljes  tömbhöz. Ha a tömb már létezik, megtagadási esemény következik be az ütközésből.

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
        "value": [{
            "action": "Allow",
            "value": "134.5.0.0/21"
        }]
    }]
}
```

2. példa: Egyetlen **mező/érték** pár, amely egy tömbértékkel megadott aliast használ egy **\[\*\]** [](definition-structure.md#aliases) tárfiók IP-szabályainak beállításhoz.  Az alias használatával a hatás hozzáfűzi az értéket **\[\*\]** egy potenciálisan már létező tömbhöz.  Ha a tömb még nem létezik, létrejön.

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]",
        "value": {
            "value": "40.40.40.40",
            "action": "Allow"
        }
    }]
}
```

## <a name="audit"></a>Naplózás

A nem megfelelő erőforrások kiértékelése során a rendszer a naplózás segítségével figyelmeztető eseményt hoz létre a tevékenységnaplóban, de nem állíthatja le a kérést.

### <a name="audit-evaluation"></a>Naplózás kiértékelése

A naplózás az erőforrás Azure Policy vagy frissítése során ellenőrzött utolsó hatás. A Resource Manager esetén a Azure Policy elküldi az erőforrást az erőforrás-szolgáltatónak. A naplózás ugyanúgy működik az erőforrás-kérelmek és a kiértékelési ciklusok esetén. Új és frissített erőforrások esetén a Azure Policy hozzáad egy műveletet a tevékenységnaplóhoz, és nem megfelelőként jelöli `Microsoft.Authorization/policies/audit/action` meg az erőforrást.

### <a name="audit-properties"></a>Naplózási tulajdonságok

A Resource Manager esetén a naplózási hatás nem rendelkezik további tulajdonságokkal  a szabályzatdefiníció akkori feltételében való használathoz.

A erőforrás-szolgáltatói üzemmódja esetén a naplózási hatás a következő további `Microsoft.Kubernetes.Data` részleteket **tartalmazza:**.

- **constraintTemplate** (kötelező)
  - Az új korlátozásokat meghatározó CustomResourceDefinition (CRD) constraint sablon. A sablon meghatározza a Rego logikát, a Constraint sémát és  a Constraint paramétereket, amelyek a függvényből származó Azure Policy.
- **megkötés** (kötelező)
  - A Korlátozás sablon CRD-implementációja. A értékekkel átadott **paramétereket** `{{ .Values.<valuename> }}` használ. Az alábbi 2. példában ezek az értékek és `{{ .Values.excludedNamespaces }}` `{{ .Values.allowedContainerImagesRegex }}` .
- **értékek** (nem kötelező)
  - Meghatározza a Korlátozásnak átadni szükséges paramétereket és értékeket. Minden értéknek léteznie kell a Megkötés sablon CRD-ben.

### <a name="audit-example"></a>Példa naplózásra

1. példa: A naplózási hatás használata Resource Manager módban.

```json
"then": {
    "effect": "audit"
}
```

2. példa: A naplózási hatás használata a erőforrás-szolgáltatói `Microsoft.Kubernetes.Data` módban. A részletekben **található** további információk határozzák meg a Kubernetesben használható Korlátozás sablont és CRD-t az engedélyezett tároló-rendszerképek korlátozására.

```json
"then": {
    "effect": "audit",
    "details": {
        "constraintTemplate": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-allowed-images/template.yaml",
        "constraint": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-allowed-images/constraint.yaml",
        "values": {
            "allowedContainerImagesRegex": "[parameters('allowedContainerImagesRegex')]",
            "excludedNamespaces": "[parameters('excludedNamespaces')]"
        }
    }
}
```

## <a name="auditifnotexists"></a>AuditIfNotExists

Az AuditIfNotExists lehetővé  teszi az erőforráshoz kapcsolódó olyan erőforrások naplózását, amelyek megfelelnek az **if** feltételnek, de nem rendelkeznek az akkor feltétel részleteiben megadott **tulajdonságokkal.** 

### <a name="auditifnotexists-evaluation"></a>AuditIfNotExists-értékelés

Az AuditIfNotExists azután fut le, hogy egy erőforrás-szolgáltató kezelt egy erőforrás-létrehozási vagy -frissítési kérést, és sikeres állapotkódot adott vissza. A naplózás akkor történik, ha nincsenek kapcsolódó erőforrások, vagy ha az **ExistenceCondition** által meghatározott erőforrások kiértékelése nem igaz. Új és frissített erőforrások esetén a Azure Policy egy műveletet ad a tevékenységnaplóhoz, és nem megfelelőként jelöli `Microsoft.Authorization/policies/audit/action` meg az erőforrást. Eseményindító esetén az **if** feltételnek megfelelő erőforrás az az erőforrás, amely nem megfelelőként van megjelölve.

### <a name="auditifnotexists-properties"></a>AuditIfNotExists tulajdonságok

Az  AuditIfNotExists effektusok details (részletek) tulajdonsága tartalmazza az összes olyan altulajdonságokat, amelyek meghatározzák az egyezéshez kapcsolódó erőforrásokat.

- **Típus** (kötelező)
  - Megadja a kapcsolódó erőforrás típusát, amely megfeleltetve lesz.
  - Ha **a details.type** egy erőforrástípus az  **if** condition erőforrás alatt, a szabályzat lekérdezi az ilyen típusú erőforrásokat a kiértékelt erőforrás hatókörében. Ellenkező esetben a szabályzatlekérdezések ugyanabban az erőforráscsoportban, mint a kiértékelt erőforrás.
- **Név** (nem kötelező)
  - Megadja az erőforrás pontos nevét, és hatására a szabályzat egy adott erőforrást fog lekérni a megadott típusú összes erőforrás helyett.
  - Ha az  **if.field.type** és **a.details.type** feltételértékek egyeznek, akkor a **Név** kötelezővé válik, és gyermekerőforrás esetén , vagy `[field('name')]` `[field('fullName')]` típusúnak kell lennie.
    Ehelyett azonban [érdemes](#audit) megfontolni a naplózási hatást.
- **ResourceGroupName** (nem kötelező)
  - Lehetővé teszi, hogy a kapcsolódó erőforrás egyezése egy másik erőforráscsoportból jöjjön.
  - Nem érvényes, ha a **type** egy olyan erőforrás, amely az if condition **erőforrás** alatt lenne.
  - Az alapértelmezett érték az **if** feltétel erőforráscsoportja.
- **ExistenceScope** (nem kötelező)
  - Megengedett értékek: _Subscription (Előfizetés)_ és _ResourceGroup (Erőforráscsoport)._
  - Beállítja a hatókört, ahonnan lekéri a kapcsolódó erőforrást, hogy egyeznie kell.
  - Nem érvényes, ha a **type** egy olyan erőforrás, amely az if condition **erőforrás** alatt lenne.
  - A _ResourceGroup esetén_ a az **if** feltétel erőforráscsoportját vagy a **ResourceGroupName** paraméterben megadott erőforráscsoportot korlátozza.
  - Az _Előfizetés esetén_ a lekérdezi a kapcsolódó erőforrás teljes előfizetését.
  - Az alapértelmezett érték _a ResourceGroup._
- **ExistenceCondition** (nem kötelező)
  - Ha nincs megadva,  bármely kapcsolódó típusú erőforrás megfelel ennek a hatásnak, és nem aktiválja a naplózást.
  - Ugyanazt a nyelvet használja, mint az **if** feltétel szabályzatszabálya, de a rendszer minden kapcsolódó erőforráson külön-külön értékeli ki.
  - Ha bármely egyező kapcsolódó erőforrás igaz értéket ad vissza, a hatás teljesül, és nem aktiválja a naplózást.
  - A [field()] használatával ellenőrizheti az egyenértékűséget az **if feltételben megadott értékekkel.**
  - A segítségével például ellenőrizheti, hogy a szülőerőforrás (az **if** feltételben) ugyanazon az erőforráshelyen van-e, mint az egyező kapcsolódó erőforrás.

### <a name="auditifnotexists-example"></a>AuditIfNotExists példa

Példa: Kiértékeli Virtual Machines, hogy a Kártevőirtó bővítmény létezik-e, majd naplóz, ha hiányzik.

```json
{
    "if": {
        "field": "type",
        "equals": "Microsoft.Compute/virtualMachines"
    },
    "then": {
        "effect": "auditIfNotExists",
        "details": {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "existenceCondition": {
                "allOf": [{
                        "field": "Microsoft.Compute/virtualMachines/extensions/publisher",
                        "equals": "Microsoft.Azure.Security"
                    },
                    {
                        "field": "Microsoft.Compute/virtualMachines/extensions/type",
                        "equals": "IaaSAntimalware"
                    }
                ]
            }
        }
    }
}
```

## <a name="deny"></a>Megtagadás

A Deny (Megtagadás) művelet megakadályozza az olyan erőforrás-kérelmeket, amelyek egy szabályzatdefiníción keresztül nem határozzák meg a meghatározott szabványokat, és a kérés meghiúsul.

### <a name="deny-evaluation"></a>Megtagadás kiértékelése

Ha egyező erőforrást hoz létre vagy frissít egy Resource Manager módban, a megtagadás megakadályozza a kérést, mielőtt elküldeik az erőforrás-szolgáltatónak. A kérelem a következőként lesz `403 (Forbidden)` visszaadva: . A portálon a Tiltott állapot megtekinthető a szabályzat-hozzárendelés által megakadályozott üzemelő példány állapotaként. Erőforrás-szolgáltatói mód esetén az erőforrás-szolgáltató kezeli az erőforrás értékelését.

A meglévő erőforrások kiértékelése során a megtagadási szabályzat definíciójának megfelelő erőforrások nem megfelelőként vannak megjelölve.

### <a name="deny-properties"></a>Megtagadási tulajdonságok

A Resource Manager esetén a megtagadási hatás nem rendelkezik további tulajdonságokkal a szabályzatdefiníció **then** feltételében való használathoz.

A erőforrás-szolgáltatói üzemmódja esetén a megtagadási hatás a következő további `Microsoft.Kubernetes.Data` részleteket **tartalmazza:**.

- **constraintTemplate** (kötelező)
  - Az új korlátozásokat meghatározó CustomResourceDefinition (CRD) constraint sablon. A sablon meghatározza a Rego logikát, a Constraint sémát és  a Constraint paramétereket, amelyek a függvényből származó Azure Policy.
- **megkötés** (kötelező)
  - A Megkötés sablon CRD-implementációja. Értékeken keresztül átadott **paramétereket** `{{ .Values.<valuename> }}` használ. Az alábbi 2. példában ezek az értékek és `{{ .Values.excludedNamespaces }}` `{{ .Values.allowedContainerImagesRegex }}` .
- **értékek** (nem kötelező)
  - Meghatározza a Korlátozásnak átadni szükséges paramétereket és értékeket. Minden értéknek léteznie kell a Constraint sablon CRD-ében.

### <a name="deny-example"></a>Megtagadási példa

1. példa: A megtagadási hatás használata Resource Manager módban.

```json
"then": {
    "effect": "deny"
}
```

2. példa: A megtagadási hatás használata a erőforrás-szolgáltatói `Microsoft.Kubernetes.Data` módban. A részletekben található további **információk** határozzák meg a Kubernetesben használni kívánt Korlátozás sablont és CRD-t az engedélyezett tároló-rendszerképek korlátozására.

```json
"then": {
    "effect": "deny",
    "details": {
        "constraintTemplate": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-allowed-images/template.yaml",
        "constraint": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-allowed-images/constraint.yaml",
        "values": {
            "allowedContainerImagesRegex": "[parameters('allowedContainerImagesRegex')]",
            "excludedNamespaces": "[parameters('excludedNamespaces')]"
        }
    }
}
```

## <a name="deployifnotexists"></a>DeployIfNotExists

Az AuditIfNotExists-hez hasonlóan a DeployIfNotExists szabályzatdefiníció is végrehajtja a sablon üzembe helyezését, ha a feltétel teljesül.

> [!NOTE]
> [A beágyazott sablonokat](../../../azure-resource-manager/templates/linked-templates.md#nested-template) a **deployIfNotExists** támogatja, [a](../../../azure-resource-manager/templates/linked-templates.md#linked-template) csatolt sablonok azonban jelenleg nem.

### <a name="deployifnotexists-evaluation"></a>DeployIfNotExists kiértékelés

A DeployIfNotExists körülbelül 15 perccel azután fut le, hogy egy erőforrás-szolgáltató egy létrehozási vagy frissítési előfizetést vagy erőforrás-kérést kezelt, és sikerállapotkódot adott vissza. A sablon üzembe helyezése akkor történik, ha nincsenek kapcsolódó erőforrások, vagy ha az **ExistenceCondition** által meghatározott erőforrások kiértékelése nem igaz. Az üzembe helyezés időtartama a sablonban foglalt erőforrások összetettségétől függ.

A kiértékelési ciklus során az erőforrásoknak megfelelő DeployIfNotExists effektusú szabályzatdefiníciók nem megfelelőként vannak megjelölve, de az adott erőforráson nem kell műveletet tenni. A meglévő nem megfelelő erőforrások szervizelési feladattal [szervizelési feladattal orvosolhatóak.](../how-to/remediate-resources.md)

### <a name="deployifnotexists-properties"></a>DeployIfNotExists tulajdonságok

A  DeployIfNotExists hatás details tulajdonsága tartalmazza az összes olyan altulajdonságokat, amelyek meghatározzák az egyezni kívánt kapcsolódó erőforrásokat és a végrehajtani kívánt sablontelepítést.

- **Típus** (kötelező)
  - Megadja a kapcsolódó erőforrás típusát, amely megfeleltetve lesz.
  - Elsőként megpróbál lekérni egy erőforrást az **if** feltétel erőforrása alatt, majd az if condition erőforrással azonos **erőforráscsoporton** belül lekérdezéseket ad vissza.
- **Név** (nem kötelező)
  - Megadja az erőforrás pontos egyezését, és hatására a szabályzat egy adott erőforrást fog lekérni a megadott típusú összes erőforrás helyett.
  - Ha az  **if.field.type** és a **then.details.type** feltételértékek egyeznek, akkor a **Név** kötelezővé válik, és a gyermekerőforráshoz , vagy `[field('name')]` `[field('fullName')]` típusúnak kell lennie.
- **ResourceGroupName** (nem kötelező)
  - Lehetővé teszi, hogy a kapcsolódó erőforrás egyezése egy másik erőforráscsoportból jöjjön.
  - Nem érvényes, ha a **type** egy olyan erőforrás, amely az if condition **erőforrás** alatt található.
  - Az alapértelmezett érték az **if** feltétel erőforráscsoportja.
  - Ha egy sablon üzembe helyezése végre van hajtva, az ennek az értéknek az erőforráscsoportban lesz telepítve.
- **ExistenceScope** (nem kötelező)
  - Az engedélyezett értékek a _Subscription (Előfizetés)_ és _a ResourceGroup (Erőforráscsoport)._
  - Beállítja annak a hatókörnek a hatókörét, ahonnan a kapcsolódó erőforrást lekéri az egyezéshez.
  - Nem érvényes, ha a **type** egy olyan erőforrás, amely az if condition **erőforrás** alatt található.
  - A _ResourceGroup esetén_ a az if **feltétel** erőforráscsoportját vagy a **ResourceGroupName** paraméterben megadott erőforráscsoportot korlátozza.
  - Az _Előfizetés esetén_ a lekérdezi a kapcsolódó erőforrás teljes előfizetését.
  - Az alapértelmezett érték _a ResourceGroup._
- **ExistenceCondition** (nem kötelező)
  - Ha nincs megadva,  bármely kapcsolódó típusú erőforrás megfelel ennek a hatásnak, és nem aktiválja az üzembe helyezést.
  - Ugyanazt a nyelvet használja, mint az **if** feltétel szabályzatszabálya, de a rendszer minden kapcsolódó erőforráson külön-külön értékeli ki.
  - Ha bármely egyező kapcsolódó erőforrás igaz értéket ad vissza, a hatás teljesül, és nem aktiválja az üzembe helyezést.
  - A [field()] használatával ellenőrizheti az egyenértékűséget az **if feltételben megadott értékekkel.**
  - A segítségével például ellenőrizheti, hogy a szülőerőforrás (az **if** feltételben) ugyanazon az erőforráshelyen van-e, mint a megfelelő kapcsolódó erőforrás.
- **roleDefinitionIds** (kötelező)
  - Ennek a tulajdonságnak tartalmaznia kell egy sztringtömböt, amely megfelel az előfizetés által elérhető szerepköralapú hozzáférés-vezérlési szerepkör-azonosítónak. További információ: [Szervizelés – szabályzatdefiníció konfigurálása.](../how-to/remediate-resources.md#configure-policy-definition)
- **DeploymentScope** (nem kötelező)
  - Az engedélyezett értékek a _subscription (Előfizetés)_ és _a ResourceGroup (Erőforráscsoport)._
  - Beállítja az aktiválni szükséges központi telepítés típusát. _Az_ Előfizetés előfizetési [szinten való üzembe helyezést](../../../azure-resource-manager/templates/deploy-to-subscription.md)jelez, a _ResourceGroup_ erőforráscsoportba való telepítést jelez.
  - _Előfizetési_ szintű üzemelő  példányok használata esetén meg kell adni egy hely tulajdonságot az Üzemelő példányban.
  - Az alapértelmezett érték _a ResourceGroup._
- **Üzembe helyezés** (kötelező)
  - Ennek a tulajdonságnak tartalmaznia kell a sablon teljes üzembe helyezését, ahogy azt a `Microsoft.Resources/deployments` PUT API-nak is át kellene adnunk. További információ: [Központi telepítések REST API.](/rest/api/resources/deployments)

  > [!NOTE]
  > A Deployment (Üzembe **helyezés)** tulajdonságon belüli összes függvényt a rendszer a sablon, és nem a szabályzat összetevőjeként értékeli ki. Ez alól kivétel a **parameters** tulajdonság, amely értékeket ad át a szabályzatból a sablonnak. Az **érték** átadása a sablonparaméter neve alatt ebben a szakaszban található értékkel lesz végrehajtva (lásd a DeployIfNotExists példában a _fullDbName_ részt).

### <a name="deployifnotexists-example"></a>DeployIfNotExists példa

Példa: Kiértékeli SQL Server, hogy a transparentDataEncryption engedélyezve van-e. Ha nem, akkor az engedélyezni szükséges üzembe helyezés lesz végrehajtva.

```json
"if": {
    "field": "type",
    "equals": "Microsoft.Sql/servers/databases"
},
"then": {
    "effect": "DeployIfNotExists",
    "details": {
        "type": "Microsoft.Sql/servers/databases/transparentDataEncryption",
        "name": "current",
        "roleDefinitionIds": [
            "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
            "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
        ],
        "existenceCondition": {
            "field": "Microsoft.Sql/transparentDataEncryption.status",
            "equals": "Enabled"
        },
        "deployment": {
            "properties": {
                "mode": "incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {
                        "fullDbName": {
                            "type": "string"
                        }
                    },
                    "resources": [{
                        "name": "[concat(parameters('fullDbName'), '/current')]",
                        "type": "Microsoft.Sql/servers/databases/transparentDataEncryption",
                        "apiVersion": "2014-04-01",
                        "properties": {
                            "status": "Enabled"
                        }
                    }]
                },
                "parameters": {
                    "fullDbName": {
                        "value": "[field('fullName')]"
                    }
                }
            }
        }
    }
}
```

## <a name="disabled"></a>Disabled (Letiltva)

Ez a hatás tesztelési helyzetekben vagy olyan esetekben hasznos, amikor a szabályzatdefiníció paraméteresre ták a hatást. Ez a rugalmasság lehetővé teszi egyetlen hozzárendelés letiltását a szabályzat összes hozzárendelésének letiltása helyett.

A Disabled hatás alternatívája a **enforcementMode**, amely a szabályzat-hozzárendeléshez van beállítva.
Ha **a enforcementMode** _le van tiltva,_ a rendszer továbbra is kiértékeli az erőforrásokat. A naplózás, például a tevékenységnaplók és a szabályzat hatása nem történik meg. További információ: [szabályzat-hozzárendelés – kényszerítési mód.](./assignment-structure.md#enforcement-mode)

## <a name="enforceopaconstraint"></a>AzOPAConstraint kényszerítés

Ez a hatás a szabályzatdefiníciós _módban_ `Microsoft.Kubernetes.Data` használatos. A forgalomirányító v3 belépésvezérlési szabályainak [](https://github.com/open-policy-agent/frameworks/tree/master/constraint#opa-constraint-framework) OPA Constraint Framework az [Open Policy Agent](https://www.openpolicyagent.org/) (OPA) számára való áthaladás az Azure-beli Kubernetes-fürtöknek.

> [!IMPORTANT]
> A Korlátozott előzetes verziójú szabályzatdefiníciók **EnforceOPAConstraint** effektussal és a kapcsolódó **Kubernetes** Service-kategóriával _elavultak._ Ehelyett használja a _hatásvizsgálat és a megtagadás_ _hatását_ az Erőforrás-szolgáltató `Microsoft.Kubernetes.Data` módban.

### <a name="enforceopaconstraint-evaluation"></a>AzOPAConstraint kiértékelése kényszerítése

Az Open Policy Agent belépésvezérlő valós időben értékeli ki a fürtön található új kéréseket.
15 percenként befejeződik a fürt teljes vizsgálatának befejezése, és az eredményekről jelentést Azure Policy.

### <a name="enforceopaconstraint-properties"></a>AzOPAConstraint tulajdonságainak kényszerítés

A  EnforceOPAConstraint hatás details tulajdonsága tartalmazza a Gatekeeper v3 belépésvezérlési szabályt leíró altulajdonságokat.

- **constraintTemplate** (kötelező)
  - Az új korlátozásokat meghatározó CustomResourceDefinition (CRD) constraint sablon. A sablon meghatározza a Rego logikát, a Constraint sémát és  a Constraint paramétereket, amelyek a függvényből származó Azure Policy.
- **megkötés** (kötelező)
  - A Megkötés sablon CRD-implementációja. Értékeken keresztül átadott **paramétereket** `{{ .Values.<valuename> }}` használ. Az alábbi példában ezek az értékek és `{{ .Values.cpuLimit }}` `{{ .Values.memoryLimit }}` .
- **értékek** (nem kötelező)
  - Meghatározza a Korlátozásnak átadni szükséges paramétereket és értékeket. Minden értéknek léteznie kell a megkötési sablon CRD-ében.

### <a name="enforceopaconstraint-example"></a>Példa azOPAConstraint kényszerítésre

Példa: Gatekeeper v3 belépésvezérlési szabály a tárolók CPU- és memória-erőforráskorlátának beállításhoz a Kubernetesben.

```json
"if": {
    "allOf": [
        {
            "field": "type",
            "in": [
                "Microsoft.ContainerService/managedClusters",
                "AKS Engine"
            ]
        },
        {
            "field": "location",
            "equals": "westus2"
        }
    ]
},
"then": {
    "effect": "enforceOPAConstraint",
    "details": {
        "constraintTemplate": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-resource-limits/template.yaml",
        "constraint": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-resource-limits/constraint.yaml",
        "values": {
            "cpuLimit": "[parameters('cpuLimit')]",
            "memoryLimit": "[parameters('memoryLimit')]"
        }
    }
}
```

## <a name="enforceregopolicy"></a>EnforceRegoPolicy

Ez a hatás a szabályzatdefiníciós _módban_ `Microsoft.ContainerService.Data` használatos. Ez a [Rego](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego) által meghatározott Gatekeeper v2 belépésvezérlési szabályokon ad át OPA-t a [Azure Kubernetes Service.](../../../aks/intro-kubernetes.md) [](https://www.openpolicyagent.org/)

> [!IMPORTANT]
> Az **EnforceRegoPolicy** effektussal és a kapcsolódó **Kubernetes** Service-kategóriával kapcsolatos korlátozott előzetes verziójú szabályzatdefiníciók _elavultak._ Ehelyett használja a hatásvizsgálat _és a_ _megtagadás erőforrás-szolgáltató_ módban való `Microsoft.Kubernetes.Data` használatát.

### <a name="enforceregopolicy-evaluation"></a>EnforceRegoPolicy kiértékelése

Az Open Policy Agent belépésvezérlő valós időben értékeli ki a fürtön található új kéréseket.
15 percenként befejeződik a fürt teljes vizsgálatának befejezése, és az eredmények jelentése a Azure Policy.

### <a name="enforceregopolicy-properties"></a>EnforceRegoPolicy tulajdonságok

Az  EnforceRegoPolicy hatás details tulajdonsága tartalmazza a Gatekeeper v2 belépésvezérlési szabályát leíró altulajdonságokat.

- **policyId** (kötelező)
  - A Rego belépésvezérlési szabályának paraméterként átadott egyedi név.
- **szabályzat** (kötelező)
  - Megadja a Rego belépésvezérlési szabályának URI-ját.
- **policyParameters** (nem kötelező)
  - Meghatározza a rego-szabályzatnak átadni szükséges paramétereket és értékeket.

### <a name="enforceregopolicy-example"></a>EnforceRegoPolicy példa

Példa: Gatekeeper v2 belépésvezérlési szabály, amely csak a megadott tároló rendszerképeket engedélyezi az AKS-ben.

```json
"if": {
    "allOf": [
        {
            "field": "type",
            "equals": "Microsoft.ContainerService/managedClusters"
        },
        {
            "field": "location",
            "equals": "westus2"
        }
    ]
},
"then": {
    "effect": "EnforceRegoPolicy",
    "details": {
        "policyId": "ContainerAllowedImages",
        "policy": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/KubernetesService/container-allowed-images/limited-preview/gatekeeperpolicy.rego",
        "policyParameters": {
            "allowedContainerImagesRegex": "[parameters('allowedContainerImagesRegex')]"
        }
    }
}
```

## <a name="modify"></a>Módosítás

A Modify használatával tulajdonságokat vagy címkéket adhat hozzá, frissítheti vagy távolíthat el egy előfizetésen vagy erőforráson a létrehozás vagy frissítés során. Gyakori példa az erőforrások( például a costCenter) címkéinek frissítése. A meglévő nem megfelelő erőforrások szervizelési feladattal [szervizelési feladattal orvosolhatóak.](../how-to/remediate-resources.md) Egyetlen módosítási szabálynak bármilyen számú művelete lehet.

A Modify a következő műveleteket támogatja:

- Erőforráscímkék hozzáadása, cseréje vagy eltávolítása. Címkék esetén a Módosítás szabályzatnak `mode` _Indexeltre kell állítania,_ kivéve, ha a célerőforrás egy erőforráscsoport.
- Adja hozzá vagy cserélje le a virtuális gépek és virtuálisgép-méretezési készletek felügyelt `identity.type` identitástípusának () értékét.
- Adja hozzá vagy cserélje le bizonyos aliasok értékeit (előzetes verzió).
  - A `Get-AzPolicyAlias | Select-Object -ExpandProperty 'Aliases' | Where-Object { $_.DefaultMetadata.Attributes -eq 'Modifiable' }` használata
    a Azure PowerShell **4.6.0-s** vagy újabb verzióban a Módosítással használható aliasok listájának lekért listájáért.

> [!IMPORTANT]
> Ha címkéket használ, javasoljuk, hogy a Hozzáfűzés helyett használja a Módosítás lehetőséget, mivel a Módosítás további művelettípusokat és a meglévő erőforrások szervizlének képességét biztosítja. Ha azonban nem tud felügyelt identitást létrehozni, vagy a Módosítás még nem támogatja az aliast az erőforrás-tulajdonsághoz, a Hozzáfűzés használata javasolt.

### <a name="modify-evaluation"></a>Értékelés módosítása

A Modify függvény kiértékeli, mielőtt egy erőforrás-szolgáltató feldolgozta volna a kérést az erőforrás létrehozása vagy frissítése során. A Módosítás művelet akkor lesz alkalmazva a kérés tartalmára, amikor teljesül **a** szabályzatszabály if feltétele. Minden Módosítás művelet meghatározhat egy feltételt, amely meghatározza annak alkalmazását. A rendszer kihagyja a hamisra kiértékelt feltételekkel _kapcsolatos_ műveleteket.

Ha alias van megadva, a rendszer a következő további ellenőrzéseket végzi el annak érdekében, hogy a Módosítás művelet ne változtassa meg a kérés tartalmát úgy, hogy az erőforrás-szolgáltató visszautasítsa azt:

- Az alias leképezési tulajdonsága módosíthatóként van megjelölve a kérés API-verziójában.
- A Módosítás művelet jogkivonattípusa megegyezik a kérés API-verziójában található tulajdonság várt jogkivonattípusával.

Ha ezen ellenőrzések bármelyike sikertelen, a szabályzat kiértékelése a megadott **conflictEffect (ütközések) értékre áll vissza.**

> [!IMPORTANT]
> Az aliasokat is magukban foglaló definíciók módosítása a   naplózási ütközési hatást használja a sikertelen kérések elkerülésére olyan API-verziók használatával, amelyekben a leképezett tulajdonság nem módosítható. Ha ugyanaz az alias eltérően viselkedik az API-verziók között, feltételes módosítási műveletekkel határozható meg az egyes API-verziókhoz használt módosítási művelet.

Ha a Módosítás effektust használó szabályzatdefiníció egy kiértékelési ciklus részeként fut, nem módosítja a már létező erőforrásokat. Ehelyett nem megfelelőként jelöli meg az **if** feltételnek megfelelő összes erőforrást.

### <a name="modify-properties"></a>Tulajdonságok módosítása

A **Modify** (Módosítás) hatás details (részletek) tulajdonsága tartalmazza az összes olyan  altulajdonságokat, amelyek meghatározzák a szervizeléshez szükséges engedélyeket, valamint a címkeértékek hozzáadásához, frissítéséhez vagy eltávolításához használt műveleteket.

- **roleDefinitionIds** (kötelező)
  - Ennek a tulajdonságnak tartalmaznia kell egy sztringtömböt, amely megfelel az előfizetés által elérhető szerepköralapú hozzáférés-vezérlési szerepkör-azonosítónak. További információ: [Szervizelés – szabályzatdefiníció konfigurálása.](../how-to/remediate-resources.md#configure-policy-definition)
  - A definiált szerepkörnek tartalmaznia kell a Közreműködő szerepkörhöz megadott összes [műveletet.](../../../role-based-access-control/built-in-roles.md#contributor)
- **conflictEffect (nem** kötelező)
  - Meghatározza, hogy melyik szabályzatdefiníció "nyer", ha egynél több szabályzatdefiníció módosítja ugyanazt a tulajdonságot, vagy ha a Módosítás művelet nem működik a megadott aliason.
    - Új vagy frissített erőforrások esetén a megtagadásos _szabályzatdefiníció_ élvez elsőbbséget. A naplózási _szabályzatdefiníciók kihagynak_ **minden műveletet.** Ha egynél több szabályzatdefiníció _megtagadja_ a kérést, a rendszer ütközésként megtagadja a kérést. Ha az összes szabályzatdefiníció _naplót végez,_ akkor az ütköző szabályzatdefiníciók egyik művelete sem lesz feldolgozva. 
    - Meglévő erőforrások esetén, ha egynél több szabályzatdefiníció _megtagadja_ a et, a megfelelőségi állapot _Ütközés._ Ha egy vagy kevesebb szabályzatdefiníció esetében megtagadás _van,_ minden hozzárendelés nem megfelelő megfelelőségi _állapotot ad vissza._
  - Elérhető értékek: _audit_, _deny_, _disabled_.
  - Az alapértelmezett érték a deny ( _megtagadás)._
- **műveletek** (kötelező)
  - Az egyező erőforrásokon végrehajtani szükséges címkeműveleteket tartalmazó tömb.
  - Tulajdonságok:
    - **művelet** (kötelező)
      - Meghatározza, hogy milyen műveletet kell egyező erőforráson eltenni. A lehetőségek aek: _addOrReplace,_ _Add_, _Remove_. _A Hozzáadás_ a Hozzáfűzés effektushoz [hasonlóan viselkedik.](#append)
    - **mező** (kötelező)
      - A hozzáadni, lecserélni vagy eltávolítani a címkét. A címkeneveknek meg kell felelniük más mezők elnevezési [konvencióinak.](./definition-structure.md#fields)
    - **érték** (nem kötelező)
      - A címke beállításának értéke.
      - Erre a tulajdonságra akkor van **szükség, ha a** művelet _az addOrReplace_ vagy _az Add_.
    - **feltétel** (nem kötelező)
      - Egy olyan sztring, Azure Policy a [Policy](./definition-structure.md#policy-functions) függvényekkel, amelyek kiértékelése _igaz_ vagy hamis értéket _ad vissza._
      - Nem támogatja a következő Policy-függvényeket: `field()` , `resourceGroup()` , `subscription()` .

### <a name="modify-operations"></a>Műveletek módosítása

A **műveleti tulajdonságtömb** lehetővé teszi több címke különböző módon való megváltoztatását egyetlen szabályzatdefiníciótól. Minden művelet **műveletből,** **mezőből és** **értéktulajdonságokból** áll. A művelet határozza meg, hogy a szervizelési feladat mit tesz a címkékkel, a mező határozza meg, hogy melyik címke módosult, az érték pedig a címke új beállítását. Az alábbi példa a következő címkeváltozásokat követi:

- A címkét "Teszt" értékre állítja, még akkor is, ha már létezik `environment` egy másik értékkel.
- Eltávolítja a `TempResource` címkét.
- A `Dept` címkét a _szabályzat-hozzárendeléshez konfigurált DeptName_ szabályzatparaméterre állítja be.

```json
"details": {
    ...
    "operations": [
        {
            "operation": "addOrReplace",
            "field": "tags['environment']",
            "value": "Test"
        },
        {
            "operation": "Remove",
            "field": "tags['TempResource']",
        },
        {
            "operation": "addOrReplace",
            "field": "tags['Dept']",
            "value": "[parameters('DeptName')]"
        }
    ]
}
```

A **művelet tulajdonság** a következő beállításokkal rendelkezik:

|Művelet |Description |
|-|-|
|addOrReplace |Hozzáadja a definiált tulajdonságot vagy címkét és értéket az erőforráshoz, még akkor is, ha a tulajdonság vagy címke már létezik egy másik értékkel. |
|Hozzáadás |Hozzáadja a definiált tulajdonságot vagy címkét és értéket az erőforráshoz. |
|Eltávolítás |Eltávolítja a definiált tulajdonságot vagy címkét az erőforrásból. |

### <a name="modify-examples"></a>Példák módosítása

1. példa: Adja hozzá `environment` a címkét, és cserélje le a meglévő `environment` címkéket a "Test" névre:

```json
"then": {
    "effect": "modify",
    "details": {
        "roleDefinitionIds": [
            "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
        ],
        "operations": [
            {
                "operation": "addOrReplace",
                "field": "tags['environment']",
                "value": "Test"
            }
        ]
    }
}
```

2. példa: Távolítsa el a címkét, és adja hozzá a címkét, vagy cserélje le a meglévő címkéket `env` `environment` egy `environment` paraméteres értékre:

```json
"then": {
    "effect": "modify",
    "details": {
        "roleDefinitionIds": [
            "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
        ],
        "conflictEffect": "deny",
        "operations": [
            {
                "operation": "Remove",
                "field": "tags['env']"
            },
            {
                "operation": "addOrReplace",
                "field": "tags['environment']",
                "value": "[parameters('tagValue')]"
            }
        ]
    }
}
```

3. példa: Győződjön meg arról, hogy a tárfiók nem engedélyezi a blobok nyilvános hozzáférését, a Módosítás művelet csak akkor lesz alkalmazva, ha a 2019-04-01-es vagy azzal egyenlő API-verziójú kérelmeket értékeli ki:

```json
"then": {
    "effect": "modify",
    "details": {
        "roleDefinitionIds": [
            "/providers/microsoft.authorization/roleDefinitions/17d1049b-9a84-46fb-8f53-869881c3d3ab"
        ],
        "conflictEffect": "audit",
        "operations": [
            {
                "condition": "[greaterOrEquals(requestContext().apiVersion, '2019-04-01')]",
                "operation": "addOrReplace",
                "field": "Microsoft.Storage/storageAccounts/allowBlobPublicAccess",
                "value": false
            }
        ]
    }
}
```

## <a name="layering-policy-definitions"></a>Rétegezés szabályzatdefiníciói

Egy erőforrásra több hozzárendelés is hatással lehet. Ezek a hozzárendelések lehetnek azonos hatókörben vagy különböző hatókörökben. Ezen hozzárendelések mindegyikének valószínűleg más hatása lesz. Az egyes szabályzatok feltételének és hatásának kiértékelése egymástól függetlenül történik. Például:

- 1. szabályzat
  - Az erőforrás helyét a "westus" régióra korlátozza
  - Hozzárendelve az A előfizetéshez
  - Megtagadás hatás
- 2. szabályzat
  - Az erőforrás helyét az "eastus" régióra korlátozza
  - Hozzárendelve a B erőforráscsoporthoz az A előfizetésben
  - Naplózási hatás
  
Ez a beállítás a következő eredményt eredményezné:

- Az "eastus" "B" erőforráscsoportban már található bármely erőforrás megfelel a 2. szabályzatnak, és nem felel meg az 1. szabályzatnak
- Minden olyan erőforrás, amely a B erőforráscsoportban nem az "eastus" erőforráscsoportban található, nem felel meg a 2. szabályzatnak, és nem felel meg az 1. szabályzatnak, ha nem a "westus"
- Az 1. szabályzat megtagad minden olyan új erőforrást az A előfizetésben, amely nem a "westus" régióhoz található
- Minden új erőforrás létrejön az A előfizetésben és a B erőforráscsoportban a westusban, és nem felel meg a 2. szabályzatnak

Ha az 1. és a 2. szabályzat is megtagadja a szabályzatot, a helyzet a következőre változik:

- A B erőforráscsoportban már található, az "eastus" régión kívüli bármely erőforrás nem felel meg a 2. szabályzatnak
- A B erőforráscsoportban nem a westusban található bármely erőforrás nem felel meg az 1. szabályzatnak
- Az 1. szabályzat minden olyan új erőforrást elutasít az A előfizetésben, amely nem a "westus" régión található
- Az A előfizetés B erőforráscsoportja minden új erőforrása le van tiltva

Minden hozzárendelés külön van kiértékelve. Így nincs lehetőség arra, hogy egy erőforrás a hatókörbeli különbségektől való eltérésen keresztül ússzon át. A rétegző szabályzatdefiníciók nettó eredménye kumulatív **legkorlátozóbbnak számít.** Ha például az 1. és a 2. szabályzat is megtagadja a szabályzatot, az erőforrásokat az átfedésben lévő és ütköző szabályzatdefiníciók blokkolják. Ha továbbra is létre kell hoznunk az erőforrást a célhatókörben, tekintse át az egyes hozzárendelések kivételeit annak ellenőrzéshez, hogy a megfelelő szabályzat-hozzárendelések hatással vannak-e a megfelelő hatókörökre.

## <a name="next-steps"></a>Következő lépések

- Tekintse át a példákat [a következő Azure Policy:](../samples/index.md).
- Tekintse meg az [Azure szabályzatdefiníciók struktúrája](definition-structure.md) szakaszt.
- A szabályzatok [programozott módon történő létrehozása.](../how-to/programmatically-create.md)
- Megtudhatja, hogyan [olvashatja be a megfelelőségi adatokat.](../how-to/get-compliance-data.md)
- Ismerje meg, [hogyan szervizelődnek a nem megfelelő erőforrások.](../how-to/remediate-resources.md)
- Tekintse át a felügyeleti csoportokat az [Erőforrások rendszerezése Azure-beli felügyeleti csoportokkal való használatával.](../../management-groups/overview.md)
