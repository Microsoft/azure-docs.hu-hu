---
title: Azure AD Connect Cloud Sync átalakítások
description: Ez a cikk azt ismerteti, hogyan használhatók a transzformációk az alapértelmezett attribútumok leképezésének módosításához.
author: billmath
ms.author: billmath
manager: davba
ms.date: 12/02/2019
ms.topic: how-to
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: 1c81ee0ebace65e50cdab5b5b223d5e5eae4ff9a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98613573"
---
# <a name="transformations"></a>Átalakítások

Az átalakítással megváltoztathatja az attribútumok szinkronizálásának alapértelmezett viselkedését Azure Active Directory (Azure AD) segítségével a Cloud Sync használatával.

A feladat elvégzéséhez szerkesztenie kell a sémát, majd újra el kell küldenie egy webes kérelem használatával.

A Cloud Sync attribútumokkal kapcsolatos további információkért lásd [Az Azure ad-séma megismerése](concept-attributes.md)című témakört.


## <a name="retrieve-the-schema"></a>Séma beolvasása
A séma beolvasásához kövesse a [séma megtekintése](concept-attributes.md#view-the-schema)című témakör lépéseit. 

## <a name="custom-attribute-mapping"></a>Egyéni attribútumok leképezése
Egyéni attribútumok hozzárendelésének hozzáadásához kövesse az alábbi lépéseket.

1. Másolja a sémát egy szöveg-vagy Kódszerkesztő-Szerkesztőbe, például a [Visual Studio Code](https://code.visualstudio.com/)-ba.
1. Keresse meg a sémában frissíteni kívánt objektumot.

   ![Objektum a sémában](media/how-to-transformation/transform-1.png)</br>
1. Keresse meg a `ExtensionAttribute3` felhasználói objektum alatt található kódot.

    ```
                            {
                                "defaultValue": null,
                                "exportMissingReferences": false,
                                "flowBehavior": "FlowWhenChanged",
                                "flowType": "Always",
                                "matchingPriority": 0,
                                "targetAttributeName": "ExtensionAttribute3",
                                "source": {
                                    "expression": "Trim([extensionAttribute3])",
                                    "name": "Trim",
                                    "type": "Function",
                                    "parameters": [
                                        {
                                            "key": "source",
                                            "value": {
                                                "expression": "[extensionAttribute3]",
                                                "name": "extensionAttribute3",
                                                "type": "Attribute",
                                                "parameters": []
                                            }
                                        }
                                    ]
                                }
                            },
    ```
1. Szerkessze a kódot úgy, hogy a vállalati attribútum le legyen képezve `ExtensionAttribute3` .

   ```
                                    {
                                        "defaultValue": null,
                                        "exportMissingReferences": false,
                                        "flowBehavior": "FlowWhenChanged",
                                        "flowType": "Always",
                                        "matchingPriority": 0,
                                        "targetAttributeName": "ExtensionAttribute3",
                                        "source": {
                                            "expression": "Trim([company])",
                                            "name": "Trim",
                                            "type": "Function",
                                            "parameters": [
                                                {
                                                    "key": "source",
                                                    "value": {
                                                        "expression": "[company]",
                                                        "name": "company",
                                                        "type": "Attribute",
                                                        "parameters": []
                                                    }
                                                }
                                            ]
                                        }
                                    },
   ```
 1. Másolja vissza a sémát a Graph Explorerben, módosítsa a **kérelem típusát** a **put** értékre, majd válassza a **lekérdezés futtatása** lehetőséget.

    ![Lekérdezés futtatása](media/how-to-transformation/transform-2.png)

 1. Most a Azure Portal nyissa meg a felhőalapú szinkronizálási konfigurációt, és válassza a **kiépítés újraindítása** lehetőséget.

    ![Kiépítés újraindítása](media/how-to-transformation/transform-3.png)

 1. Egy kis idő elteltével ellenőrizze, hogy az attribútumok feltöltése a következő lekérdezés futtatásával történik-e a Graph Explorerben: `https://graph.microsoft.com/beta/users/{Azure AD user UPN}` .
 1. Ekkor az értéknek kell megjelennie.

    ![Az érték jelenik meg](media/how-to-transformation/transform-4.png)

## <a name="custom-attribute-mapping-with-function"></a>Egyéni attribútumok leképezése függvénnyel
A speciális leképezéshez használhatja azokat a függvényeket, amelyek lehetővé teszik az adatok módosítását és az attribútumok értékének létrehozását a szervezet igényeinek megfelelően.

A feladat elvégzéséhez kövesse az előző lépéseket, majd szerkessze a végső érték létrehozásához használt függvényt.

A szintaxissal és a kifejezésekkel kapcsolatos további információkért lásd: [kifejezések írása az attribútumok megfeleltetéséhez Azure Active Directory](reference-expressions.md).


## <a name="next-steps"></a>Következő lépések 

- [Mi az az üzembe helyezés?](what-is-provisioning.md)
- [Mi az Azure AD Connect Cloud Sync?](what-is-cloud-sync.md)
