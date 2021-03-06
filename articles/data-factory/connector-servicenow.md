---
title: Copiar dados de ServiceNow utilizando o Azure Data Factory | Microsoft Docs
description: Saiba como copiar dados de ServiceNow aos arquivos de dados dependente suportados através da utilização de uma atividade de cópia no pipeline Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: jingwang
ms.openlocfilehash: 29fb74e49ad3fdca0bc54a431da40b02ef24882b
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2018
---
# <a name="copy-data-from-servicenow-using-azure-data-factory"></a>Copiar dados de ServiceNow utilizando o Azure Data Factory

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados de ServiceNow. Baseia-se no [copiar descrição geral da atividade](copy-activity-overview.md) artigo que apresenta uma descrição geral da atividade de cópia.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em pré-visualização. Se estiver a utilizar a versão 1 do serviço do Data Factory, o que é geralmente disponível (DG), consulte [atividade de cópia no V1](v1/data-factory-data-movement-activities.md).

## <a name="supported-capabilities"></a>Capacidades suportadas

Pode copiar dados de ServiceNow para qualquer arquivo de dados suportados sink. Para obter uma lista dos arquivos de dados que são suportados como origens/sinks pela atividade de cópia, consulte o [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

O Azure Data Factory fornece um controlador incorporado para ativar a conetividade, pelo que não precisa de instalar manualmente o controlador de utilizar este conector.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started-2](../../includes/data-factory-v2-connector-get-started-2.md)]

As secções seguintes fornecem detalhes sobre as propriedades que são utilizados para definir entidades do Data Factory específicas para o conector do ServiceNow.

## <a name="linked-service-properties"></a>Propriedades de serviço ligado

As seguintes propriedades são suportadas para o serviço ligado do ServiceNow:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo tem de ser definida: **ServiceNow** | Sim |
| endpoint | O ponto final do servidor do ServiceNow (`http://<instance>.service-now.com`).  | Sim |
| authenticationType | O tipo de autenticação a utilizar. <br/>Valores permitidos são: **básico**, **OAuth2** | Sim |
| o nome de utilizador | O nome de utilizador utilizado para ligar ao servidor do ServiceNow para autenticação básica e OAuth2.  | Não |
| palavra-passe | A palavra-passe correspondente ao nome de utilizador para a autenticação básica e OAuth2. Marcar este campo como um SecureString armazena de forma segura na fábrica de dados, ou [referenciar um segredo armazenado no Cofre de chaves do Azure](store-credentials-in-key-vault.md). | Não |
| clientId | O ID de cliente de autenticação OAuth2.  | Não |
| clientSecret | O segredo do cliente para autenticação de OAuth2. Marcar este campo como um SecureString armazena de forma segura na fábrica de dados, ou [referenciar um segredo armazenado no Cofre de chaves do Azure](store-credentials-in-key-vault.md). | Não |
| useEncryptedEndpoints | Especifica se os pontos finais de origem de dados são encriptados através de HTTPS. O valor predefinido é verdadeiro.  | Não |
| useHostVerification | Especifica se o nome de anfitrião no certificado do servidor para fazer corresponder o nome de anfitrião do servidor ao ligar através de SSL. O valor predefinido é verdadeiro.  | Não |
| usePeerVerification | Especifica se pretende verificar a identidade do servidor ao ligar através de SSL. O valor predefinido é verdadeiro.  | Não |

**Exemplo:**

```json
{
    "name": "ServiceNowLinkedService",
    "properties": {
        "type": "ServiceNow",
        "typeProperties": {
            "endpoint" : "http://<instance>.service-now.com",
            "authenticationType" : "Basic",
            "username" : "<username>",
            "password": {
                 "type": "SecureString",
                 "value": "<password>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para uma lista completa das secções e propriedades disponíveis para definir os conjuntos de dados, consulte o [conjuntos de dados](concepts-datasets-linked-services.md) artigo. Esta secção fornece uma lista de propriedades suportadas por conjunto de dados do ServiceNow.

Para copiar dados de ServiceNow, defina a propriedade de tipo do conjunto de dados para **ServiceNowObject**. Não há nenhuma propriedade de tipo específicas adicional neste tipo de conjunto de dados.

**Exemplo**

```json
{
    "name": "ServiceNowDataset",
    "properties": {
        "type": "ServiceNowObject",
        "linkedServiceName": {
            "referenceName": "<ServiceNow linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para uma lista completa das secções e propriedades disponíveis para definir as atividades, consulte o [Pipelines](concepts-pipelines-activities.md) artigo. Esta secção fornece uma lista de propriedades suportado pela origem de ServiceNow.

### <a name="servicenow-as-source"></a>ServiceNow como origem

Para copiar dados de ServiceNow, defina o tipo de origem na atividade de cópia para **ServiceNowSource**. As seguintes propriedades são suportadas na atividade de cópia **origem** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo da origem de atividade de cópia tem de ser definida: **ServiceNowSource** | Sim |
| consulta | Utilize a consulta SQL personalizada para ler os dados. Por exemplo: `"SELECT * FROM Actual.alm_asset"`. | Sim |

Quando especificar o esquema e a coluna para ServiceNow na consulta, tenha em atenção o seguinte:

- **Esquema:** especificar o esquema como `Actual` ou `Display` na consulta de ServiceNow, pode examiná-lo como o parâmetro de `sysparm_display_value` como VERDADEIRO ou falso quando chamar [ServiceNow restful APIs](https://developer.servicenow.com/app.do#!/rest_api_doc?v=jakarta&id=r_AggregateAPI-GET). 
- **Coluna:** o nome da coluna para o valor real em `Actual` esquema é `[columne name]_value`, enquanto para o valor de apresentação em `Display` esquema é `[columne name]_display_value`. Tenha em atenção o nome da coluna tem de mapear para o esquema que está a ser utilizado na consulta.

**Consulta de exemplo:** 
 `SELECT col_value FROM Actual.alm_asset` ou `SELECT col_display_value FROM Display.alm_asset`

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromServiceNow",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ServiceNow input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "ServiceNowSource",
                "query": "SELECT * FROM Actual.alm_asset"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>Passos Seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks pela atividade de cópia no Azure Data Factory, consulte [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats).
