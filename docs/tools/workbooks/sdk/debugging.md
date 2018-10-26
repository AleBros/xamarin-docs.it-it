---
title: Debug delle integrazioni
description: Questo documento descrive come eseguire il debug delle integrazioni di Xamarin Workbooks lato agente sia lato client in Windows e Mac.
ms.prod: xamarin
ms.assetid: 90143544-084D-49BF-B44D-7AF943668F6C
author: lobrien
ms.author: laobri
ms.date: 06/19/2018
ms.openlocfilehash: 86d9c6af93e7f59eb0e819730e46324688df7566
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50106000"
---
# <a name="debugging-integrations"></a>Debug delle integrazioni

## <a name="debugging-agent-side-integrations"></a>Debug delle integrazioni lato agente

Debug delle integrazioni lato agente è meglio effettuare utilizzando i metodi di registrazione forniti dal `Log` classe `Xamarin.Interactive.Logging`. Vedere le [ `API docs` ](https://developer.xamarin.com/api/type/Xamarin.Interactive.Logging.Log/) per i metodi da chiamare.

In macOS, i messaggi di log visualizzato nel menu entrambi il Visualizzatore di log (**finestra > Visualizzatore Log**) e nel log del client. In Windows, i messaggi solo vengono visualizzati nel log del client, poiché non esiste alcun visualizzatore log.

Log del client è negli articoli seguenti su macOS e Windows:

- Mac: `~/Library/Logs/Xamarin/Workbooks/Xamarin Workbooks {date}.log`
- Windows: `%LOCALAPPDATA%\Xamarin\Workbooks\logs\Xamarin Workbooks {date}.log`

È un aspetto da tenere presente che quando si caricano le integrazioni tramite la consueta `#r` meccanismo durante lo sviluppo, l'assembly di integrazione verrà prelevato come un _dipendenza_ della cartella di lavoro e incluso nel pacchetto, se è un percorso assoluto non utilizzato. Ciò può provocare modifiche vengano visualizzate non propagare, come se l'integrazione di ricompilazione non aveva alcun effetto.

## <a name="debugging-client-side-integrations"></a>Debug delle integrazioni lato client

Integrazioni lato client vengono scritte in JavaScript e caricate in superficie i browser web (vedere la [architettura](~/tools/workbooks/sdk/architecture.md) documentazione), il modo migliore per eseguirne il debug usando gli strumenti di sviluppo WebKit su Mac oppure mediante selezione F12 in Windows .

Entrambi i set di strumenti consentono di visualizzare l'origine di JavaScript/TypeScript, impostare punti di interruzione, visualizzare l'output della console e controllare e modificare il DOM.

### <a name="mac"></a>Mac

Per abilitare gli strumenti di sviluppo per Xamarin Workbooks su Mac, eseguire il comando seguente nel terminale:

```shell
defaults write com.xamarin.Workbooks WebKitDeveloperExtras -bool true
```

e quindi riavviare Xamarin Workbooks. In questo caso, verranno visualizzati **elemento ispezionare** vengono visualizzati nel menu di scelta rapida e un nuovo **Developer** riquadro saranno disponibile nelle preferenze di cartelle di lavoro. Questa opzione consente di scegliere se si desidera che gli strumenti di sviluppo aperti all'avvio:

[![Riquadro per gli sviluppatori](debugging-images/developer-pane-small.png)](debugging-images/developer-pane.png#lightbox)

Questa preferenza viene anche solo il riavvio, è necessario riavviare il client di cartelle di lavoro affinché diventi efficace in nuove cartelle di lavoro. Attivare gli strumenti di sviluppo tramite il menu di scelta rapida o le preferenze mostrerà la familiare interfaccia utente di Safari:

[![Strumenti di sviluppo di Safari](debugging-images/mac-dev-tools.png)](debugging-images/mac-dev-tools.png#lightbox)

Per informazioni sull'uso di strumenti di sviluppo di Safari, vedere la [documentazione di WebKit inspector][webkit-docs].

### <a name="windows"></a>WINDOWS

In Windows, il team di IE fornisce uno strumento noto come "Selezione F12" vale a dire un debugger remoto per le istanze di Internet Explorer incorporate. È possibile trovare lo strumento nel percorso seguente:

```shell
C:\Windows\System32\F12\F12Chooser.exe
```

Selezione F12 esecuzione, verrà visualizzato l'istanza di embedded che attiva l'area client di cartelle di lavoro nell'elenco. Scegliere e il F12 familiari strumenti di debug da Internet Explorer verranno visualizzato, connesso al client:

[![Strumenti F12](debugging-images/windows-dev-tools.png)](debugging-images/windows-dev-tools.png#lightbox)

[webkit-docs]: https://trac.webkit.org/wiki/WebInspector
