---
title: 'Azure VPN Gateway: a csomagok rögzítésének konfigurálása'
description: További információ a VPN-átjárón használható csomag-rögzítési funkciókról a probléma okának csökkentése érdekében.
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alzam
ms.openlocfilehash: 0983139d1c9af235eba4c9f99da7bc9dea3f231b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101726614"
---
# <a name="configure-packet-capture-for-vpn-gateways"></a>A csomagok rögzítésének konfigurálása a VPN-átjárók számára

A kapcsolat és a teljesítménnyel kapcsolatos problémák gyakran bonyolultak. Jelentős időt és fáradságot is igénybe vehet, hogy leszűkítse a probléma okát. A csomagok rögzítése segítségével leszűkítheti a probléma hatókörét a hálózat bizonyos részeire. Segít eldönteni, hogy a probléma a hálózat ügyfél oldalán, a hálózat Azure-oldalán vagy valahol a között van-e. A probléma szűkítése után hatékonyabb a hibakeresés és a javító művelet.

Vannak általánosan elérhető csomag-rögzítési eszközök. Ezeknek az eszközöknek a megfelelő csomagjainak beszerzése nehézkes lehet, különösen nagy mennyiségű forgalmi helyzetekben. Az Azure VPN Gateway Packet Capture által biztosított szűrési képességek jelentős differenciált. A VPN Gateway csomagok rögzítését együtt használhatja az általánosan elérhető csomagok rögzítése eszközzel.

## <a name="vpn-gateway-packet-capture-filtering-capabilities"></a>A csomagok rögzítésének szűrési képességei VPN Gateway

Az igénytől függően futtathat VPN Gateway csomagok rögzítését az átjárón vagy egy adott kapcsolaton. A csomagok rögzítését egyszerre több alagúton is futtathatja. Az egyirányú vagy kétirányú forgalmat, az IKE-és az ESP-forgalmat, valamint a belső csomagokat a VPN-átjáró szűrésével együtt is rögzítheti.

A nagy mennyiségű forgalomban felmerülő problémák elkülönítése érdekében hasznos lehet egy öt rekordos szűrő (forrásoldali alhálózat, célként használt alhálózat, forrásport, célport, protokoll) és TCP-jelzők (SYN, ACK, FIN, URG, PSH, első) használata.

Az alábbi JSON-és JSON-sémák az egyes tulajdonságok magyarázatát tartalmazzák. Az alábbiakban néhány korlátozást érdemes figyelembe venni a csomagok rögzítésekor:

- Az itt látható sémában a szűrő egy tömb, de jelenleg egyszerre csak egy szűrőt lehet használni.
- Egyszerre több, az átjáróra kiterjedő csomagok rögzítése nem futtatható.
- Egyszerre nem futtathat több csomagot egyetlen kapcsolaton. Egyszerre több csomagot is futtathat különböző kapcsolatokon.
- Egy átjárón belül legfeljebb öt csomagot lehet futtatni párhuzamosan. A csomagok rögzítése az egész átjáróra kiterjedő csomagok rögzítése és a kapcsolaton belüli csomagok rögzítésének kombinációja lehet.
- A MaxPacketBufferSize mértékegysége bájt, a MaxFileSize pedig megabájt.

### <a name="example-json"></a>Példa JSON-ra
```JSON-interactive
{
  "TracingFlags": 11,
  "MaxPacketBufferSize": 120,
  "MaxFileSize": 200,
  "Filters": [
    {
      "SourceSubnets": [
        "20.1.1.0/24"
      ],
      "DestinationSubnets": [
        "10.1.1.0/24"
      ],
      "SourcePort": [
        500
      ],
      "DestinationPort": [
        4500
      ],
      "Protocol": [
        6
      ],
      "TcpFlags": 16,
      "CaptureSingleDirectionTrafficOnly": true
    }
  ]
}
```
### <a name="json-schema"></a>JSON-séma
```JSON-interactive
{
    "type": "object",
    "title": "The Root Schema",
    "description": "The root schema input JSON filter for packet capture",
    "default": {},
    "additionalProperties": true,
    "required": [
        "TracingFlags",
        "MaxPacketBufferSize",
        "MaxFileSize",
        "Filters"
    ],
    "properties": {
        "TracingFlags": {
            "$id": "#/properties/TracingFlags",
            "type": "integer",
            "title": "The Tracingflags Schema",
            "description": "Tracing flags that customer can pass to define which packets are to be captured. Supported values are CaptureESP = 1, CaptureIKE = 2, CaptureOVPN = 8. The final value is OR of the bits.",
            "default": 11,
            "examples": [
                11
            ]
        },
        "MaxPacketBufferSize": {
            "$id": "#/properties/MaxPacketBufferSize",
            "type": "integer",
            "title": "The Maxpacketbuffersize Schema",
            "description": "Maximum buffer size of each packet. The capture will only contain contents of each packet truncated to this size.",
            "default": 120,
            "examples": [
                120
            ]
        },
        "MaxFileSize": {
            "$id": "#/properties/MaxFileSize",
            "type": "integer",
            "title": "The Maxfilesize Schema",
            "description": "Maximum file size of the packet capture file. It is a circular buffer.",
            "default": 100,
            "examples": [
                100
            ]
        },
        "Filters": {
            "$id": "#/properties/Filters",
            "type": "array",
            "title": "The Filters Schema",
            "description": "An array of filters that can be passed to filter inner ESP traffic.",
            "default": [],
            "examples": [
                [
                    {
                        "Protocol": [
                            6
                        ],
                        "CaptureSingleDirectionTrafficOnly": true,
                        "SourcePort": [
                            500
                        ],
                        "DestinationPort": [
                            4500
                        ],
                        "TcpFlags": 16,
                        "SourceSubnets": [
                            "20.1.1.0/24"
                        ],
                        "DestinationSubnets": [
                            "10.1.1.0/24"
                        ]
                    }
                ]
            ],
            "additionalItems": true,
            "items": {
                "$id": "#/properties/Filters/items",
                "type": "object",
                "title": "The Items Schema",
                "description": "An explanation about the purpose of this instance.",
                "default": {},
                "examples": [
                    {
                        "SourcePort": [
                            500
                        ],
                        "DestinationPort": [
                            4500
                        ],
                        "TcpFlags": 16,
                        "SourceSubnets": [
                            "20.1.1.0/24"
                        ],
                        "DestinationSubnets": [
                            "10.1.1.0/24"
                        ],
                        "Protocol": [
                            6
                        ],
                        "CaptureSingleDirectionTrafficOnly": true
                    }
                ],
                "additionalProperties": true,
                "required": [
                    "SourceSubnets",
                    "DestinationSubnets",
                    "SourcePort",
                    "DestinationPort",
                    "Protocol",
                    "TcpFlags",
                    "CaptureSingleDirectionTrafficOnly"
                ],
                "properties": {
                    "SourceSubnets": {
                        "$id": "#/properties/Filters/items/properties/SourceSubnets",
                        "type": "array",
                        "title": "The Sourcesubnets Schema",
                        "description": "An array of source subnets that need to match the Source IP address of a packet. Packet can match any one value in the array of inputs.",
                        "default": [],
                        "examples": [
                            [
                                "20.1.1.0/24"
                            ]
                        ],
                        "additionalItems": true,
                        "items": {
                            "$id": "#/properties/Filters/items/properties/SourceSubnets/items",
                            "type": "string",
                            "title": "The Items Schema",
                            "description": "An explanation about the purpose of this instance.",
                            "default": "",
                            "examples": [
                                "20.1.1.0/24"
                            ]
                        }
                    },
                    "DestinationSubnets": {
                        "$id": "#/properties/Filters/items/properties/DestinationSubnets",
                        "type": "array",
                        "title": "The Destinationsubnets Schema",
                        "description": "An array of destination subnets that need to match the Destination IP address of a packet. Packet can match any one value in the array of inputs.",
                        "default": [],
                        "examples": [
                            [
                                "10.1.1.0/24"
                            ]
                        ],
                        "additionalItems": true,
                        "items": {
                            "$id": "#/properties/Filters/items/properties/DestinationSubnets/items",
                            "type": "string",
                            "title": "The Items Schema",
                            "description": "An explanation about the purpose of this instance.",
                            "default": "",
                            "examples": [
                                "10.1.1.0/24"
                            ]
                        }
                    },
                    "SourcePort": {
                        "$id": "#/properties/Filters/items/properties/SourcePort",
                        "type": "array",
                        "title": "The Sourceport Schema",
                        "description": "An array of source ports that need to match the Source port of a packet. Packet can match any one value in the array of inputs.",
                        "default": [],
                        "examples": [
                            [
                                500
                            ]
                        ],
                        "additionalItems": true,
                        "items": {
                            "$id": "#/properties/Filters/items/properties/SourcePort/items",
                            "type": "integer",
                            "title": "The Items Schema",
                            "description": "An explanation about the purpose of this instance.",
                            "default": 0,
                            "examples": [
                                500
                            ]
                        }
                    },
                    "DestinationPort": {
                        "$id": "#/properties/Filters/items/properties/DestinationPort",
                        "type": "array",
                        "title": "The Destinationport Schema",
                        "description": "An array of destination ports that need to match the Destination port of a packet. Packet can match any one value in the array of inputs.",
                        "default": [],
                        "examples": [
                            [
                                4500
                            ]
                        ],
                        "additionalItems": true,
                        "items": {
                            "$id": "#/properties/Filters/items/properties/DestinationPort/items",
                            "type": "integer",
                            "title": "The Items Schema",
                            "description": "An explanation about the purpose of this instance.",
                            "default": 0,
                            "examples": [
                                4500
                            ]
                        }
                    },
                    "Protocol": {
                        "$id": "#/properties/Filters/items/properties/Protocol",
                        "type": "array",
                        "title": "The Protocol Schema",
                        "description": "An array of protocols that need to match the Protocol of a packet. Packet can match any one value in the array of inputs.",
                        "default": [],
                        "examples": [
                            [
                                6
                            ]
                        ],
                        "additionalItems": true,
                        "items": {
                            "$id": "#/properties/Filters/items/properties/Protocol/items",
                            "type": "integer",
                            "title": "The Items Schema",
                            "description": "An explanation about the purpose of this instance.",
                            "default": 0,
                            "examples": [
                                6
                            ]
                        }
                    },
                    "TcpFlags": {
                        "$id": "#/properties/Filters/items/properties/TcpFlags",
                        "type": "integer",
                        "title": "The Tcpflags Schema",
                        "description": "A list of TCP flags. The TCP flags set on the packet must match any flag in the list of flags provided. FIN = 0x01,SYN = 0x02,RST = 0x04,PSH = 0x08,ACK = 0x10,URG = 0x20,ECE = 0x40,CWR = 0x80. An OR of flags can be provided.",
                        "default": 0,
                        "examples": [
                            16
                        ]
                    },
                    "CaptureSingleDirectionTrafficOnly": {
                        "$id": "#/properties/Filters/items/properties/CaptureSingleDirectionTrafficOnly",
                        "type": "boolean",
                        "title": "The Capturesingledirectiontrafficonly Schema",
                        "description": "A flags which when set captures reverse traffic also.",
                        "default": false,
                        "examples": [
                            true
                        ]
                    }
                }
            }
        }
    }
}
```

## <a name="packet-capture---portal"></a>Csomag rögzítése – portál

Megadhatja a csomagok rögzítését a Azure Portalban.

:::image type="content" source="./media/packet-capture/portal.jpg" alt-text="Képernyőkép a csomag rögzítéséről a portálon." lightbox="./media/packet-capture/portal.jpg":::

## <a name="packet-capture---powershell"></a>Csomag rögzítése – PowerShell

Az alábbi példák olyan PowerShell-parancsokat mutatnak be, amelyek elindítják és leállítják a csomagok rögzítését. További információ a paraméterek beállításairól: [Start-AzVirtualnetworkGatewayPacketCapture](/powershell/module/az.network/start-azvirtualnetworkgatewaypacketcapture).

### <a name="start-packet-capture-for-a-vpn-gateway"></a>A csomagok rögzítésének elindítása VPN-átjáró esetén

```azurepowershell-interactive
Start-AzVirtualnetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName"
```

Szűrő alkalmazásához a választható paramétert használhatja `-FilterData` .

### <a name="stop-packet-capture-for-a-vpn-gateway"></a>A csomagok rögzítésének leállítása a VPN-átjárón

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName" -SasUrl "YourSASURL"
```

### <a name="start-packet-capture-for-a-vpn-gateway-connection"></a>A csomagok rögzítésének elindítása a VPN Gateway-kapcsolathoz

```azurepowershell-interactive
Start-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName"
```

Szűrő alkalmazásához a választható paramétert használhatja `-FilterData` .

### <a name="stop-packet-capture-on-a-vpn-gateway-connection"></a>A csomagok rögzítésének leállítása a VPN Gateway-kapcsolaton

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName" -SasUrl "YourSASURL"
```

## <a name="key-considerations"></a>Fő szempontok

- A csomagok rögzítésének futtatása hatással lehet a teljesítményre. Ne felejtse el leállítani a csomagok rögzítését, ha nincs rá szükség.
- A csomagok minimális rögzítési időtartama 600 másodperc. Az elérési úton található több összetevő szinkronizálási problémái miatt előfordulhat, hogy rövidebb csomagok rögzítése nem biztosít teljes adatmennyiséget.
- A csomag-rögzítési adatfájlok PCAP formátumban jönnek létre. A PCAP-fájlok megnyitásához használjon Wireshark vagy más általánosan elérhető alkalmazásokat.
- A csomagok rögzítése nem támogatott a házirend-alapú átjárók esetében.
- Ha a `SASurl` paraméter nincs megfelelően konfigurálva, a nyomkövetés sikertelen lehet a tárolási hibákkal. A paraméterek megfelelő létrehozásával kapcsolatos példákért `SASurl` lásd: [stop-AzVirtualNetworkGatewayPacketCapture](/powershell/module/az.network/stop-azvirtualnetworkgatewaypacketcapture).



## <a name="next-steps"></a>Következő lépések

További információ a VPN Gatewayről: [Mi az VPN Gateway?](vpn-gateway-about-vpngateways.md)
