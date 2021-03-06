---
title: Azure IoT Edge モジュールの構成 | Microsoft Docs
description: Azure IoT Edge モジュールを構成する内容と、それらをどのように再利用できるかについて説明します。
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 03/23/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 3d95a602815cd444fb4b062853d9d31b75993e6a
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/14/2018
---
# <a name="understand-how-iot-edge-modules-can-be-used-configured-and-reused---preview"></a>IoT Edge モジュールをどのように使用、構成、および再利用できるかを理解する - プレビュー

各 IoT Edge デバイスは、少なくとも 2 つのモジュール $edgeAgent と $edgeHub を実行します。IoT Edge ランタイムはこのモジュールによって構成されます。 この 2 つの標準のモジュールに加えて、IoT Edge デバイスでは複数のモジュールを実行して、任意の数のプロセスを実行できます。 デバイスにすべてのモジュールを一度にデプロイする場合、含めるモジュール、およびモジュール同士の連携の仕方について宣言する方法が必要です。 

*デプロイ マニフェスト*は、次の内容が記述された JSON ドキュメントです。

* デプロイする必要がある IoT Edge モジュールと、その作成および管理オプション。
* Edge ハブの構成 (モジュール間および最終的には IoT Hub へのメッセージのフロー方法を含む)。
* オプションで、モジュール ツインの必要なプロパティに設定したり、個々のモジュール アプリケーションを構成したりするための値。

すべての IoT Edge デバイスをデプロイ マニフェストで構成する必要があります。 新しくインストールされた IoT Edge ランタイムは、有効なマニフェストで構成されるまでエラー コードを報告します。 

Azure IoT Edge チュートリアルでは、Azure IoT Edge ポータルでウィザードを使用することによってデプロイ マニフェストを作成します。 また、REST または IoT Hub サービス SDK を使用して、プログラムでデプロイ マニフェストを適用することもできます。 IoT Edge デプロイの詳細については、「[Deploy and monitor (デプロイおよび監視)][lnk-deploy]」を参照してください。

## <a name="create-a-deployment-manifest"></a>配置マニフェストの作成

高いレベルでは、配置マニフェストは、IoT Edge デバイスにデプロイされた IoT Edge モジュールのモジュール ツインの必要なプロパティを構成します。 これらのモジュールのうちの 2 つである Edge エージェントと Edge ハブは常に存在します。

IoT Edge ランタイム (エージェントとハブ) のみが含まれた配置マニフェストは有効です。

マニフェストは、この構造に従います。

```json
{
    "moduleContent": {
        "$edgeAgent": {
            "properties.desired": {
                // desired properties of the Edge agent
                // includes the image URIs of all modules
            }
        },
        "$edgeHub": {
            "properties.desired": {
                // desired properties of the Edge hub
                // includes the routing information between modules, and to IoT Hub
            }
        },
        "{module1}": {  // optional
            "properties.desired": {
                // desired properties of module with id {module1}
            }
        },
        "{module2}": {  // optional
            ...
        },
        ...
    }
}
```

## <a name="configure-modules"></a>モジュールの構成

デプロイするモジュールに必要なプロパティを確立するのに加えて、それらをインストールする方法を IoT Edge ランタイムに指示する必要があります。 すべてのモジュールの構成と管理情報は、**$edgeAgent** の必要なプロパティに含めます。 この情報には、Edge エージェント自体の構成パラメーターが含まれます。 

含めることができるプロパティおよび必須プロパティの完全な一覧については、[Edge エージェントおよび Edge ハブのプロパティ](module-edgeagent-edgehub.md)に関するページをご覧ください。

$EdgeAgent プロパティは次の構造に従います。

```json
"$edgeAgent": {
    "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
        },
        "systemModules": {
            "edgeAgent": {
                // configuration and management details
            },
            "edgeHub": {
                // configuration and management details
            }
        },
        "modules": {
            "{module1}": { //optional
                // configuration and management details
            },
            "{module2}": { // optional
                // configuration and management details
            }
        }
    }
},
```

## <a name="declare-routes"></a>ルートの宣言

Edge ハブは、モジュール間およびモジュールと IoT Hub の間でメッセージを宣言的にルーティングするための方法を提供します。 Edge ハブがすべての通信を管理するため、ルート情報は **$edgeHub** の必要なプロパティに含めます。 同じデプロイ内にルートを複数持たせることができます。

ルートは、**$edgeHub** の必要なプロパティで、次の構文を使用して宣言します。

```json
"$edgeHub": {
    "properties.desired": {
        "routes": {
            "{route1}": "FROM <source> WHERE <condition> INTO <sink>",
            "{route2}": "FROM <source> WHERE <condition> INTO <sink>"
        },
    }
}
```

ソースとシンクはすべてのルートに必要ですが、条件は、メッセージをフィルター処理するために使用できる省略可能な部分です。 


### <a name="source"></a>ソース
ソースでは、メッセージがどこから送信されるのかを指定します。 次のいずれかの値を指定できます。

| ソース | [説明] |
| ------ | ----------- |
| `/*` | 任意のデバイスまたはモジュールからの、デバイスからクラウドへのすべてのメッセージ |
| `/messages/*` | 何らかの出力と共に、または出力なしでデバイスまたはモジュールによって送信された、デバイスからクラウドへの任意のメッセージ |
| `/messages/modules/*` | 何らかの出力と共に、または出力なしでモジュールによって送信された、デバイスからクラウドへの任意のメッセージ |
| `/messages/modules/{moduleId}/*` | 出力なしで {moduleId} によって送信された、デバイスからクラウドへの任意のメッセージ |
| `/messages/modules/{moduleId}/outputs/*` | 何らかの出力と共に {moduleId} によって送信された、デバイスからクラウドへの任意のメッセージ |
| `/messages/modules/{moduleId}/outputs/{output}` | {output} を使用して {moduleId} によって送信された、デバイスからクラウドへの任意のメッセージ |

### <a name="condition"></a>条件
条件は、ルートの宣言では省略可能です。 シンクからソースへのメッセージをすべて渡す場合は、**WHERE** 句全体をそのまま削除します。 または、[IoT Hub クエリ言語][lnk-iothub-query] を使用して、条件を満たす特定のメッセージまたはメッセージの種類をフィルター処理することができます。

IoT Edge のモジュール間を通過するメッセージは、デバイスと Azure IoT Hub の間を通過するメッセージと同じ形式になります。 すべてのメッセージは JSON で書式設定され、パラメーターとして **systemProperties**、**appProperties**、**body** が与えられます。 

3 つすべてのパラメーターでクエリを構築できます。次の構文を利用します。 

* システム プロパティ: `$<propertyName>` または `{$<propertyName>}`
* アプリケーション プロパティ: `<propertyName>`
* 本文プロパティ: `$body.<propertyName>` 

メッセージ プロパティのクエリを作成する方法の例は、「[デバイスからクラウドへのメッセージ ルートのクエリ式](../iot-hub/iot-hub-devguide-query-language.md#device-to-cloud-message-routes-query-expressions)」を参照してください。

IoT Edge に固有の例としては、たとえば、リーフ デバイスからゲートウェイ デバイスに到着したメッセージにフィルターを適用します。 モジュールから送信されるメッセージには、**connectionModuleId** と呼ばれるシステム プロパティが含まれます。 したがって、リーフ デバイスからメッセージを直接 IoT Hub にルーティングする場合は、次のルートを使用してモジュールのメッセージを除外します。

```sql
FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO $upstream
```

### <a name="sink"></a>シンク
シンクでは、メッセージの送信先が定義されます。 次のいずれかの値を指定できます。

| シンク | [説明] |
| ---- | ----------- |
| `$upstream` | IoT Hub にメッセージを送信する |
| `BrokeredEndpoint("/modules/{moduleId}/inputs/{input}")` | モジュール `{moduleId}` の入力 `{input}` にメッセージを送信する |

Edge ハブが少なくとも 1 つの保証を提供することに注意することが重要です。つまり、ルートがそのシンクにメッセージを配信できない場合 (たとえば、Edge ハブが IoT Hub に接続できない場合や、ターゲット モジュールが接続されていない場合)、メッセージはローカルに格納されます。

Edge ハブでは、[Edge ハブの必要なプロパティ](module-edgeagent-edgehub.md)の `storeAndForwardConfiguration.timeToLiveSecs` プロパティで指定した時間まで、メッセージが格納されます。

## <a name="define-or-update-desired-properties"></a>必要なプロパティの定義または更新 

配置マニフェストでは、IoT Edge デバイスにデプロイされた各モジュールのモジュール ツインに対して必要なプロパティを指定できます。 必要なプロパティがデプロイ マニフェストで指定されている場合、それらは現在モジュール ツインにある必要なプロパティをすべて上書きします。

デプロイ マニフェストでモジュール ツインの必要なプロパティを指定しない場合、IoT Hub はモジュール ツインをどのような方法でも変更しないため、ユーザーは必要なプロパティをプログラムで設定できます。

デバイス ツインを変更できるのと同じメカニズムを使用してモジュール ツインを変更できます。 詳しくは、[デバイス ツインの開発者ガイド](../iot-hub/iot-hub-devguide-device-twins.md)に関するページをご覧ください。   

## <a name="deployment-manifest-example"></a>デプロイ マニフェストの例

これは、デプロイ マニフェストの JSON ドキュメントの例です。

```json
{
"moduleContent": {
    "$edgeAgent": {
        "properties.desired": {
            "schemaVersion": "1.0",
            "runtime": {
                "type": "docker",
                "settings": {
                    "minDockerVersion": "v1.25",
                    "loggingOptions": ""
                }
            },
            "systemModules": {
                "edgeAgent": {
                    "type": "docker",
                    "settings": {
                    "image": "microsoft/azureiotedge-agent:1.0-preview",
                    "createOptions": ""
                    }
                },
                "edgeHub": {
                    "type": "docker",
                    "status": "running",
                    "restartPolicy": "always",
                    "settings": {
                    "image": "microsoft/azureiotedge-hub:1.0-preview",
                    "createOptions": ""
                    }
                }
            },
            "modules": {
                "tempSensor": {
                    "version": "1.0",
                    "type": "docker",
                    "status": "running",
                    "restartPolicy": "always",
                    "settings": {
                    "image": "microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview",
                    "createOptions": "{}"
                    }
                },
                "filtermodule": {
                    "version": "1.0",
                    "type": "docker",
                    "status": "running",
                    "restartPolicy": "always",
                    "settings": {
                    "image": "myacr.azurecr.io/filtermodule:latest",
                    "createOptions": "{}"
                    }
                }
            }
        }
    },
    "$edgeHub": {
        "properties.desired": {
            "schemaVersion": "1.0",
            "routes": {
                "sensorToFilter": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
                "filterToIoTHub": "FROM /messages/modules/filtermodule/outputs/output1 INTO $upstream"
            },
            "storeAndForwardConfiguration": {
                "timeToLiveSecs": 10
            }
        }
    }
}
}
```

## <a name="next-steps"></a>次の手順

* $edgeAgent および $edgeHub に含めることができるプロパティおよび含める必要があるプロパティの完全な一覧については、[Edge エージェントおよび Edge ハブのプロパティ](module-edgeagent-edgehub.md)に関するページをご覧ください。

* これで IoT Edge モジュールがどのように使用されるかがわかったので、「[Understand the requirements and tools for developing IoT Edge modules (IoT Edge モジュールを開発するための要件およびツールを理解する)][lnk-module-dev]」に進みます。

[lnk-deploy]: module-deployment-monitoring.md
[lnk-iothub-query]: ../iot-hub/iot-hub-devguide-query-language.md
[lnk-docker-create-options]: https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate
[lnk-docker-logging-options]: https://docs.docker.com/engine/admin/logging/overview/
[lnk-module-dev]: module-development.md
