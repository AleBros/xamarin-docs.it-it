---
ms.openlocfilehash: 0071fcc72844e4816e707c5828464859e09db68c
ms.sourcegitcommit: 2503da076966d12a8bdbf7af209662b74913a15c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2020
ms.locfileid: "78261618"
---
# <a name="contributing"></a>Contributo

Grazie per l'interesse dimostrato nell'apporto di contributi alla documentazione di Xamarin.

Questa pagina illustra il processo di base per l'aggiornamento del contenuto nella [documentazione di Xamarin](https://docs.microsoft.com/xamarin).

- [Contratto di licenza con il collaboratore](LICENSE)

## <a name="process-for-contributing"></a>Processo per fornire il contributo

### <a name="small-changes--edits"></a>Piccole modifiche e modifiche

Per applicare correzioni e aggiornamenti minori, è possibile fare clic sul pulsante **Modifica** in qualsiasi pagina e usare il sito Web GitHub per aggiungere il proprio contributo, oppure seguire questa procedura:

1. Creare una copia tramite fork del repository `MicrosoftDocs/xamarin-docs`.

2. Creare un elemento `branch` per le modifiche.

3. Scrivere il contenuto. Fare riferimento al [modello](contributing-guidelines/template.md) e alla [Guida di stile](contributing-guidelines/voice-tone.md).

4. Inviare una richiesta pull (PR) dal ramo a `MicrosoftDocs/xamarin-docs/live`.

5. Eseguire gli aggiornamenti necessari per il ramo, come discusso con il team nella richiesta pull.

6. Dopo aver applicato il feedback e se le modifiche sono corrette, i responsabili eseguiranno il merge della richiesta pull. La richiesta verrà visualizzata in docs.microsoft.com poco dopo.

> [!NOTE]
> Se la richiesta pull tratta un problema esistente, aggiungere la parola chiave `Fixes #Issue_Number` al messaggio di commit o alla descrizione della richiesta pull, in modo che il problema possa essere automaticamente chiuso quando viene eseguito il merge della richiesta pull. Per altre informazioni vedere l'argomento relativo alla [chiusura di problemi tramite i messaggi di commit](https://help.github.com/articles/closing-issues-via-commit-messages/).

### <a name="big-changes-or-new-content"></a>Modifiche importanti o nuovi contenuti

Per i contributi con modifiche importanti e i nuovi contenuti, [aprire un problema](https://github.com/MicrosoftDocs/xamarin-docs/issues) che illustra l'articolo che si vuole scrivere e la relazione con il contenuto esistente. Il contenuto all'interno della cartella docs è organizzato in sezioni suddivise per area di prodotto (ad esempio Android e iOS). Provare a determinare la cartella corretta per il nuovo contenuto. 

**Prima di iniziare a scrivere il contenuto, ottenere un feedback per la proposta attraverso il problema.**

Se si tratta di un nuovo argomento, è possibile usare il [file modello](../contributing-guidelines/template.md) come punto di partenza. Contiene le linee guida per la scrittura e illustra anche i metadati necessari per ogni articolo, ad esempio le informazioni sull'autore.

Aggiungere eventuali immagini e altre risorse statiche alla sottocartella denominata **\<nomepagina>-images**. Se si sta creando una nuova cartella per il contenuto, aggiungere una cartella images alla nuova cartella.

#### <a name="example-structure"></a>Struttura di esempio

```
docs
    /android
        mypage.md
        /mypage-images
            some-image.png
```

Assicurarsi di seguire la sintassi di Markdown appropriata. Per altre informazioni, vedere la [guida di stile](../contributing-guidelines/template.md).

La procedura di invio vera e propria è quella descritta per le piccole modifiche ([sopra](#process-for-contributing)).

Il team di Xamarin esamina la richiesta pull e informa l'utente (tramite feedback nella richiesta pull) se la modifica è corretta o se per l'approvazione sono necessari altri aggiornamenti o modifiche.

Dopo aver applicato il feedback e se le modifiche sono corrette, i responsabili eseguono il merge della richiesta pull.

A intervalli stabiliti viene eseguito il push di tutti i commit dal ramo master al sito attivo e l'utente può visualizzare i propri contributi in https://docs.microsoft.com/xamarin/.

### <a name="contributing-to-international-content"></a>Aggiunta di contributi ai contenuti internazionali

I contributi per il contenuto del computer tradotto (MT) non sono attualmente accettati per il momento. Per migliorare la qualità del contenuto MT, è stato effettuato il passaggio a un motore MT neurale. Accettiamo e incoraggiamo i contributi per il contenuto della traduzione umana (HT), usato per il training del motore Neural MT. Nel tempo, i contributi ai contenuti HT miglioreranno la qualità di HT e MT. Gli argomenti MT avranno una dichiarazione di non responsabilità che informa che parte dell'argomento può essere MT e il pulsante **modifica** non viene visualizzato perché è disabilitato.

## <a name="dos-and-donts"></a>Cosa fare e cosa non fare

Di seguito è riportato un breve elenco di regole che è necessario tenere presenti quando si inviano contributi alla documentazione di .NET.

- **NON** inviare richieste pull di grandi dimensioni. Al contrario, segnalare un problema e avviare una discussione in modo che si possa concordare una direzione prima di investire una grande quantità di tempo.
- **LEGGERE** le linee guida riportate nella [Guida di stile](contributing-guidelines/template.md) e sulla [voce e tono](contributing-guidelines/voice-tone.md).
- **USARE** il file [modello](contributing-guidelines/template.md) come punto di partenza del proprio lavoro.
- **CREARE** un ramo separato nel fork prima di lavorare sugli articoli.
- **SEGUIRE** il [flusso di lavoro di GitHub Flow](https://guides.github.com/introduction/flow/).
- **SCIVERE UN BLOG O INVIARE TWEET** (o usare altri mezzi) frequentemente per informazioni sui contributi.

> [!NOTE]
> Si potrebbe notare che attualmente alcuni argomenti non seguono tutte le linee guida indicate qui e nella [Guida di stile](contributing-guidelines/template.md). Ci stiamo impegnando per il raggiungimento della coerenza in tutto il sito. 
