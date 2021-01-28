---
title: Resource Manager-sablonok – minták Log Analytics munkaterületekhez
description: Példa Azure Resource Manager sablonokat Log Analytics-munkaterületek üzembe helyezéséhez és az adatforrások konfigurálásához Azure Monitorban.
ms.subservice: logs
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 05/18/2020
ms.openlocfilehash: c755ee0ee91156da9910a9d43c891c87f86cd430
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945037"
---
# <a name="resource-manager-template-samples-for-log-analytics-workspaces-in-azure-monitor"></a>Resource Manager-sablonok – minták Log Analytics munkaterületekhez Azure Monitor
Ez a cikk mintákat [Azure Resource Manager sablonokat](../../azure-resource-manager/templates/template-syntax.md) tartalmaz log Analytics munkaterületek létrehozásához és konfigurálásához Azure monitorban. Mindegyik minta tartalmaz egy sablonfájlt és egy, a sablonhoz adni kívánt minta értékeket tartalmazó paramétereket tartalmazó fájlt.

[!INCLUDE [azure-monitor-samples](../../../includes/azure-monitor-resource-manager-samples.md)]


## <a name="template-references"></a>Sablon referenciái

- [Microsoft. OperationalInsights-munkaterületek](/azure/templates/microsoft.operationalinsights/2020-03-01-preview/workspaces
) 
- [Microsoft. OperationalInsights-munkaterületek/adatforrások](/azure/templates/microsoft.operationalinsights/2020-03-01-preview/workspaces/datasources)

## <a name="create-a-log-analytics-workspace"></a>Log Analytics-munkaterület létrehozása
Az alábbi minta egy új üres Log Analytics munkaterületet hoz létre.

### <a name="notes"></a>Jegyzetek

- Ha **ingyenes** díjszabási szintet ad meg, távolítsa el a **retentionInDays** elemet.

### <a name="template-file"></a>Sablonfájl

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "workspaceName": {
          "type": "string",
          "metadata": {
            "description": "Name of the workspace."
          }
      },
      "sku": {
          "type": "string",
          "allowedValues": [
            "pergb2018",
            "Free",
            "Standalone",
            "PerNode",
            "Standard",
            "Premium"
            ],
          "defaultValue": "pergb2018",
          "metadata": {
          "description": "Pricing tier: PerGB2018 or legacy tiers (Free, Standalone, PerNode, Standard or Premium) which are not available to all customers."
          }
        },
        "location": {
          "type": "string",
          "allowedValues": [
          "australiacentral", 
          "australiaeast", 
          "australiasoutheast", 
          "brazilsouth",
          "canadacentral", 
          "centralindia", 
          "centralus", 
          "eastasia", 
          "eastus", 
          "eastus2", 
          "francecentral", 
          "japaneast", 
          "koreacentral", 
          "northcentralus", 
          "northeurope", 
          "southafricanorth", 
          "southcentralus", 
          "southeastasia",
          "switzerlandnorth",
          "switzerlandwest",
          "uksouth", 
          "ukwest", 
          "westcentralus", 
          "westeurope", 
          "westus", 
          "westus2" 
          ],
          "metadata": {
              "description": "Specifies the location for the workspace."
              }
        },
        "retentionInDays": {
          "type": "int",
          "defaultValue": 120,
          "metadata": {
            "description": "Number of days to retain data."
          }
        },
        "resourcePermissions": {
          "type": "bool",
          "metadata": {
            "description": "true to use resource or workspace permissions. false to require workspace permissions."
          }
      }

      },
      "resources": [
      {
          "type": "Microsoft.OperationalInsights/workspaces",
          "name": "[parameters('workspaceName')]",
          "apiVersion": "2020-08-01",
          "location": "[parameters('location')]",
          "properties": {
              "sku": {
                  "name": "[parameters('sku')]"
              },
              "retentionInDays": "[parameters('retentionInDays')]",
              "features": {
                  "searchVersion": 1,
                  "legacy": 0,
                  "enableLogAccessUsingOnlyResourcePermissions": "[parameters('resourcePermissions')]"
              }
          }
      }
  ]
}
```

### <a name="parameter-file"></a>Paraméter fájlja

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "value": "MyWorkspace"
    },
    "sku": {
      "value": "pergb2018"
    },
    "location": {
      "value": "eastus"
    },
    "resourcePermissions": {
      "value": true
    }
  }
}
```

## <a name="collect-windows-events"></a>Windows-események gyűjtése
Az alábbi példa [Windows-események](../platform/data-sources-windows-events.md) gyűjteményét adja hozzá egy meglévő munkaterülethez.

### <a name="notes"></a>Jegyzetek

- Vegyen fel egy **adatforrást** a gyűjteni kívánt eseménynaplóba. Az egyes naplókhoz különböző típusú eseménytípus is megadható.

### <a name="template-file"></a>Sablonfájl

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "workspaceName": {
          "type": "string"
      },
      "location": {
        "type": "string"
      }
  },
  "resources": [
  {
      "type": "Microsoft.OperationalInsights/workspaces",
      "apiVersion": "2020-08-01",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "resources": [
        {
          "type": "datasources",
          "apiVersion": "2020-08-01",
          "name": "WindowsEventsSystem",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "WindowsEvent",
          "properties": {
            "eventLogName": "System",
            "eventTypes": [
              {
                "eventType": "Error"
              },
              {
                "eventType": "Warning"
              }
            ]
          }
        },
        {
          "type": "datasources",
          "apiVersion": "2020-08-01",
          "name": "WindowsEventsApplication",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "WindowsEvent",
          "properties": {
            "eventLogName": "Application",
            "eventTypes": [
              {
                "eventType": "Error"
              },
              {
                "eventType": "Warning"
              },
              {
                "eventType": "Information"
              }
            ]
          }
        }
      ]
    }
  ]
}

```

### <a name="parameter-file"></a>Paraméter fájlja

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "value": "MyWorkspace"
    },
    "location": {
      "value": "eastus"
    }
  }
}
```

## <a name="collect-syslog"></a>Syslog begyűjtése
A következő minta a syslog- [események](../platform/data-sources-syslog.md) gyűjteményét adja hozzá egy meglévő munkaterülethez.

### <a name="notes"></a>Jegyzetek

- Vegyen fel egy **adatforrást** az egyes létesítmények összegyűjtéséhez. Az egyes létesítményeknél különböző súlyossági szintet adhat meg.

### <a name="template-file"></a>Sablonfájl

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "description": "Name of the workspace."
            }
        },
        "location": {
            "type": "string",
            "metadata": {
              "description": "Specifies the location in which to create the workspace."
            }
        }
    },
    "resources": [
    {
        "apiVersion": "2020-08-01",
        "type": "Microsoft.OperationalInsights/workspaces",
        "name": "[parameters('workspaceName')]",
        "location": "[parameters('location')]",
        "resources": [
            {
                "type": "datasources",
                "apiVersion": "2020-08-01",
                "name": "SyslogKern",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                ],
                "kind": "LinuxSyslog",
                "properties": {
                    "syslogName": "kern",
                    "syslogSeverities": [
                        {
                            "severity": "emerg"
                        },
                        {
                            "severity": "alert"
                        },
                        {
                            "severity": "crit"
                        },
                        {
                            "severity": "err"
                        },
                        {
                            "severity": "warning"
                        },
                        {
                            "severity": "notice"
                        },
                        {
                            "severity": "info"
                        },
                        {
                            "severity": "debug"
                        }
                    ]
                }
            },
            {
                "type": "datasources",
                "apiVersion": "2020-08-01",
                "name": "SyslogDaemon",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                ],
                "kind": "LinuxSyslog",
                "properties": {
                    "syslogName": "daemon",
                    "syslogSeverities": [
                        {
                            "severity": "emerg"
                        },
                        {
                            "severity": "alert"
                        },
                        {
                            "severity": "crit"
                        },
                        {
                            "severity": "err"
                        },
                        {
                            "severity": "warning"
                        }
                    ]
                }
            },
            {
                "apiVersion": "2020-08-01",
                "type": "datasources",
                "name": "SyslogCollection",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                ],
                "kind": "LinuxSyslogCollection",
                "properties": {
                    "state": "Enabled"
                }
            }
        ]
      }
    ]
}

```


### <a name="parameter-file"></a>Paraméter fájlja

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "value": "MyWorkspace"
    },
    "location": {
      "value": "eastus"
    }
  }
}
```

## <a name="collect-windows-performance-counters"></a>Windows-teljesítményszámlálók gyűjtése
Az alábbi példa egy meglévő munkaterülethez hozzáadja a [Windows](../platform/data-sources-performance-counters.md) -teljesítményszámlálók gyűjteményét.

### <a name="notes"></a>Jegyzetek

- Vegyen fel egy **adatforrást** az egyes számlálók és példányok összegyűjtéséhez. Az egyes számlálók és példányok kombinációi esetében különböző gyűjtési arányt adhat meg.
  
### <a name="template-file"></a>Sablonfájl

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "description": "Name of the workspace."
            }
        },
        "location": {
            "type": "string",
            "metadata": {
              "description": "Location of the workspace."
            }
        }
    },
    "resources": [
    {
        "apiVersion": "2020-08-01",
        "type": "Microsoft.OperationalInsights/workspaces",
        "name": "[parameters('workspaceName')]",
        "location": "[parameters('location')]",
        "resources": [
          {
            "apiVersion": "2020-08-01",
            "type": "datasources",
            "name": "WindowsPerfMemoryAvailableBytes",
            "dependsOn": [
              "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
            ],
            "kind": "WindowsPerformanceCounter",
            "properties": {
              "objectName": "Memory",
              "instanceName": "*",
              "intervalSeconds": 10,
              "counterName": "Available MBytes "
            }
          },
          {
            "apiVersion": "2020-08-01",
            "type": "datasources",
            "name": "WindowsPerfMemoryPercentageBytes",
            "dependsOn": [
              "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
            ],
            "kind": "WindowsPerformanceCounter",
            "properties": {
              "objectName": "Memory",
              "instanceName": "*",
              "intervalSeconds": 10,
              "counterName": "% Committed Bytes in Use"
            }
          },
          {
            "apiVersion": "2020-08-01",
            "type": "datasources",
            "name": "WindowsPerfProcessorPercentage",
            "dependsOn": [
              "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
            ],
            "kind": "WindowsPerformanceCounter",
            "properties": {
              "objectName": "Processor",
              "instanceName": "_Total",
              "intervalSeconds": 10,
              "counterName": "% Processor Time"
            }
          }
        ]
      }
    ]
}

```

### <a name="parameter-file"></a>Paraméter fájlja

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "value": "MyWorkspace"
    },
    "location": {
      "value": "eastus"
    }
  }
}
```


## <a name="collect-linux-performance-counters"></a>Linux-teljesítményszámlálók gyűjtése
Az alábbi példa egy meglévő munkaterülethez tartozó [Linux](../platform/data-sources-performance-counters.md) -teljesítményszámlálók gyűjteményét adja hozzá.

### <a name="notes"></a>Jegyzetek

- Vegyen fel egy **adatforrást** az egyes objektumok és példányok összegyűjtéséhez. Különböző számlálókat adhat meg az egyes objektumok és példányok kombinációja számára, de csak egyetlen díjat adhat meg az összes számlálóhoz.
  
### <a name="template-file"></a>Sablonfájl

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
              "description": "Name of the workspace."
            }
        },
        "location": {
          "type": "string",
          "metadata": {
            "description": "Specifies the location in which to create the workspace."
          }
        }
    },
    "resources": [
    {
        "apiVersion": "2020-08-01",
        "type": "Microsoft.OperationalInsights/workspaces",
        "name": "[parameters('workspaceName')]",
        "location": "[parameters('location')]",
        "resources": [
            {
                "apiVersion": "2020-08-01",
                "type": "datasources",
                "name": "LinuxPerformanceLogicalDisk",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                ],
                "kind": "LinuxPerformanceObject",
                "properties": {
                    "objectName": "Logical Disk",
                    "instanceName": "*",
                    "intervalSeconds": 10,
                    "performanceCounters": [
                        {
                            "counterName": "% Used Inodes"
                        },
                        {
                            "counterName": "Free Megabytes"
                        },
                        {
                            "counterName": "% Used Space"
                        },
                        {
                            "counterName": "Disk Transfers/sec"
                        },
                        {
                            "counterName": "Disk Reads/sec"
                        },
                        {
                            "counterName": "Disk Writes/sec"
                        }
                    ]
                }
            },
            {
                "apiVersion": "2020-08-01",
                "type": "datasources",
                "name": "LinuxPerformanceProcessor",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                ],
                "kind": "LinuxPerformanceObject",
                "properties": {
                    "objectName": "Processor",
                    "instanceName": "*",
                    "intervalSeconds": 10,
                    "performanceCounters": [
                        {
                            "counterName": "% Processor Time"
                        },
                        {
                            "counterName": "% Privileged Time"
                        }
                    ]
                }
            }
        ]
      }
    ]
}
```

### <a name="parameter-file"></a>Paraméter fájlja

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "value": "MyWorkspace"
    },
    "location": {
      "value": "eastus"
    }
  }
}
```


## <a name="collect-custom-logs"></a>Egyéni naplók gyűjtése
Az alábbi minta [egyéni naplók](../platform/data-sources-custom-logs.md) gyűjteményét adja hozzá egy meglévő munkaterülethez.

### <a name="notes"></a>Jegyzetek

- Az elválasztó karakterek és a kinyerések konfigurálása összetett lehet. Ha segítségre van szüksége, egyéni naplót határozhat meg a Azure Portal használatával, valamint a konfiguráció lekérése a [Get-AzOperationalInsightsDataSource](/powershell/module/az.operationalinsights/get-azoperationalinsightsdatasource) és a **-Kind** beállítás használatával a **szokás**.

  
### <a name="template-file"></a>Sablonfájl

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "workspaceName": {
          "type": "string",
          "metadata": {
            "description": "Name of the workspace."
          }
      },
      "location": {
        "type": "string",
        "metadata": {
          "description": "Specifies the location in which to create the workspace."
        }
      }
  },
  "resources": [
  {
      "apiVersion": "2020-08-01",
      "type": "Microsoft.OperationalInsights/workspaces",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "resources": [
        {
            "apiVersion": "2020-08-01",
            "type": "dataSources",
            "name": "[concat(parameters('workspaceName'), 'armlog_timedelimited')]",
            "dependsOn": [
                "[concat('Microsoft.OperationalInsights/workspaces/', '/', parameters('workspaceName'))]"
            ],
            "kind": "CustomLog",
            "properties": {
                "customLogName": "arm_log_timedelimited",
                "description": "this is a description",
                "inputs": [
                  {
                      "location": {
                        "fileSystemLocations": {
                            "linuxFileTypeLogPaths": [ "/var/logs" ],
                            "windowsFileTypeLogPaths": ["c:\\Windows\\Logs\\*.txt"]
                        }
                      },
                      "recordDelimiter": {
                        "regexDelimiter": {
                          "matchIndex": 0,
                          "numberdGroup": null,
                          "pattern": "(^.*((\\d{2})|(\\d{4}))-([0-1]\\d)-(([0-3]\\d)|(\\d))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9].*$)"
                        }
                      }
                  }
                ],
                "extractions": [
                {
                    "extractionName": "TimeGenerated",
                    "extractionProperties": {
                    "dateTimeExtraction": {
                        "regex": [
                          {
                              "matchIndex": 0,
                              "numberdGroup": null,
                              "pattern": "((\\d{2})|(\\d{4}))-([0-1]\\d)-(([0-3]\\d)|(\\d))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9]"
                          }
                        ]
                    }
                    },
                    "extractionType": "DateTime"
                }
                ]
            }
        },
        {
          "apiVersion": "2020-08-01",
          "type": "dataSources",
          "name": "[concat(parameters('workspaceName'), 'armlog_newline')]",
          "dependsOn": [
              "[concat('Microsoft.OperationalInsights/workspaces/', '/', parameters('workspaceName'))]"
          ],
          "kind": "CustomLog",
          "properties": {
              "customLogName": "armlog_newline",
              "description": "this is a description",
              "inputs": [
                {
                    "location": {
                      "fileSystemLocations": {
                          "linuxFileTypeLogPaths": [ "/var/logs" ],
                          "windowsFileTypeLogPaths": ["c:\\Windows\\Logs\\*.txt"]
                      }
                    },
                    "recordDelimiter": {
                      "regexDelimiter": {
                        "pattern": "\\n",
                        "matchIndex": 0,
                        "numberdGroup": null
                      }
                    }
                }
              ],
              "extractions": [
                {
                  "extractionName": "TimeGenerated",
                  "extractionType": "DateTime",
                  "extractionProperties": {
                    "dateTimeExtraction": {
                        "regex": null,
                        "joinStringRegex": null
                    }
                  }
                }
              ]
          }
        }
      ]
    }
  ]
}

```

### <a name="parameter-file"></a>Paraméter fájlja

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "value": "MyWorkspace"
    },
    "location": {
      "value": "eastus"
    }
  }
}
```


## <a name="collect-iis-log"></a>IIS-napló gyűjtése
Az alábbi minta egy meglévő munkaterülethez hozzáadja az [IIS-naplók](../platform/data-sources-iis-logs.md) gyűjteményét.

### <a name="template-file"></a>Sablonfájl

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
              "description": "Name of the workspace."
            }
        },
        "location": {
          "type": "string",
          "metadata": {
            "description": "Specifies the location in which to create the workspace."
          }
        }
    },
    "resources": [
    {
        "type": "Microsoft.OperationalInsights/workspaces",
        "apiVersion": "2020-08-01",
        "name": "[parameters('workspaceName')]",
        "location": "[parameters('location')]",
        "resources": [
            {
                "apiVersion": "2020-08-01",
                "type": "datasources",
                "name": "IISLog",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                ],
                "kind": "IISLogs",
                "properties": {
                    "state": "OnPremiseEnabled"
                }
            }
        ]
      }
    ]
}

```

### <a name="parameter-file"></a>Paraméter fájlja

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "value": "MyWorkspace"
    },
    "location": {
      "value": "eastus"
    }
  }
}
```



## <a name="next-steps"></a>Következő lépések

* [További Azure monitor-sablonok beszerzése](resource-manager-samples.md).
* [További információ a log Analytics munkaterületekről](../learn/quick-create-workspace.md).
* [További információ az ügynök adatforrásairól](../platform/agent-data-sources.md).
