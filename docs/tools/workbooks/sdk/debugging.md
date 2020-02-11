---
title: Debug di integrazioni
description: In questo documento viene descritto come eseguire il debug di Xamarin Workbooks integrazioni, sia lato agente che lato client in Windows e Mac.
ms.prod: xamarin
ms.assetid: 90143544-084D-49BF-B44D-7AF943668F6C
author: davidortinau
ms.author: daortin
ms.date: 06/19/2018
ms.openlocfilehash: 3030bf907d1ddbb02884f997f178b55950b442d4
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73018811"
---
# <a name="debugging-integrations"></a>Debug di integrazioni

## <a name="debugging-agent-side-integrations"></a>Debug di integrazioni lato agente

Per eseguire il debug delle integrazioni lato agente, è consigliabile usare i metodi di registrazione forniti dalla classe `Log` in `Xamarin.Interactive.Logging`.

In macOS i messaggi di log vengono visualizzati nel menu Visualizzatore log (**Visualizzatore log >** ) e nel log del client. In Windows i messaggi vengono visualizzati solo nel log del client, in quanto non è presente alcun visualizzatore di log.

Il log del client si trova nei percorsi seguenti in macOS e Windows:

- Mac: `~/Library/Logs/Xamarin/Workbooks/Xamarin Workbooks {date}.log`
- Windows: `%LOCALAPPDATA%\Xamarin\Workbooks\logs\Xamarin Workbooks {date}.log`

Una cosa da tenere presente è che, quando si caricano le integrazioni tramite il consueto meccanismo di `#r` durante lo sviluppo, l'assembly di integrazione verrà prelevato come _dipendenza_ della cartella di lavoro e incluso nel pacchetto se non viene utilizzato un percorso assoluto. Questo può compromettere la propagazione delle modifiche, come se la ricompilazione dell'integrazione non fosse stata eseguita.

## <a name="debugging-client-side-integrations"></a>Debug di integrazioni lato client

Poiché le integrazioni lato client sono scritte in JavaScript e caricate nella nostra area del Web browser (vedere la documentazione dell' [architettura](~/tools/workbooks/sdk/architecture.md) ), il modo migliore per eseguirne il debug consiste nell'usare gli strumenti di sviluppo WebKit in Mac o la selezione F12 in Windows.

Entrambi i set di strumenti consentono di visualizzare l'origine JavaScript/TypeScript, impostare i punti di interruzione, visualizzare l'output della console e controllare e modificare il DOM.

### <a name="mac"></a>Mac

Per abilitare gli strumenti di sviluppo per Xamarin Workbooks su Mac, eseguire il comando seguente nel terminale:

```shell
defaults write com.xamarin.Workbooks WebKitDeveloperExtras -bool true
```

e quindi riavviare Xamarin Workbooks. Una volta eseguita questa operazione, nel menu di scelta rapida fare clic con il pulsante destro del mouse sul menu di scelta rapida. **verrà visualizzato un** nuovo riquadro **sviluppatore** nelle preferenze di cartelle di lavoro. Questa opzione consente di scegliere se si desidera che gli strumenti di sviluppo siano aperti all'avvio:

[riquadro![Developer](debugging-images/developer-pane-small.png)](debugging-images/developer-pane.png#lightbox)

Questa preferenza è anche di riavvio: è necessario riavviare il client delle cartelle di lavoro per rendere effettive le nuove cartelle di lavoro. Quando si attivano gli strumenti di sviluppo tramite il menu di scelta rapida o le preferenze, viene visualizzata la nota interfaccia utente di Safari:

[strumenti di sviluppo per![Safari](debugging-images/mac-dev-tools.png)](debugging-images/mac-dev-tools.png#lightbox)

Per informazioni sull'uso degli strumenti di sviluppo Safari, vedere la [documentazione del controllo WebKit][webkit-docs].

### <a name="windows"></a>WINDOWS

In Windows, il team di IE fornisce uno strumento noto come "selezione F12", ovvero un debugger remoto per le istanze di Internet Explorer incorporate. Lo strumento è reperibile nel percorso seguente:

```shell
C:\Windows\System32\F12\F12Chooser.exe
```

Eseguire la selezione F12. verrà visualizzata l'istanza incorporata che alimenta la superficie client delle cartelle di lavoro nell'elenco. Scegliere questa finestra e verranno visualizzati gli strumenti di debug F12 noti di Internet Explorer, collegati al client:

[strumenti di![F12](debugging-images/windows-dev-tools.png)](debugging-images/windows-dev-tools.png#lightbox)

[webkit-docs]: https://trac.webkit.org/wiki/WebInspector
