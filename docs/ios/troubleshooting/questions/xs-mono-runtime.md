---
title: Come impostare le variabili di ambiente di Runtime di Mono per i progetti iOS in Xamarin Studio?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 1176CEA9-C7F1-411B-8F1A-99374E8AFF33
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/31/2017
ms.openlocfilehash: ba74e316f706e5bf22f973de4dad38d94ccd0db9
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50116940"
---
# <a name="how-do-i-set-mono-runtime-environment-variables-for-ios-projects-in-xamarin-studio"></a>Come impostare le variabili di ambiente di Runtime di Mono per i progetti iOS in Xamarin Studio?

Se è necessario impostare variabili di ambiente di alcun runtime di Mono, possono essere impostate nel **opzioni progetto > Esegui > Generale** pagina.

Nota: Operazione di Garbage Collection le variabili di ambiente SGen (MONO\_GC\_PARAMS) set in questo modo verrà utilizzato solo durante l'avvio da Xamarin Studio. Se si avvia l'app dal dispositivo, è possibile che le impostazioni per Sgen verranno ignorate. 

Per impostare in modo permanente una variabile di ambiente per un'app, è necessario aggiungere quanto segue come argomenti aggiuntivi di mtouch (per tutte le configurazioni pertinenti):

```csharp
   --setenv=NAME=VALUE
```

Per visualizzare le variabili di ambiente che è possibile impostare, vedere la pagina Mono: [ http://docs.go-mono.com/?link=man%3amono(1) ](http://docs.go-mono.com/?link=man%3amono(1)) vedere la sezione: `ENVIRONMENT VARIABLES`

![](xs-mono-runtime-images/environment-variables.jpg "Impostazione delle variabili di ambiente per un progetto")
