---
title: Continuamente integrar e desenvolver com ferramentas do Stream Analytics
description: Este artigo descreve como utilizar as ferramentas do Visual Studio para o Azure Stream Analytics para configurar um processo de implementação e a integração contínua.
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 9/27/2017
ms.openlocfilehash: e4e831c602255df66f4c86ffa17336f51d2b52f7
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2018
---
# <a name="continuously-integrate-and-develop-with-stream-analytics-tools"></a>Continuamente integrar e desenvolver com ferramentas do Stream Analytics
Este artigo descreve como utilizar para utilizar as ferramentas do Azure Stream Analytics para o Visual Studio para configurar um processo de integração e a implementação contínuo.

Versão de utilização 2.3.0000.0 ou acima do [ferramentas do Stream Analytics para o Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio) obter suporte do MSBuild.

Um pacote NuGet está disponível: [Microsoft.Azure.Stream Analytics.CICD](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/). Fornece o MSBuild, execução local e ferramentas de implementação que suportam o processo de integração e a implementação contínuo de projetos do Visual Studio do Stream Analytics. 
> [!NOTE] 
O pacote NuGet pode ser utilizado apenas com o 2.3.0000.0 ou acima versão do Stream Analytics Tools para Visual Studio. Se tiver projetos criados em versões anteriores das ferramentas do Visual Studio, basta abri-los com o 2.3.0000.0 ou superior a versão e guardar. Em seguida, as novas funcionalidades estão ativadas. 

Para obter mais informações, consulte [ferramentas do Stream Analytics para o Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio).

## <a name="msbuild"></a>MSBuild
Como a experiência de Visual Studio MSBuild padrão, para criar um projeto tem duas opções. Clique com o botão direito no projeto e, em seguida, escolha **criar**. Também pode utilizar **MSBuild** no pacote NuGet na linha de comandos.
```
./build/msbuild /t:build [Your Project Full Path] /p:CompilerTaskAssemblyFile=Microsoft.WindowsAzure.StreamAnalytics.Common.CompileService.dll  /p:ASATargetsFilePath="[NuGet Package Local Path]\build\StreamAnalytics.targets"

```

Quando um projeto do Visual Studio do Stream Analytics baseia-se com êxito, gera os seguintes ficheiros de modelo de Gestor de recursos do Azure dois sob o **bin / [depuração/revenda] / implementar** pasta: 

*  Ficheiro de modelo do Resource Manager

       [ProjectName].JobTemplate.json 

*  Ficheiro de parâmetros do Gestor de recursos

       [ProjectName].JobTemplate.parameters.json   

Os parâmetros de predefinição no ficheiro Parameters. JSON são das definições no seu projeto de Visual Studio. Se pretender implementar para outro ambiente, substitua os parâmetros em conformidade.

> [!NOTE] 
Para todas as credenciais, os valores predefinidos são definidos como nulo. É *necessário* definir os valores antes de implementar para a nuvem.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```
Saiba mais sobre como [implementar com um ficheiro de modelo do Resource Manager e o Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy). Saiba mais sobre como [utilizar um objeto como um parâmetro num modelo do Resource Manager](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/objects-as-parameters).


## <a name="command-line-tool"></a>Ferramenta de linha de comandos

### <a name="build-the-project"></a>Compilar o projeto
O pacote NuGet tem uma ferramenta da linha de comandos denominada **SA.exe**. Suporta a compilação do projeto e testar local numa máquina arbitrária, que pode utilizar a integração contínua e o processo de entrega contínua. 

Por predefinição, os ficheiros de implementação são colocados sob o diretório atual. Pode especificar o caminho de saída utilizando o parâmetro - OutputPath seguintes:

```
./tools/SA.exe build -Project [Your Project Full Path] [-OutputPath <outputPath>] 
```

### <a name="test-the-script-locally"></a>Testar o script localmente

Se o projeto tiver especificado os ficheiros de entrada locais no Visual Studio, pode executar um teste de script automático, utilizando o *localrun* comando. O resultado de saída é colocado sob o diretório atual.
 
```
localrun -Project [ProjectFullPath]
```

### <a name="generate-a-job-definition-file-to-use-with-the-stream-analytics-powershell-api"></a>Gerar um ficheiro de definição de tarefa para utilizar com a API de PowerShell do Stream Analytics

O *arm* comando obtém o modelo de tarefa e os ficheiros de parâmetro de modelo de tarefa gerados através de compilação como entrada. Em seguida,-combina-las num ficheiro de JSON de definição de tarefa que pode ser utilizado com a API de PowerShell do Stream Analytics.

```
arm -JobTemplate <templateFilePath> -JobParameterFile <jobParameterFilePath> [-OutputFile <asaArmFilePath>]
```
Exemplo:
```
./tools/SA.exe arm -JobTemplate "ProjectA.JobTemplate.json" -JobParameterFile "ProjectA.JobTemplate.parameters.json" -OutputFile "JobDefinition.json" 
```


