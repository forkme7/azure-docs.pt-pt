---
title: Ferramentas de desenvolvimento de Máquina Virtual de ciência de dados - Azure | Microsoft Docs
description: Ferramentas de desenvolvimento de Máquina Virtual de ciência do dados.
keywords: ferramentas de ciência de dados, a máquina de virtual de ciência de dados, as ferramentas de ciência de dados, ciência de dados do linux
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: gokuma
ms.openlocfilehash: b8b0b8934b51080c3583281673183c1498c26417
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2018
---
# <a name="development-tools-on-the-data-science-virtual-machine"></a>Ferramentas de desenvolvimento na máquina de Virtual de ciência de dados

A Máquina Virtual de ciência de dados (DSVM) disponibiliza um ambiente produtivo para o desenvolvimento por agrupamento várias ferramentas populares e IDE. Seguem-se algumas ferramentas que são fornecidas no DSVM. 

## <a name="visual-studio-2017"></a>Visual Studio 2017  
|    |           |
| ------------- | ------------- |
| O que é?   | Objetivo geral IDE      |
| Versões suportadas DSVM      | Windows      |
| Utilizações comuns      | Desenvolvimento de software    |
| Como é mesmo configurado / instalado o DSVM?      | Carga de trabalho de ciência de dados (ferramentas de Python e R), do Azure carga de trabalho (Hadoop, Data Lake), Node.js, ferramentas do SQL Server, [Visual Studio Tools para AI](https://github.com/Microsoft/vs-tools-for-ai)    |
| Como utilizar / executá-lo?      | Atalho do ambiente de trabalho (`C:\Program Files (x86)\Microsoft Visual Studio\2017\Community\Common7\IDE\devenv.exe`)    |
| Ferramentas relacionadas no DSVM      |     Visual Studio Code, RStudio, Juno  |

## <a name="visual-studio-code"></a>Visual Studio Code 
|    |           |
| ------------- | ------------- |
| O que é?   | Objetivo geral IDE      |
| Versões suportadas DSVM      | Windows, Linux     |
| Utilizações comuns      | Editor de código e a integração de Git   |
| Como utilizar / executá-lo?      | Atalho do ambiente de trabalho (`C:\Program Files (x86)\Microsoft VS Code\Code.exe`) no Windows, de atalho do ambiente de trabalho ou de terminal (`code`) no Linux    |
| Ferramentas relacionadas no DSVM      |     Visual Studio 2017, RStudio, Juno  |

## <a name="rstudio--desktop"></a>Ambiente de trabalho do RStudio 
|    |           |
| ------------- | ------------- |
| O que é?   | Cliente IDE para R    |
| Versões suportadas DSVM      | Windows, Linux      |
| Utilizações comuns      |  Desenvolvimento de R     |
| Como utilizar / executá-lo?      | Atalho do ambiente de trabalho (`C:\Program Files\RStudio\bin\rstudio.exe`) no Windows, o atalho do ambiente de trabalho (`/usr/bin/rstudio`) no Linux      |
| Ferramentas relacionadas no DSVM      |   Visual Studio 2017, o código do Visual Studio, Juno      |

## <a name="rstudio--server"></a>Servidor do RStudio 
|    |           |
| ------------- | ------------- |
| O que é?   | IDE baseada na Web para R    |
| Versões suportadas DSVM      | Linux      |
| Utilizações comuns      |  Desenvolvimento de R     |
| Como utilizar / executá-lo?      | Ativar o serviço com _systemctl ativar servidores do rstudio_, em seguida, inicie o serviço com _systemctl iniciar servidores do rstudio_. Em seguida, podem iniciar sessão no servidor do RStudio http://your-vm-ip:8787.       |
| Ferramentas relacionadas no DSVM      |   Visual Studio 2017, o código do Visual Studio, ambiente de trabalho do RStudio      |

## <a name="juno"></a>Juno 
|    |           |
| ------------- | ------------- |
| O que é?   | Cliente IDE para o idioma de Leonor   |
| Versões suportadas DSVM      | Windows, Linux      |
| Utilizações comuns      |  Desenvolvimento de Leonor     |
| Como utilizar / executá-lo?      | Atalho do ambiente de trabalho (`C:\JuliaPro-0.5.1.1\Juno.bat`) no Windows, o atalho do ambiente de trabalho (`/opt/JuliaPro-VERSION/Juno`) no Linux      |
| Ferramentas relacionadas no DSVM      |   Visual Studio 2017, o Visual Studio Code, RStudio      |

## <a name="pycharm"></a>Pycharm
|    |           |
| ------------- | ------------- |
| O que é?   | Cliente IDE para o idioma de Python    |
| Versões suportadas DSVM      | Linux      |
| Utilizações comuns      |  Desenvolvimento de R     |
| Como utilizar / executá-lo?      | Atalho do ambiente de trabalho (`/usr/bin/pycharm`) no Linux      |
| Ferramentas relacionadas no DSVM      |   Visual Studio 2017, o Visual Studio Code, RStudio      |



## <a name="powerbi-desktop"></a>Power BI Desktop 
|    |           |
| ------------- | ------------- |
| O que é?   | Visualização de dados interativa e BI ferramenta    |
| Versões suportadas DSVM      | Windows  |
| Utilizações comuns      |  Visualização de dados e a criação de Dashboards   |
| Como utilizar / executá-lo?      | Atalho do ambiente de trabalho (`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`)      |
| Ferramentas relacionadas no DSVM      |   Visual Studio 2017, o código do Visual Studio, Juno      |

