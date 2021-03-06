---
title: Copiar dados de Presto utilizando o Azure Data Factory (Beta) | Microsoft Docs
description: Saiba como copiar dados de Presto aos arquivos de dados dependente suportados através da utilização de uma atividade de cópia no pipeline Azure Data Factory.
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
ms.date: 04/26/2017
ms.author: jingwang
ms.openlocfilehash: e0d801dbb8060cf2fa175086fc5324f444870412
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2018
---
# <a name="copy-data-from-presto-using-azure-data-factory-beta"></a>Copiar dados de Presto utilizando o Azure Data Factory (Beta)

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados de Presto. Baseia-se no [copiar descrição geral da atividade](copy-activity-overview.md) artigo que apresenta uma descrição geral da atividade de cópia.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em pré-visualização. Se estiver a utilizar a versão 1 do serviço do Data Factory, o que é geralmente disponível (DG), consulte [atividade de cópia no V1](v1/data-factory-data-movement-activities.md).

> [!IMPORTANT]
> Este conector está atualmente na versão Beta. Pode experimentar e envie-nos comentários. Não utilizá-lo em ambientes de produção.

## <a name="supported-capabilities"></a>Capacidades suportadas

Pode copiar dados de Presto para qualquer arquivo de dados suportados sink. Para obter uma lista dos arquivos de dados que são suportados como origens/sinks pela atividade de cópia, consulte o [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

O Azure Data Factory fornece um controlador incorporado para ativar a conetividade, pelo que não precisa de instalar manualmente o controlador de utilizar este conector.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started-2](../../includes/data-factory-v2-connector-get-started-2.md)]

As secções seguintes fornecem detalhes sobre as propriedades que são utilizados para definir o conector Presto entidades do Data Factory específicas.

## <a name="linked-service-properties"></a>Propriedades de serviço ligado

As seguintes propriedades são suportadas para o serviço ligado Presto:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo tem de ser definida: **Presto** | Sim |
| anfitrião | O endereço IP ou anfitrião nome do servidor Presto. (ou seja, 192.168.222.160)  | Sim |
| serverVersion | A versão do servidor Presto. (ou seja, 0.148-t)  | Sim |
| catálogo | O contexto de catálogo para todos os pedidos do servidor.  | Sim |
| porta | A porta TCP que o servidor Presto utiliza para escutar ligações de cliente. O valor predefinido é 8080.  | Não |
| authenticationType | O mecanismo de autenticação utilizado para ligar ao servidor Presto. <br/>Valores permitidos são: **anónimo**, **LDAP** | Sim |
| o nome de utilizador | O nome de utilizador utilizado para ligar ao servidor Presto.  | Não |
| palavra-passe | A palavra-passe correspondente ao nome de utilizador. Marcar este campo como um SecureString armazena de forma segura na fábrica de dados, ou [referenciar um segredo armazenado no Cofre de chaves do Azure](store-credentials-in-key-vault.md). | Não |
| enableSsl | Especifica se as ligações ao servidor são encriptadas com SSL. O valor predefinido é falso.  | Não |
| trustedCertPath | O caminho completo do ficheiro. pem que contém os certificados de AC fidedigna para verificar o servidor ao ligar através de SSL. Esta propriedade só pode ser definida quando a utilizar SSL no IR. personalizada alojada O valor predefinido é o ficheiro de cacerts.pem instalado com o IR.  | Não |
| useSystemTrustStore | Especifica se deve utilizar um certificado de AC a partir do arquivo de confiança de sistema ou a partir de um ficheiro PEM especificado. O valor predefinido é falso.  | Não |
| allowHostNameCNMismatch | Especifica se requer um nome de certificado SSL emitidos pela AC para corresponder ao nome do anfitrião do servidor ao ligar através de SSL. O valor predefinido é falso.  | Não |
| allowSelfSignedServerCert | Especifica se pretende permitir que os certificados autoassinados do servidor. O valor predefinido é falso.  | Não |
| timeZoneID | O local fuso horário utilizado pela ligação. Os valores válidos para esta opção estão especificados na base de dados de fuso horário da IANA. O valor predefinido é o fuso horário do sistema.  | Não |

**Exemplo:**

```json
{
    "name": "PrestoLinkedService",
    "properties": {
        "type": "Presto",
        "typeProperties": {
            "host" : "<host>",
            "serverVersion" : "0.148-t",
            "catalog" : "<catalog>",
            "port" : "<port>",
            "authenticationType" : "LDAP",
            "username" : "<username>",
            "password": {
                 "type": "SecureString",
                 "value": "<password>"
            },
            "timeZoneID" : "Europe/Berlin"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para uma lista completa das secções e propriedades disponíveis para definir os conjuntos de dados, consulte o [conjuntos de dados](concepts-datasets-linked-services.md) artigo. Esta secção fornece uma lista de propriedades suportadas por Presto conjunto de dados.

Para copiar dados de Presto, defina a propriedade de tipo do conjunto de dados para **PrestoObject**. Não há nenhuma propriedade de tipo específicas adicional neste tipo de conjunto de dados.

**Exemplo**

```json
{
    "name": "PrestoDataset",
    "properties": {
        "type": "PrestoObject",
        "linkedServiceName": {
            "referenceName": "<Presto linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para uma lista completa das secções e propriedades disponíveis para definir as atividades, consulte o [Pipelines](concepts-pipelines-activities.md) artigo. Esta secção fornece uma lista de propriedades suportadas por origem Presto.

### <a name="prestosource-as-source"></a>PrestoSource como origem

Para copiar dados de Presto, defina o tipo de origem na atividade de cópia para **PrestoSource**. As seguintes propriedades são suportadas na atividade de cópia **origem** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo da origem de atividade de cópia tem de ser definida: **PrestoSource** | Sim |
| consulta | Utilize a consulta SQL personalizada para ler os dados. Por exemplo: `"SELECT * FROM MyTable"`. | Sim |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromPresto",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Presto input dataset name>",
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
                "type": "PrestoSource",
                "query": "SELECT * FROM MyTable"
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
