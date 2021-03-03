---
title: A hatások működésének megismerése
description: Azure Policy definíciók különböző effektusokkal rendelkeznek, amelyek meghatározzák a megfelelőség felügyeletének és jelentésének módját.
ms.date: 02/17/2021
ms.topic: conceptual
ms.openlocfilehash: 67445b3d0d63b3827f82822de00412bdab67c5ab
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101741820"
---
# <a name="understand-azure-policy-effects"></a>Azure Policy effektusok ismertetése

Az Azure Policyban minden egyes szabályzatdefiníció egyetlen hatással bír. Ez a hatás határozza meg, hogy mi történik, ha a házirend-szabály kiértékelése megfelel a feltételeknek. A hatások másképp viselkednek, ha egy új erőforráshoz, egy frissített erőforráshoz vagy egy meglévő erőforráshoz tartoznak.

Ezek a hatások jelenleg a szabályzatok definíciójában támogatottak:

- [Append](#append)
- [Audit](#audit)
- [AuditIfNotExists](#auditifnotexists)
- [Deny](#deny)
- [DeployIfNotExists](#deployifnotexists)
- [Disabled](#disabled)
- [Modify](#modify)

A következő hatások _elavultak_:

- [EnforceOPAConstraint](#enforceopaconstraint)
- [EnforceRegoPolicy](#enforceregopolicy)

> [!IMPORTANT]
> Az **EnforceOPAConstraint** -vagy **EnforceRegoPolicy** -effektusok helyett használja az _audit_ és a _Megtagadás_ erőforrás-szolgáltatói módot `Microsoft.Kubernetes.Data` . A beépített szabályzat-definíciók frissítve lettek. Ha a beépített szabályzat-definíciók meglévő szabályzat-hozzárendelései módosulnak, az _Effect_ paramétert a frissített _allowedValues_ listán szereplő értékre kell módosítani.

## <a name="order-of-evaluation"></a>Kiértékelési sorrend

Az erőforrások létrehozására vagy frissítésére vonatkozó kérelmeket a Azure Policy először kiértékeli. Azure Policy létrehoz egy listát az erőforrásra vonatkozó összes hozzárendelésről, majd kiértékeli az erőforrást az egyes definíciók alapján. A [Resource Manager üzemmód](./definition-structure.md#resource-manager-modes)esetében Azure Policy a hatások közül többen dolgozza fel a kérést a megfelelő erőforrás-szolgáltatóba. Ez a megrendelés megakadályozza az erőforrás-szolgáltató általi szükségtelen feldolgozást, ha az erőforrás nem teljesíti a Azure Policy tervezett irányítási vezérlőit. Erőforrás- [szolgáltatói módban](./definition-structure.md#resource-provider-modes)az erőforrás-szolgáltató kezeli a kiértékelést és az eredményt, és visszaküldi az eredményeket a Azure Policynak.

- A **Letiltva** érték megadásával megállapíthatja, hogy ki kell-e értékelni a házirend-szabályt.
- Ezután kiértékeli a **hozzáfűzést** és a **módosítást** . Mivel bármelyik megváltoztathatja a kérést, a módosítás miatt előfordulhat, hogy egy naplózási vagy megtagadási hatás megakadályozza az indítást. Ezek a hatások csak Resource Manager módban érhetők el.
- A **Megtagadás** ekkor kiértékelésre kerül. A Megtagadás kiértékelésével a nem kívánt erőforrások kettős naplózása megakadályozható.
- A rendszer a **naplózást** utoljára értékeli.

Miután az erőforrás-szolgáltató egy sikeres sikerességi kódot ad vissza egy Resource Manager módú kérelemben, a **AuditIfNotExists** és a **DeployIfNotExists** kiértékelésével megállapíthatja, hogy szükséges-e további megfelelőségi naplózás vagy művelet.

Emellett a `PATCH` csak a kapcsolódó mezők módosítására irányuló kérések `tags` korlátozzák a házirendek értékelését olyan házirendekre, amelyek a `tags` kapcsolódó mezőkkel kapcsolatos feltételeket tartalmaznak.

## <a name="append"></a>Append (Hozzáfűzés)

A Hozzáfűzés használatával további mezőket adhat hozzá a kért erőforráshoz a létrehozás vagy a frissítés során. Gyakori példa egy tárolási erőforrás engedélyezett IP-címeinek megadására.

> [!IMPORTANT]
> A Hozzáfűzés a nem címkével ellátott tulajdonságokkal való használatra készült. Míg a hozzáfűzéssel hozzáadhat címkéket egy erőforráshoz egy létrehozási vagy frissítési kérelem során, azt javasoljuk, hogy használja inkább a címkék [módosítási](#modify) effektusát.

### <a name="append-evaluation"></a>Kiértékelés hozzáfűzése

A kiértékelések hozzáfűzésével a rendszer az erőforrás létrehozásakor vagy frissítésekor egy erőforrás-szolgáltató dolgozza fel a kérést. A Hozzáfűzés mezők hozzáadása az erőforráshoz, **Ha a házirend-szabály feltételei** teljesülnek. Ha a hozzáfűzési effektus felülbírálja az eredeti kérelemben szereplő értéket eltérő értékkel, akkor megtagadási hatásként viselkedik, és elutasítja a kérelmet. Új érték meglévő tömbhöz való hozzáfűzéséhez használja az **\[\*\]** alias verzióját.

Ha a hozzáfűzési effektust használó házirend-definíció egy értékelési ciklus részeként fut, akkor nem módosítja a már létező erőforrásokat. Ehelyett olyan erőforrást jelöl, amely megfelel az **IF** feltétel nem megfelelőnek.

### <a name="append-properties"></a>Hozzáfűzési tulajdonságok

A hozzáfűzési effektushoz csak a **részletek** tömbje szükséges. A **részletek** egy tömb, amely egyetlen **mező/érték** párokat vagy többszöröseket is igénybe vehet. Tekintse meg az elfogadható mezők listáját a [definíciós struktúrában](definition-structure.md#fields) .

### <a name="append-examples"></a>Példák hozzáfűzése

1. példa: egyetlen **mező/érték** pár, amely egy **\[\*\]** tömb **értékkel** nem rendelkező [aliast](definition-structure.md#aliases) használ az IP-szabályok tárolási fiókra való beállításához. Ha a nem **\[\*\]** alias egy tömb, a hatás a teljes tömbként hozzáfűzi az **értéket** . Ha a tömb már létezik, megtagadási esemény következik be az ütközésből.

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

2. példa: egyetlen **mező/érték** pár, amely egy **\[\*\]** tömb **értékkel** rendelkező [aliast](definition-structure.md#aliases) használ az IP-szabályok tárolási fiókra való beállításához. Az alias használatával **\[\*\]** a hatás hozzáfűzi az **értéket** egy potenciálisan előre meglévő tömbhöz. Ha a tömb még nem létezik, akkor létrejön.

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

A naplózás egy figyelmeztetési esemény létrehozására szolgál a tevékenység naplójában a nem megfelelő erőforrások kiértékelése során, de nem állítja le a kérést.

### <a name="audit-evaluation"></a>Naplózás kiértékelése

Az erőforrás létrehozása vagy frissítése során az Azure Policy által ellenőrzött legutóbb végrehajtott ellenőrzés. Resource Manager-üzemmód esetén a Azure Policy ezt követően elküldi az erőforrást az erőforrás-szolgáltatónak. A naplózás ugyanúgy működik, mint egy erőforrás-kérelem és egy értékelési ciklus. Az új és frissített erőforrások esetében Azure Policy egy `Microsoft.Authorization/policies/audit/action` műveletet hoz létre a tevékenység naplójába, és nem megfelelőként jelöli meg az erőforrást.

### <a name="audit-properties"></a>Naplózási tulajdonságok

Resource Manager üzemmód esetén a naplózási effektus nem rendelkezik további tulajdonságokkal a házirend-definíció **feltételében** .

Erőforrás-szolgáltatói mód esetén `Microsoft.Kubernetes.Data` a naplózási effektus a **részletek** további altulajdonságaival rendelkezik.

- **constraintTemplate** (kötelező)
  - A korlátozási sablon CustomResourceDefinition (CRD), amely új korlátozásokat határoz meg. A sablon meghatározza a Rego logikát, a megkötési sémát és a Azure Policy **értékeit** használó megkötési paramétereket.
- **korlátozás** (kötelező)
  - A korlátozási sablon CRD-implementációja. Az **értékeken** keresztül átadott paramétereket használja `{{ .Values.<valuename> }}` . Az alábbi 2. példában ezek az értékek a `{{ .Values.excludedNamespaces }}` és a `{{ .Values.allowedContainerImagesRegex }}` .
- **értékek** (nem kötelező)
  - Meghatározza a korlátozásnak átadandó paramétereket és értékeket. Minden értéknek léteznie kell a korlátozási sablon CRD-ben.

### <a name="audit-example"></a>Példa naplózásra

1. példa: a Resource Manager-üzemmódok naplózási hatásának használata.

```json
"then": {
    "effect": "audit"
}
```

2. példa: a naplózási effektus használata erőforrás-szolgáltatói módra `Microsoft.Kubernetes.Data` . A **részletekben** szereplő további információk meghatározzák a korlátozási sablont és a CRD-t, amelyet a Kubernetes az engedélyezett tároló lemezképek korlátozására használ.

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

A AuditIfNotExists lehetővé teszi az erőforráshoz _kapcsolódó_ erőforrások naplózását, amelyek az **IF** feltételnek felelnek meg, de nem rendelkeznek a **then** feltétel **részleteiben** megadott tulajdonságokkal.

### <a name="auditifnotexists-evaluation"></a>AuditIfNotExists kiértékelése

A AuditIfNotExists azt követően fut, hogy egy erőforrás-szolgáltató kezelt egy erőforrás-létrehozási vagy frissítési kérelmet, és sikeres állapotkódot adott vissza. A naplózás akkor fordul elő, ha nincsenek kapcsolódó erőforrások, vagy ha a **ExistenceCondition** által meghatározott erőforrások nem értékelik az igaz értéket. Az új és frissített erőforrások esetében Azure Policy egy `Microsoft.Authorization/policies/audit/action` műveletet hoz létre a tevékenység naplójába, és nem megfelelőként jelöli meg az erőforrást. Az aktiváláskor az az erőforrás, amely megfelel az **IF** feltételnek, nem megfelelőként megjelölt erőforrás.

### <a name="auditifnotexists-properties"></a>AuditIfNotExists tulajdonságai

A AuditIfNotExists-effektusok **részletek** tulajdonsága az összes olyan altulajdonsággal rendelkezik, amely meghatározza a kapcsolódó erőforrásokat.

- **Típus** (kötelező)
  - Meghatározza a kapcsolódó erőforrás típusát.
  - Ha a **details. Type** egy erőforrástípus az **IF** feltétel erőforrása alatt, a szabályzat a kiértékelt erőforrás hatókörén belül lekérdezi az ilyen **típusú** erőforrásokat. Ellenkező esetben a házirend-lekérdezések a kiértékelt erőforrással megegyező erőforráscsoport alatt vannak.
- **Név** (nem kötelező)
  - Megadja az egyező erőforrás pontos nevét, és a megadott típus összes erőforrása helyett egy adott erőforrás beolvasását okozza.
  - Ha a feltétel értéke **if. Field. Type** , **majd. details. Type** egyezés, akkor a **név** _megadása kötelező_ , `[field('name')]` vagy `[field('fullName')]` egy alárendelt erőforrásnak kell lennie.
    A [naplózási](#audit) hatást azonban figyelembe kell venni.
- **ResourceGroupName** (nem kötelező)
  - Lehetővé teszi a kapcsolódó erőforrás megfeleltetését egy másik erőforráscsoporthoz.
  - Nem alkalmazható, ha a **típus** olyan erőforrás, amely az **IF** feltétel erőforrása alá esik.
  - Az alapértelmezett érték a **IF** Condition erőforrás erőforráscsoport.
- **ExistenceScope** (nem kötelező)
  - Az engedélyezett értékek az _előfizetések_ és a _ResourceGroup_.
  - Azt a hatókört állítja be, ahová a kapcsolódó erőforrást be kell olvasni.
  - Nem alkalmazható, ha a **típus** olyan erőforrás, amely az **IF** feltétel erőforrása alá esik.
  - A _ResourceGroup_ esetében korlátozni kellene az **IF** feltétel erőforrásának erőforráscsoport vagy a **ResourceGroupName** megadott erőforráscsoport esetében.
  - Az _előfizetés_ a kapcsolódó erőforrás teljes előfizetését kérdezi le.
  - Az alapértelmezett érték a _ResourceGroup_.
- **ExistenceCondition** (nem kötelező)
  - Ha nincs megadva, az összes kapcsolódó erőforrás **megfelel a** hatásnak, és nem indítja el a naplózást.
  - Ugyanazt a nyelvet használja, mint az **IF** feltétel házirend-szabálya, de a rendszer minden kapcsolódó erőforrásra külön kiértékeli.
  - Ha bármely egyező kapcsolódó erőforrás igaz értéket ad vissza, a hatás teljesül, és nem indítja el a naplózást.
  - A [Field ()] paranccsal az egyenértékűséget az **IF** feltétel értékeivel is megtekintheti.
  - Például arra használható, hogy ellenőrizze, hogy a szülő erőforrás ( **Ha** a feltétel) ugyanabban az erőforrásban van-e, mint a megfelelő kapcsolódó erőforrás.

### <a name="auditifnotexists-example"></a>AuditIfNotExists példa

Példa: kiértékeli a Virtual Machines annak megállapítására, hogy a kártevők bővítménye létezik-e, majd naplózza a hiányzó állapotot.

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

A Megtagadás megakadályozza egy olyan erőforrás-kérelem használatát, amely nem felel meg a meghatározott szabványoknak egy házirend-definíción keresztül, és a kérést nem teljesíti.

### <a name="deny-evaluation"></a>Kiértékelés megtagadása

Egyeztetett erőforrás Resource Manager módban való létrehozásakor vagy frissítésekor a Megtagadás megakadályozza a kérést az erőforrás-szolgáltatónak való elküldés előtt. A kérést a rendszer a következőként adja vissza: `403 (Forbidden)` . A portálon a tiltott megtekinthető állapotként az üzemelő példányon, amelyet a szabályzat-hozzárendelés megakadályozott. Erőforrás-szolgáltatói mód esetén az erőforrás-szolgáltató kezeli az erőforrás értékelését.

A meglévő erőforrások kiértékelése során a megtagadási szabályzat definíciójának megfelelő erőforrások nem megfelelőként vannak megjelölve.

### <a name="deny-properties"></a>Megtagadási tulajdonságok

Resource Manager módban a megtagadási effektus nem rendelkezik további tulajdonságokkal a házirend-definíció **feltételében** .

Az erőforrás-szolgáltatói mód esetén `Microsoft.Kubernetes.Data` a megtagadási effektus a **részletek** további altulajdonságaival rendelkezik.

- **constraintTemplate** (kötelező)
  - A korlátozási sablon CustomResourceDefinition (CRD), amely új korlátozásokat határoz meg. A sablon meghatározza a Rego logikát, a megkötési sémát és a Azure Policy **értékeit** használó megkötési paramétereket.
- **korlátozás** (kötelező)
  - A korlátozási sablon CRD-implementációja. Az **értékeken** keresztül átadott paramétereket használja `{{ .Values.<valuename> }}` . Az alábbi 2. példában ezek az értékek a `{{ .Values.excludedNamespaces }}` és a `{{ .Values.allowedContainerImagesRegex }}` .
- **értékek** (nem kötelező)
  - Meghatározza a korlátozásnak átadandó paramétereket és értékeket. Minden értéknek léteznie kell a korlátozási sablon CRD-ben.

### <a name="deny-example"></a>Megtagadási példa

1. példa: a megtagadási hatás használata a Resource Manager-módokon.

```json
"then": {
    "effect": "deny"
}
```

2. példa: az erőforrás-szolgáltatói mód megtagadási hatásának használata `Microsoft.Kubernetes.Data` . A **részletekben** szereplő további információk meghatározzák a korlátozási sablont és a CRD-t, amelyet a Kubernetes az engedélyezett tároló lemezképek korlátozására használ.

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

A AuditIfNotExists hasonlóan a DeployIfNotExists házirend-definíciója is végrehajtja a sablon telepítését a feltétel teljesülése esetén.

> [!NOTE]
> A [beágyazott sablonok](../../../azure-resource-manager/templates/linked-templates.md#nested-template) a **deployIfNotExists**-mel támogatottak, de a [csatolt sablonok](../../../azure-resource-manager/templates/linked-templates.md#linked-template) jelenleg nem támogatottak.

### <a name="deployifnotexists-evaluation"></a>DeployIfNotExists kiértékelése

Az DeployIfNotExists körülbelül 15 perccel azután fut le, hogy az erőforrás-szolgáltató egy létrehozási vagy frissítési előfizetést vagy erőforrás-kérést kezelt, és sikeres állapotkódot adott vissza. A sablonok központi telepítése akkor történik, ha nincsenek kapcsolódó erőforrások, vagy ha a **ExistenceCondition** által meghatározott erőforrások nem értékelik az igaz értéket. A központi telepítés időtartama a sablonban található erőforrások bonyolultságával függ.

A kiértékelési ciklusokban az erőforrásoknak megfelelő DeployIfNotExists-effektussal rendelkező házirend-definíciók nem megfelelőként vannak megjelölve, de az adott erőforráson nem végeznek műveleteket. A meglévő, nem megfelelő erőforrások szervizelése [szervizelési feladattal](../how-to/remediate-resources.md)javítható.

### <a name="deployifnotexists-properties"></a>DeployIfNotExists tulajdonságai

A DeployIfNotExists-effektus **részletek** tulajdonsága az összes olyan altulajdonsággal rendelkezik, amely meghatározza a kapcsolódó erőforrásokat, valamint a sablon központi telepítését.

- **Típus** (kötelező)
  - Meghatározza a kapcsolódó erőforrás típusát.
  - A parancs elindítja az erőforrás lekérését az **IF** feltétel erőforrása alatt, majd az adott erőforráscsoporthoz tartozó, az **IF** feltétel erőforrással rendelkező lekérdezéseket.
- **Név** (nem kötelező)
  - Megadja az egyező erőforrás pontos nevét, és a megadott típus összes erőforrása helyett egy adott erőforrás beolvasását okozza.
  - Ha a feltétel értéke **if. Field. Type** , **majd. details. Type** egyezés, akkor a **név** _megadása kötelező_ , `[field('name')]` vagy `[field('fullName')]` egy alárendelt erőforrásnak kell lennie.
- **ResourceGroupName** (nem kötelező)
  - Lehetővé teszi a kapcsolódó erőforrás megfeleltetését egy másik erőforráscsoporthoz.
  - Nem alkalmazható, ha a **típus** olyan erőforrás, amely az **IF** feltétel erőforrása alá esik.
  - Az alapértelmezett érték a **IF** Condition erőforrás erőforráscsoport.
  - Ha egy sablon központi telepítését hajtja végre, a rendszer az adott érték erőforráscsoporthoz helyezi üzembe.
- **ExistenceScope** (nem kötelező)
  - Az engedélyezett értékek az _előfizetések_ és a _ResourceGroup_.
  - Azt a hatókört állítja be, ahová a kapcsolódó erőforrást be kell olvasni.
  - Nem alkalmazható, ha a **típus** olyan erőforrás, amely az **IF** feltétel erőforrása alá esik.
  - A _ResourceGroup_ esetében korlátozni kellene az **IF** feltétel erőforrásának erőforráscsoport vagy a **ResourceGroupName** megadott erőforráscsoport esetében.
  - Az _előfizetés_ a kapcsolódó erőforrás teljes előfizetését kérdezi le.
  - Az alapértelmezett érték a _ResourceGroup_.
- **ExistenceCondition** (nem kötelező)
  - Ha nincs megadva, az összes kapcsolódó erőforrás **megfelel a** hatásnak, és nem indítja el a telepítést.
  - Ugyanazt a nyelvet használja, mint az **IF** feltétel házirend-szabálya, de a rendszer minden kapcsolódó erőforrásra külön kiértékeli.
  - Ha bármely egyező kapcsolódó erőforrás igaz értéket ad vissza, a hatás teljesül, és nem indítja el a telepítést.
  - A [Field ()] paranccsal az egyenértékűséget az **IF** feltétel értékeivel is megtekintheti.
  - Például arra használható, hogy ellenőrizze, hogy a szülő erőforrás ( **Ha** a feltétel) ugyanabban az erőforrásban van-e, mint a megfelelő kapcsolódó erőforrás.
- **roleDefinitionIds** (kötelező)
  - Ennek a tulajdonságnak tartalmaznia kell egy olyan karakterlánc-tömböt, amely megfelel az előfizetés által elérhető szerepköralapú hozzáférés-vezérlési szerepkör-AZONOSÍTÓnak. További információ: [szervizelés – házirend-definíció konfigurálása](../how-to/remediate-resources.md#configure-policy-definition).
- **DeploymentScope** (nem kötelező)
  - Az engedélyezett értékek az _előfizetések_ és a _ResourceGroup_.
  - Az aktiválni kívánt központi telepítés típusának beállítása. Az _előfizetés_ az [előfizetési szinten](../../../azure-resource-manager/templates/deploy-to-subscription.md)üzemelő telepítést jelzi, a _ResourceGroup_ pedig egy erőforráscsoporthoz történő központi telepítést jelez.
  - Az előfizetések szintjén üzemelő példányok használatakor meg kell adni egy _Location_ tulajdonságot a _központi telepítésben_ .
  - Az alapértelmezett érték a _ResourceGroup_.
- **Üzembe helyezés** (kötelező)
  - Ennek a tulajdonságnak szerepelnie kell a sablon teljes telepítésének, mivel az a Put API-nak lesz átadva `Microsoft.Resources/deployments` . További információ: [központi telepítések REST API](/rest/api/resources/deployments).

  > [!NOTE]
  > A **központi telepítési** tulajdonságon belüli összes függvény a sablon összetevőiként lesz kiértékelve, nem pedig a házirendnek. A kivétel a **Paraméterek** tulajdonság, amely értékeket továbbít a szabályzatból a sablonba. Ennek az értéknek a megadásához a sablon paraméter neve alatt található **értéket** kell használni (lásd: _fullDbName_ a DeployIfNotExists példában).

### <a name="deployifnotexists-example"></a>DeployIfNotExists példa

Példa: kiértékeli SQL Server adatbázisokat annak megállapítására, hogy engedélyezve van-e a transparentDataEncryption. Ha nem, akkor a rendszer végrehajtja az engedélyezéshez szükséges központi telepítést.

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

Ez a hatás akkor hasznos, ha tesztelési helyzetekben vagy ha a házirend-definícióban a hatás szerepel. Ez a rugalmasság lehetővé teszi egyetlen hozzárendelés letiltását ahelyett, hogy letiltja a szabályzat összes hozzárendelését.

A letiltott effektus alternatívájaként a **enforcementMode**, amely a szabályzat-hozzárendelésre van beállítva.
Ha  a enforcementMode _le van tiltva_, a rendszer továbbra is kiértékeli az erőforrásokat. A naplózás, például a tevékenységek naplói, és a házirend hatása nem történik meg. További információ: [szabályzat-hozzárendelés – kényszerítési mód](./assignment-structure.md#enforcement-mode).

## <a name="enforceopaconstraint"></a>EnforceOPAConstraint

Ez a hatás a szabályzat-definíciós _móddal_ együtt használható `Microsoft.Kubernetes.Data` . A rendszer az [Opa-korlátozási keretrendszerben](https://github.com/open-policy-agent/frameworks/tree/master/constraint#opa-constraint-framework) definiált forgalomirányító v3 belépésvezérlési szabályok átadására szolgál a Kubernetes-fürtökön az Azure-ban való [megnyitásához](https://www.openpolicyagent.org/) .

> [!IMPORTANT]
> A korlátozott előzetes verziójú házirend-definíciók a **EnforceOPAConstraint** -effektussal és a kapcsolódó **Kubernetes szolgáltatás** kategóriája _elavult_. Ehelyett használja a hatások _naplózása_ és a _Megtagadás_ erőforrás-szolgáltatói módot `Microsoft.Kubernetes.Data` .

### <a name="enforceopaconstraint-evaluation"></a>EnforceOPAConstraint kiértékelése

A nyílt házirend-ügynök beléptetési vezérlője valós időben értékeli ki a fürtön lévő új kéréseket.
A rendszer 15 percenként teljes vizsgálatot hajt végre a fürtön, és az eredményeket Azure Policy.

### <a name="enforceopaconstraint-properties"></a>EnforceOPAConstraint tulajdonságai

A EnforceOPAConstraint Effect **details** tulajdonsága a forgalomirányító v3 belépésvezérlés szabályának altulajdonságaival rendelkezik.

- **constraintTemplate** (kötelező)
  - A korlátozási sablon CustomResourceDefinition (CRD), amely új korlátozásokat határoz meg. A sablon meghatározza a Rego logikát, a megkötési sémát és a Azure Policy **értékeit** használó megkötési paramétereket.
- **korlátozás** (kötelező)
  - A korlátozási sablon CRD-implementációja. Az **értékeken** keresztül átadott paramétereket használja `{{ .Values.<valuename> }}` . Az alábbi példában ezek az értékek a következők: `{{ .Values.cpuLimit }}` és `{{ .Values.memoryLimit }}` .
- **értékek** (nem kötelező)
  - Meghatározza a korlátozásnak átadandó paramétereket és értékeket. Minden értéknek léteznie kell a korlátozási sablon CRD-ben.

### <a name="enforceopaconstraint-example"></a>EnforceOPAConstraint példa

Példa: forgalomirányító v3 belépésvezérlési szabály a tároló CPU-és memória-erőforrás-korlátainak a Kubernetes való beállításához.

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

Ez a hatás a szabályzat-definíciós _móddal_ együtt használható `Microsoft.ContainerService.Data` . A [Rego](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego) által meghatározott forgalomirányító v2 belépésvezérlés-szabályok átadására szolgálnak [az](https://www.openpolicyagent.org/) [Azure Kubernetes Service](../../../aks/intro-kubernetes.md)-ben.

> [!IMPORTANT]
> A korlátozott előzetes verziójú házirend-definíciók a **EnforceRegoPolicy** -effektussal és a kapcsolódó **Kubernetes szolgáltatás** kategóriája _elavult_. Ehelyett használja a hatások _naplózása_ és a _Megtagadás_ erőforrás-szolgáltatói módot `Microsoft.Kubernetes.Data` .

### <a name="enforceregopolicy-evaluation"></a>EnforceRegoPolicy kiértékelése

A nyílt házirend-ügynök beléptetési vezérlője valós időben értékeli ki a fürtön lévő új kéréseket.
A rendszer 15 percenként teljes vizsgálatot hajt végre a fürtön, és az eredményeket Azure Policy.

### <a name="enforceregopolicy-properties"></a>EnforceRegoPolicy tulajdonságai

A EnforceRegoPolicy effektus **details** tulajdonsága a forgalomirányító v2 belépésvezérlés szabályát leíró altulajdonságokkal rendelkezik.

- **policyId** (kötelező)
  - Egy egyedi név, amelyet paraméterként adott át a Rego belépésvezérlési szabálynak.
- **házirend** (kötelező)
  - Megadja a Rego-belépésvezérlés szabályának URI-JÁT.
- **policyParameters** (nem kötelező)
  - Meghatározza a Rego szabályzatnak átadandó paramétereket és értékeket.

### <a name="enforceregopolicy-example"></a>EnforceRegoPolicy példa

Példa: forgalomirányító v2 belépésvezérlési szabály, amely csak a megadott tároló lemezképeit engedélyezi az AK-ban.

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

A módosítással hozzáadhat, frissíthet vagy eltávolíthat tulajdonságokat vagy címkéket az előfizetésben vagy az erőforrásban a létrehozás vagy a frissítés során. Gyakori példa az olyan erőforrásokra vonatkozó címkék frissítése, mint például a costCenter. A meglévő, nem megfelelő erőforrások szervizelése [szervizelési feladattal](../how-to/remediate-resources.md)javítható. Egyetlen módosítási szabálynak tetszőleges számú művelete lehet.

A módosítás a következő műveleteket támogatja:

- Erőforrás-Címkék hozzáadása, cseréje vagy eltávolítása. Címkék esetén a módosítási házirendet `mode` csak akkor kell _indexelni_ , ha a célként megadott erőforrás egy erőforráscsoport.
- Adja hozzá vagy cserélje le a `identity.type` virtuális gépek és a virtuálisgép-méretezési csoportok felügyelt identitási típusának értékét.
- Adja hozzá vagy cserélje le az egyes aliasok értékeit (előzetes verzió).
  - A `Get-AzPolicyAlias | Select-Object -ExpandProperty 'Aliases' | Where-Object { $_.DefaultMetadata.Attributes -eq 'Modifiable' }` használata
    Azure PowerShell **4.6.0** vagy újabb verzióban a módosítással használható aliasok listájának lekéréséhez.

> [!IMPORTANT]
> Ha címkéket kezel, javasolt a hozzáfűzés helyett a módosítás használata, amely további műveleti típusokat és a meglévő erőforrások javítását is lehetővé teszi. A Hozzáfűzés azonban ajánlott, ha nem tud felügyelt identitást létrehozni, vagy a módosítás még nem támogatja az erőforrás-tulajdonság aliasát.

### <a name="modify-evaluation"></a>Értékelés módosítása

A módosítja a kiértékeléseket, mielőtt az erőforrás-szolgáltató feldolgozza a kérést az erőforrás létrehozása vagy frissítése során. A módosítási műveletek a kérelem tartalmára lesznek alkalmazva **, ha** teljesülnek a házirend szabályának feltételei. Az egyes módosítási műveletek megadhatnak egy olyan feltételt, amely meghatározza, hogy mikor alkalmazza a rendszer. A rendszer kihagyja a _hamis_ értékkel kiértékelt feltételekkel rendelkező műveleteket.

Alias megadásakor a rendszer a következő további ellenőrzéseket hajtja végre annak biztosítása érdekében, hogy a módosítási művelet ne módosítsa a kérelem tartalmát úgy, hogy az erőforrás-szolgáltató visszautasítsa azt:

- Az alias leképezésének tulajdonsága "módosítható" jelöléssel van megjelölve a kérelem API-verziójában.
- A módosítási műveletben szereplő token típusa megegyezik a kérelem API-verziójában szereplő tulajdonsághoz tartozó várt jogkivonat-típussal.

Ha az ellenőrzések bármelyike meghiúsul, a szabályzat kiértékelése visszakerül a megadott **conflictEffect**.

> [!IMPORTANT]
> Olyan javasolt, amelyek aliasokat tartalmazó definíciókat módosítanak, a _naplózási_ **ütközési effektussal** elkerülhetők az olyan API-verziókkal kapcsolatos sikertelen kérelmek, amelyeknél a leképezett tulajdonság nem "módosítható". Ha ugyanaz az alias eltérően viselkedik az API-verziók között, a feltételes módosítási műveletek segítségével határozható meg az egyes API-verziókban használt módosítási művelet.

Ha a módosítási hatást használó házirend-definíció egy értékelési ciklus részeként fut, akkor nem módosítja a már létező erőforrásokat. Ehelyett olyan erőforrást jelöl, amely megfelel az **IF** feltétel nem megfelelőnek.

### <a name="modify-properties"></a>Tulajdonságok módosítása

A Modify Effect **details** tulajdonsága minden olyan altulajdonsággal rendelkezik, amely meghatározza a szervizeléshez szükséges engedélyeket, valamint a címkék hozzáadásához, frissítéséhez vagy eltávolításához használt **műveleteket** .

- **roleDefinitionIds** (kötelező)
  - Ennek a tulajdonságnak tartalmaznia kell egy olyan karakterlánc-tömböt, amely megfelel az előfizetés által elérhető szerepköralapú hozzáférés-vezérlési szerepkör-AZONOSÍTÓnak. További információ: [szervizelés – házirend-definíció konfigurálása](../how-to/remediate-resources.md#configure-policy-definition).
  - A definiált szerepkörnek tartalmaznia kell a [közreműködő](../../../role-based-access-control/built-in-roles.md#contributor) szerepkörhöz megadott összes műveletet.
- **conflictEffect** (nem kötelező)
  - Meghatározza, hogy mely "WINS" házirend-definíciót kell módosítani abban az esetben, ha egynél több házirend-definíció módosítja ugyanazt a tulajdonságot, vagy ha a módosítási művelet nem működik a megadott aliason.
    - Új vagy frissített erőforrások esetén a _megtagadással_ rendelkező szabályzat-definíció elsőbbséget élvez. Szabályzat-definíciók a _naplózás_ kihagyása minden **művelet**. Ha több házirend-definíció is meg van _tagadva_, a rendszer ütközésként megtagadja a kérelmet. Ha az összes házirend-definíció _naplózással_ rendelkezik, akkor a rendszer nem dolgozza fel az ütköző házirend-definíciók egyik **műveletét** sem.
    - Ha a meglévő erőforrások esetében több házirend-definíció is meg van _tagadva_, a megfelelőségi állapot _ütközést_ jelez. Ha egy vagy több szabályzat-definíció _megtagadást_ tartalmaz, az egyes hozzárendelések _nem megfelelőnek_ minősülő megfelelőségi állapotot adnak vissza.
  - Elérhető értékek: _naplózás_, _Megtagadás_, _Letiltva_.
  - Az alapértelmezett érték _megtagadva_.
- **műveletek** (kötelező)
  - Az összes címkéző művelet tömbje, amely a megfelelő erőforrásokon fejeződik be.
  - Tulajdonságok:
    - **művelet** (kötelező)
      - Meghatározza, hogy milyen műveletet kell végrehajtani a megfelelő erőforráson. A lehetőségek a következők: _addOrReplace_, _Hozzáadás_, _Eltávolítás_. A _Hozzáadás_ a [hozzáfűzési](#append) effektushoz hasonló.
    - **mező** (kötelező)
      - A hozzáadni, cserélni vagy eltávolítani kívánt címke. A címkék nevének meg kell felelnie a többi [mezőhöz](./definition-structure.md#fields)tartozó elnevezési konvenciónak.
    - **érték** (nem kötelező)
      - Az az érték, amellyel a címkét be kell állítani.
      - Ez a tulajdonság kötelező, ha a **művelet** _addOrReplace_ vagy _hozzáadása_.
    - **feltétel** (nem kötelező)
      - Egy Azure Policy nyelvi kifejezést tartalmazó karakterlánc [, amely a](./definition-structure.md#policy-functions) _true_ vagy a _false értéket_ értékeli.
      - A nem támogatja a következő házirend-funkciókat:, `field()` `resourceGroup()` , `subscription()` .

### <a name="modify-operations"></a>Műveletek módosítása

Az **Operations** Property Array lehetővé teszi több címke különböző módon történő módosítását egyetlen házirend-definícióból. Minden művelet **művelet**, **mező** és **érték** tulajdonságaiból tevődik fel. A művelet meghatározza, hogy a Szervizelési feladat mit tesz a címkék területen, a mező határozza meg, hogy melyik címke módosult, és az érték határozza meg az adott címke új beállítását. Az alábbi példa a következő címke-módosításokat végzi el:

- A `environment` címkét "teszt" értékre állítja, még akkor is, ha már létezik egy másik érték.
- Eltávolítja a címkét `TempResource` .
- Beállítja a `Dept` címkét a szabályzat-hozzárendelésen konfigurált _DeptName_ házirend-paraméterhez.

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

A **Operation** tulajdonság a következő beállításokkal rendelkezik:

|Művelet |Leírás |
|-|-|
|addOrReplace |Hozzáadja a definiált tulajdonságot vagy címkét és értéket az erőforráshoz, még akkor is, ha a tulajdonság vagy a címke már létezik egy másik értékkel. |
|Hozzáadás |Hozzáadja a definiált tulajdonságot vagy címkét és értéket az erőforráshoz. |
|Eltávolítás |Eltávolítja a definiált tulajdonságot vagy címkét az erőforrásból. |

### <a name="modify-examples"></a>Példák módosítása

1. példa: vegye fel a `environment` címkét, és cserélje le a meglévő `environment` címkéket a "test" kifejezésre:

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

2. példa: távolítsa el a `env` címkét, és adja hozzá a `environment` címkét, vagy cserélje le a meglévő `environment` címkéket paraméteres értékre:

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

3. példa: gondoskodjon arról, hogy a Storage-fiók ne engedélyezze a Blobok nyilvános elérését, a módosítási műveletet csak akkor alkalmazza a rendszer, ha az API-verzióval nagyobb vagy egyenlő, mint a "2019-04-01" értékkel:

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

## <a name="layering-policy-definitions"></a>Rétegbeli házirend-definíciók

Egy erőforrásra több hozzárendelés is hatással lehet. Ezek a hozzárendelések lehetnek ugyanazon a hatókörön vagy eltérő hatókörökben. Ezen hozzárendelések mindegyike valószínűleg más effektust is meghatároz. Az egyes szabályzatok feltételeit és hatásait egymástól függetlenül ki kell értékelni. Például:

- 1. szabályzat
  - Erőforrás helyének korlátozása a következőre: "westus"
  - Az A előfizetéshez rendelve
  - Megtagadási hatás
- 2. szabályzat
  - Erőforrás helyének korlátozása a következőre: "eastus"
  - Az A előfizetéshez tartozó B erőforráscsoport számára hozzárendelve
  - Naplózási hatás
  
Ez a beállítás a következő eredményt eredményezheti:

- A (z) "eastus" B erőforráscsoporthoz tartozó összes erőforrás megfelel a 2. házirendnek, és nem felel meg az 1. házirendnek
- A B erőforráscsoport egyik erőforrása, amely nem a "eastus", nem felel meg a 2. házirendnek, és nem felel meg az 1. házirendnek, ha nem a "westus"
- A (z) "westus" nem a következő előfizetésben lévő új erőforrást a szabályzat 1.
- A (z) "westus" nevű és "B" erőforráscsoport összes új erőforrása létrehozva és nem megfelelő a (z) 2. házirendben

Ha az 1. és a 2. házirend mindkét esetben megtagadta a Megtagadás hatását, a helyzet a következőre változik:

- A B erőforráscsoport egyik erőforrása, amely nem a "eastus", nem felel meg a 2. házirendnek
- A B erőforráscsoport egyik erőforrása, amely nem a "westus", nem felel meg az 1. szabályzatnak
- A (z) "westus" nem a következő előfizetésben lévő új erőforrást a szabályzat 1.
- A (z) "A" előfizetéshez tartozó B erőforráscsoport új erőforrásai megtagadva

Minden hozzárendelés külön kiértékelésre kerül. Ilyen esetben nincs lehetőség arra, hogy egy erőforrás a hatókörben lévő eltérések miatt nem csúszik meg. A rétegbeli házirend-definíciók nettó eredményét **összesítő legszigorúbbnak** tekinti a rendszer. Ha például az 1. és a 2. szabályzat is megtagadási hatást gyakorolt, az átfedésben lévő és ütköző házirend-definíciók letiltják az erőforrásokat. Ha továbbra is szüksége van az erőforrás létrehozására a cél hatókörében, tekintse át az egyes hozzárendelések kizárásait a megfelelő házirend-hozzárendelések érvényesítéséhez a megfelelő hatókörökre.

## <a name="next-steps"></a>Következő lépések

- Tekintse át a példákat [Azure Policy mintákon](../samples/index.md).
- Tekintse meg az [Azure szabályzatdefiníciók struktúrája](definition-structure.md) szakaszt.
- Megtudhatja, hogyan [hozhat létre programozott módon házirendeket](../how-to/programmatically-create.md).
- Ismerje meg, hogyan [kérheti le a megfelelőségi információkat](../how-to/get-compliance-data.md).
- Ismerje meg, hogyan javíthatja a [nem megfelelő erőforrásokat](../how-to/remediate-resources.md).
- Tekintse át, hogy a felügyeleti csoport hogyan [rendezi az erőforrásokat az Azure felügyeleti csoportjaival](../../management-groups/overview.md).
