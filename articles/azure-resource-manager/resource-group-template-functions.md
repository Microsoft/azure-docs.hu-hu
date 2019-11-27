---
title: Sablonfüggvények
description: A Azure Resource Manager-sablonban az értékek beolvasására, a karakterláncok és a numerikus karakterek használatára, valamint az üzembe helyezési adatok lekérésére használt függvények leírását ismerteti.
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: cf263bff72677778433d4ef2f3cee8135fe3ab06
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224178"
---
# <a name="azure-resource-manager-template-functions"></a>Azure Resource Manager sablon függvények

Ez a cikk a Azure Resource Manager-sablonokban használható összes funkciót ismerteti. További információ a függvények használatáról a sablonban: [sablon szintaxisa](template-expressions.md).

Saját függvények létrehozásához tekintse meg a [felhasználó által definiált függvények](resource-group-authoring-templates.md#functions)című témakört.

<a id="array" aria-hidden="true" />
<a id="coalesce" aria-hidden="true" />
<a id="concatarray" aria-hidden="true" />
<a id="contains" aria-hidden="true" />
<a id="createarray" aria-hidden="true" />
<a id="empty" aria-hidden="true" />
<a id="first" aria-hidden="true" />
<a id="intersection" aria-hidden="true" />
<a id="json" aria-hidden="true" />
<a id="last" aria-hidden="true" />
<a id="length" aria-hidden="true" />
<a id="min" aria-hidden="true" />
<a id="max" aria-hidden="true" />
<a id="range" aria-hidden="true" />
<a id="skip" aria-hidden="true" />
<a id="take" aria-hidden="true" />
<a id="union" aria-hidden="true" />

## <a name="array-and-object-functions"></a>Tömb- és objektumfüggvények
A Resource Manager számos funkciót biztosít a tömbök és objektumok használatához.

* [tömb](resource-group-template-functions-array.md#array)
* [összefonódik](resource-group-template-functions-array.md#coalesce)
* [concat](resource-group-template-functions-array.md#concat)
* [tartalmaz](resource-group-template-functions-array.md#contains)
* [createArray](resource-group-template-functions-array.md#createarray)
* [üres](resource-group-template-functions-array.md#empty)
* [első](resource-group-template-functions-array.md#first)
* [kereszteződés](resource-group-template-functions-array.md#intersection)
* [JSON](resource-group-template-functions-array.md#json)
* [utolsó](resource-group-template-functions-array.md#last)
* [hossza](resource-group-template-functions-array.md#length)
* [min](resource-group-template-functions-array.md#min)
* [Max](resource-group-template-functions-array.md#max)
* [tartomány](resource-group-template-functions-array.md#range)
* [kihagyása](resource-group-template-functions-array.md#skip)
* [eltarthat](resource-group-template-functions-array.md#take)
* [Union](resource-group-template-functions-array.md#union)

<a id="equals" aria-hidden="true" />
<a id="less" aria-hidden="true" />
<a id="lessorequals" aria-hidden="true" />
<a id="greater" aria-hidden="true" />
<a id="greaterorequals" aria-hidden="true" />

## <a name="comparison-functions"></a>Összehasonlító függvények
A Resource Manager számos funkciót biztosít a sablonokban való összehasonlításhoz.

* [egyenlő](resource-group-template-functions-comparison.md#equals)
* [kisebb](resource-group-template-functions-comparison.md#less)
* [lessOrEquals](resource-group-template-functions-comparison.md#lessorequals)
* [nagyobb](resource-group-template-functions-comparison.md#greater)
* [greaterOrEquals](resource-group-template-functions-comparison.md#greaterorequals)

<a id="deployment" aria-hidden="true" />
<a id="parameters" aria-hidden="true" />
<a id="variables" aria-hidden="true" />

## <a name="deployment-value-functions"></a>Központi telepítési érték függvények
A Resource Manager a következő függvényeket biztosítja a sablon és a telepítéshez kapcsolódó értékek beolvasásához:

* [telepítési](resource-group-template-functions-deployment.md#deployment)
* [környezet](resource-group-template-functions-deployment.md#environment)
* [paraméterek](resource-group-template-functions-deployment.md#parameters)
* [változók](resource-group-template-functions-deployment.md#variables)

<a id="and" aria-hidden="true" />
<a id="bool" aria-hidden="true" />
<a id="if" aria-hidden="true" />
<a id="not" aria-hidden="true" />
<a id="or" aria-hidden="true" />

## <a name="logical-functions"></a>Logikai függvények
A Resource Manager a következő funkciókat biztosítja a logikai feltételekkel való együttműködéshez:

* [és](resource-group-template-functions-logical.md#and)
* [bool](resource-group-template-functions-logical.md#bool)
* [Ha](resource-group-template-functions-logical.md#if)
* [nem](resource-group-template-functions-logical.md#not)
* [vagy](resource-group-template-functions-logical.md#or)

<a id="add" aria-hidden="true" />
<a id="copyindex" aria-hidden="true" />
<a id="div" aria-hidden="true" />
<a id="float" aria-hidden="true" />
<a id="int" aria-hidden="true" />
<a id="minint" aria-hidden="true" />
<a id="maxint" aria-hidden="true" />
<a id="mod" aria-hidden="true" />
<a id="mul" aria-hidden="true" />
<a id="sub" aria-hidden="true" />

## <a name="numeric-functions"></a>Numerikus függvények
A Resource Manager a következő függvényeket biztosítja az egész számokkal való használathoz:

* [hozzáadása](resource-group-template-functions-numeric.md#add)
* [copyIndex](resource-group-template-functions-numeric.md#copyindex)
* [div](resource-group-template-functions-numeric.md#div)
* [float](resource-group-template-functions-numeric.md#float)
* [int](resource-group-template-functions-numeric.md#int)
* [min](resource-group-template-functions-numeric.md#min)
* [Max](resource-group-template-functions-numeric.md#max)
* [mod](resource-group-template-functions-numeric.md#mod)
* [mul](resource-group-template-functions-numeric.md#mul)
* [Sub](resource-group-template-functions-numeric.md#sub)

<a id="extensionResourceId" aria-hidden="true" />
<a id="listkeys" aria-hidden="true" />
<a id="list" aria-hidden="true" />
<a id="providers" aria-hidden="true" />
<a id="reference" aria-hidden="true" />
<a id="resourcegroup" aria-hidden="true" />
<a id="resourceid" aria-hidden="true" />
<a id="subscription" aria-hidden="true" />
<a id="subscriptionResourceId" aria-hidden="true" />
<a id="tenantResourceId" aria-hidden="true" />

## <a name="resource-functions"></a>Erőforrásfüggvények
Resource Manager az alábbi funkciókat biztosít erőforrás-értékeinek beolvasása:

* [extensionResourceId](resource-group-template-functions-resource.md#extensionresourceid)
* [listAccountSas](resource-group-template-functions-resource.md#list)
* [Listkeys műveletének beolvasása](resource-group-template-functions-resource.md#listkeys)
* [listSecrets](resource-group-template-functions-resource.md#list)
* [listáját](resource-group-template-functions-resource.md#list)
* [szolgáltatók](resource-group-template-functions-resource.md#providers)
* [referencia](resource-group-template-functions-resource.md#reference)
* [resourceGroup](resource-group-template-functions-resource.md#resourcegroup)
* [resourceId](resource-group-template-functions-resource.md#resourceid)
* [előfizetést](resource-group-template-functions-resource.md#subscription)
* [subscriptionResourceId](resource-group-template-functions-resource.md#subscriptionresourceid)
* [tenantResourceId](resource-group-template-functions-resource.md#tenantresourceid)

<a id="base64" aria-hidden="true" />
<a id="base64tojson" aria-hidden="true" />
<a id="base64tostring" aria-hidden="true" />
<a id="concat" aria-hidden="true" />
<a id="containsstring" aria-hidden="true" />
<a id="datauri" aria-hidden="true" />
<a id="datauritostring" aria-hidden="true" />
<a id="emptystring" aria-hidden="true" />
<a id="endswith" aria-hidden="true" />
<a id="firststring" aria-hidden="true" />
<a id="guid" aria-hidden="true" />
<a id="indexof" aria-hidden="true" />
<a id="laststring" aria-hidden="true" />
<a id="lastindexof" aria-hidden="true" />
<a id="lengthstring" aria-hidden="true" />
<a id="padleft" aria-hidden="true" />
<a id="replace" aria-hidden="true" />
<a id="skipstring" aria-hidden="true" />
<a id="split" aria-hidden="true" />
<a id="startswith" aria-hidden="true" />
<a id="string" aria-hidden="true" />
<a id="substring" aria-hidden="true" />
<a id="takestring" aria-hidden="true" />
<a id="tolower" aria-hidden="true" />
<a id="toupper" aria-hidden="true" />
<a id="trim" aria-hidden="true" />
<a id="uniquestring" aria-hidden="true" />
<a id="uri" aria-hidden="true" />
<a id="uricomponent" aria-hidden="true" />
<a id="uricomponenttostring" aria-hidden="true" />

## <a name="string-functions"></a>Sztringfüggvények
A Resource Manager a következő függvényeket biztosítja a karakterláncok használatához:

* [Base64](resource-group-template-functions-string.md#base64)
* [base64ToJson](resource-group-template-functions-string.md#base64tojson)
* [base64ToString](resource-group-template-functions-string.md#base64tostring)
* [concat](resource-group-template-functions-string.md#concat)
* [tartalmaz](resource-group-template-functions-string.md#contains)
* [dataUri](resource-group-template-functions-string.md#datauri)
* [dataUriToString](resource-group-template-functions-string.md#datauritostring)
* [üres](resource-group-template-functions-string.md#empty)
* [endsWith](resource-group-template-functions-string.md#endswith)
* [első](resource-group-template-functions-string.md#first)
* [formátumban](resource-group-template-functions-string.md#format)
* [GUID](resource-group-template-functions-string.md#guid)
* [indexOf](resource-group-template-functions-string.md#indexof)
* [utolsó](resource-group-template-functions-string.md#last)
* [lastIndexOf](resource-group-template-functions-string.md#lastindexof)
* [hossza](resource-group-template-functions-string.md#length)
* [newGuid](resource-group-template-functions-string.md#newguid)
* [padLeft](resource-group-template-functions-string.md#padleft)
* [csere](resource-group-template-functions-string.md#replace)
* [kihagyása](resource-group-template-functions-string.md#skip)
* [felosztás](resource-group-template-functions-string.md#split)
* [startsWith](resource-group-template-functions-string.md#startswith)
* [karakterlánc](resource-group-template-functions-string.md#string)
* [substring](resource-group-template-functions-string.md#substring)
* [eltarthat](resource-group-template-functions-string.md#take)
* [toLower](resource-group-template-functions-string.md#tolower)
* [toUpper](resource-group-template-functions-string.md#toupper)
* [Trim](resource-group-template-functions-string.md#trim)
* [uniqueString](resource-group-template-functions-string.md#uniquestring)
* [URI](resource-group-template-functions-string.md#uri)
* [uriComponent](resource-group-template-functions-string.md#uricomponent)
* [uriComponentToString](resource-group-template-functions-string.md#uricomponenttostring)
* [utcNow](resource-group-template-functions-string.md#utcnow)

## <a name="next-steps"></a>Következő lépések

* Azure Resource Manager sablonban található részekről a következő témakörben talál leírást: [Azure Resource Manager sablonok készítése](resource-group-authoring-templates.md)
* Több sablon egyesítéséhez tekintse meg [a csatolt sablonok használata Azure Resource Manager használatával](resource-group-linked-templates.md) című témakört.
* Ha meg szeretné ismételni a megadott számú alkalommal egy erőforrás-típus létrehozásakor, tekintse meg az [erőforrások több példányának létrehozása a Azure Resource Managerban](resource-group-create-multiple.md) című témakört.
* A létrehozott sablon üzembe helyezéséről lásd: [alkalmazás központi telepítése Azure Resource Manager sablonnal](resource-group-template-deploy.md)
