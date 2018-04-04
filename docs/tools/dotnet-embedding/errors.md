---
title: Errori di incorporamento .NET
ms.prod: xamarin
ms.assetid: 932C3F0C-D968-42D1-BB14-D97C73361983
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: 64caaf6610d9f9193a686d91b4731cd4d4953fa6
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="em0xxx-binding-error-messages"></a>EM0xxx: i messaggi di errore di associazione

Ad esempio, i parametri, ambiente

<!-- 0xxx: the generator itself, e.g. parameters, environment -->
<h3><a name="EM0000"/>EM0000: Errore imprevisto - compilare un report di bug in https://github.com/mono/Embeddinator-4000/issues</h3>

Si è verificato un errore imprevisto. . [Un problema del file](https://github.com/mono/Embeddinator-4000/issues) con le informazioni possibili, tra cui:

* Log, di compilazione completa con massimo livello di dettaglio
* Un test case minimo riprodurre l'errore
* Tutte le informazioni di versione

Il modo più semplice per ottenere informazioni sulla versione esatta consiste nell'utilizzare il **Xamarin Studio** menu **su Xamarin Studio** elemento **Mostra dettagli** pulsante e copiare e incollare la versione informazioni (è possibile utilizzare il **copia informazioni** pulsante).

<h3><a name="EM0001"/>EM0001: Impossibile creare la directory di Output `X`</h3>

Il nome della directory specificato da `-o=DIR` non esiste e non può essere creato. Potrebbe essere un nome non valido per il file system.

<h3><a name="EM0002"/>EM0002: Opzione `X` non è supportata</h3>

Lo strumento non supporta l'opzione `X`. È possibile che un'altra versione dello strumento supporta o non si applica in questo ambiente.

<h3><a name="EM0003"/>EM0003: La piattaforma `X` non è valido.</h3>

Lo strumento non supporta la piattaforma `X`. È possibile che un'altra versione dello strumento supporta o non si applica in questo ambiente.

<h3><a name="EM0004"/>EM0004: La destinazione `X` non è valido.</h3>

Lo strumento non supporta la destinazione `X`. È possibile che un'altra versione dello strumento supporta o non si applica in questo ambiente.

<h3><a name="EM0005"/>EM0005: Destinazione della compilazione `X` non è valido.</h3>

Lo strumento non supporta la destinazione di compilazione `X`. È possibile che un'altra versione dello strumento supporta o non si applica in questo ambiente.

<h3><a name="EM0006"/>EM0006: Impossibile trovare il percorso di Xcode.</h3>

Lo strumento non è stato possibile trovare il percorso Xcode attualmente selezionato usando la `xcode-select -p` comando. Verificare che questo comando ha esito positivo e restituisce il percorso corretto di Xcode.

<h3><a name="EM0007"/>EM0007: Impossibile ottenere la versione del sdk per '{sdk}'.</h3>

Lo strumento non è stato possibile ottenere la versione SDK utilizzando il `xcrun --show-sdk-version --sdk {sdk}` comando. Verificare che questo comando ha esito positivo e restituisce la versione del SDK.

<h3><a name="EM0008"/>EM0008: L'architettura '{arch}' non è valido per la piattaforma di {}. Le architetture valide per la piattaforma di {} sono: '{architetture}'.</h3>

L'architettura nel messaggio di errore non è valido per la piattaforma di destinazione. Verificare che l'opzione - abi viene passato a un'architettura valida.

<h3><a name="EM0009"/>EM0009: La funzionalità `X` non è attualmente implementato dal generatore</h3>

Si tratta di un problema noto che si intende risolvere in una versione futura del generatore. Contributi sono iniziale.

<h3><a name="EM0010"/>EM0010: Impossibile eseguire il merge di Framework '{simulatorFramework}' e '{deviceFramework}' perché il file ' {}' esiste in entrambi.</h3>

Lo strumento non può unire i Framework indicati nel messaggio di errore, perché è presente un file comune tra di essi.

Ciò potrebbe indicare un bug in Embeddinator-4000; inviare una segnalazione di bug in [ https://github.com/mono/Embeddinator-4000/issues ](https://github.com/mono/Embeddinator-4000/issues) con un test case.

<h3><a name="EM0011"/>EM0011: L'assembly `X` non esiste.</h3>

Lo strumento non è riuscito a trovare l'assembly `X` specificato negli argomenti.

<h3><a name="EM0012"/>EM0012: Il nome dell'assembly `X` non è univoco</h3>

Più di un assembly fornito ha il nome stesso, interno e non sarebbe possibile distinguere tra di essi in fase di esecuzione.

La causa più probabile è che un assembly è stato specificato più volte agli argomenti della riga di comando. Tuttavia non sono un mantiene ancora assembly rinominato è il nome originale e più copie coesiste.

<h3><a name="EM0013"/>EM0013: Impossibile trovare l'assembly 'X', 'Y' a cui fa riferimento.</h3>

Lo strumento Impossibile trovare l'assembly 'X', a cui fa riferimento l'assembly 'Y'. Verificare che tutti gli assembly cui viene fatto riferimenti siano nella stessa directory dell'assembly da associare.

<h3><a name="EM0014"/>EM0014: Impossibile trovare {prodotto} ({prodotto} {min_version} è obbligatorio).</h3>

Impossibile trovare la dipendenza indicata nel messaggio di errore nel sistema.

<h3><a name="EM0015"/>EM0015: Impossibile trovare una versione valida di {} (trovato {version}, ma almeno {min_version} è obbligatorio).</h3>

La dipendenza indicato nell'errore messaggio è stato trovato nel sistema, ma è troppo vecchio. Eseguire l'aggiornamento a una versione più recente.

<h3><a name="EM0016">EM0016: Collegamento simbolico non è stato possibile creare '{file}' -> '{target}': errore {numero}</h3>

Impossibile creare il collegamento simbolico indicato nel messaggio di errore.

<h3><a name="EM0026"/>EM0026 Impossibile analizzare l'argomento della riga di comando 'A': *</h3>

La sintassi per l'opzione della riga di comando `A` non può essere analizzato dallo strumento. È probabile che non è corretto, verificare con la documentazione o la Guida per la sintassi corretta.

<h3><a name="EM0099"/>: EM0099 Errore interno *. Inviare una segnalazione di bug con un test case (https://github.com/mono/Embeddinator-4000/issues).</h3>

Questo messaggio di errore viene segnalato quando si verifica un errore di controllo di coerenza interna nel Embeddinator-4000.

Questo indica un bug in Embeddinator-4000; inviare una segnalazione di bug in [ https://github.com/mono/Embeddinator-4000/issues ](https://github.com/mono/Embeddinator-4000/issues) con un test case.


<!-- 1xxx: code processing -->

# <a name="em1xxx-code-processing"></a>EM1xxx: Elaborazione del codice

<h3><a name="EM1010"/>Tipo `T` non viene generato perché `X` non sono supportati.</h3>

Si tratta di un **avviso** che il tipo `T` verranno ignorate (ad esempio non verrà generato) perché utilizza `X`, una funzionalità che non è supportata.

Nota: Evolveranno funzionalità supportate con le nuove versioni dello strumento.


<h3><a name="EM1011"/>Tipo `T` non viene generato perché manca un equivalente nativo.</h3>

Si tratta di un **avviso** che il tipo `T` verranno ignorate (ad esempio non verrà generato) perché viene utilizzata espongono un codice di .NET framework che non ha equivalenti in piattaforma nativa.


<h3><a name="EM1011"/>Tipo `T` non viene generato perché manca un codice di marshalling con un equivalente nativo.</h3>

Si tratta di un **avviso** che il tipo `T` verranno ignorate (ad esempio non verrà generato) perché viene utilizzata espongono un codice di .NET framework che richiede molto marshalling.

Nota: Si tratta di un elemento che è possibile che venga get supportato, con alcune limitazioni, in una versione futura dello strumento.


<h3><a name="EM1020"/>Costruttore `C` non viene generato a causa di tipo di parametro `T` non è supportata.</h3>

Si tratta di un **avviso** che il costruttore `C` verranno ignorate (ad esempio non verrà generato) perché un parametro di tipo `T` non è supportata.

Dovrebbe esserci un avviso precedente contenente ulteriori informazioni, perché digitare `T` non è supportata.

Nota: Evolveranno funzionalità supportate con le nuove versioni dello strumento.


<h3><a name="EM1021"/>Costruttore `C` dispone di valori predefiniti per il quale non viene generato alcun wrapper.</h3>

Si tratta di un **avviso** che i parametri predefiniti di costruttore `C` non generano codice aggiuntivo. La causa più comune è che un metodo esistente già la stessa firma. Ad esempio, in .net è possibile disporre di:

```
public class MyType {
    public MyType () { ... }
    public MyType (int i = 0) { ... }
}
```

In questi casi solo due generati `init` verranno creati i selettori, sia la chiamata a mono, ma non sarà disponibile alcun wrapper per le versioni successive.


<h3><a name="EM1030"/>Metodo `M` non viene generato perché il tipo restituito `T` non è supportata.</h3>

Si tratta di un **avviso** che il metodo `M` verranno ignorate (ad esempio non verrà generato) perché è un tipo restituito `T` non è supportata.

Dovrebbe esserci un avviso precedente contenente ulteriori informazioni, perché digitare `T` non è supportata.

Nota: Evolveranno funzionalità supportate con le nuove versioni dello strumento.


<h3><a name="EM1031"/>Metodo `M` non viene generato a causa di tipo di parametro `T` non è supportata.</h3>

Si tratta di un **avviso** che il metodo `M` verranno ignorate (ad esempio non verrà generato) perché un parametro di tipo `T` non è supportata.

Dovrebbe esserci un avviso precedente contenente ulteriori informazioni, perché digitare `T` non è supportata.

Nota: Evolveranno funzionalità supportate con le nuove versioni dello strumento.


<h3><a name="EM1032"/>Metodo `M` ha valori predefiniti per il quale non viene generato alcun wrapper.</h3>

Si tratta di un **avviso** che i parametri predefiniti del metodo `M` non generano codice aggiuntivo. La causa più comune è che un metodo esistente già la stessa firma. Ad esempio, in .net è possibile disporre di:

```
public class MyType {
    public int Increment () { ... }
    public int Increment (int i = 0) { ... }
}
```

In questi casi solo due generati `increment` verranno creati i selettori, sia la chiamata a mono, ma non sarà disponibile alcun wrapper per le versioni successive.


<h3><a name="EM1033"/>Metodo `M` non viene generato perché un altro metodo espone l'operatore con un nome descrittivo.</h3>

Si tratta di un **avviso** che il metodo `M` non viene generato perché un altro metodo espone l'operatore con un nome descrittivo. (https://msdn.microsoft.com/en-us/library/ms229032(v=vs.110).aspx)


<h3><a name="EM1034"/>Metodo di estensione `M` non viene generato all'interno di una categoria, poiché non possono essere creati su un tipo primitivo `T`. È stato generato un metodo statico normale.</h3>

Si tratta di un **avviso** che tipo di un metodo di estensione in un primivite (ad esempio `System.Int32`) è stato trovato. In ObjC non è possibile creare categorie di tipo primitivo. Invece il generatore produrrà un metodo statico normale.



<h3><a name="EM1040"/>Proprietà `P` non viene generato a causa di tipo di parametro `T` non è supportata.</h3>

Si tratta di un **avviso** che la proprietà `P` verranno ignorate (ad esempio non verrà generato) perché il tipo esposto `T` non è supportata.

Dovrebbe esserci un avviso precedente contenente ulteriori informazioni, perché digitare `T` non è supportata.

Nota: Evolveranno funzionalità supportate con le nuove versioni dello strumento.

<h3><a name="EM1041"/>Proprietà indicizzate in `T` non viene generato perché più proprietà indicizzate non sono supportate.</h3>

Si tratta di un **avviso** che le proprietà indicizzate `T` verranno ignorate (ad esempio non verrà generato) perché non sono supportate più proprietà indicizzate.


<h3><a name="EM1050"/>Campo `F` non viene generato a causa di tipo di campo `T` non è supportata.</h3>

Si tratta di un **avviso** che il campo `F` verranno ignorate (ad esempio non verrà generato) perché il tipo esposto `T` non è supportata.

Dovrebbe esserci un avviso precedente contenente ulteriori informazioni, perché digitare `T` non è supportata.

Nota: Evolveranno funzionalità supportate con le nuove versioni dello strumento.

<h3><a name="EM1051"/>Elemento `E` viene generato invece come `F` perché il relativo nome in conflitto con un selettore objective-c importanti.</h3>

Si tratta di un **avviso** che l'elemento `E` verrà generato come `F` perché il relativo nome in conflitto con un selettore objective-c importanti.

Selettori nel [NSObjectProtocol](https://developer.apple.com/reference/objectivec/1418956-nsobject?language=objc) hanno un significato importante in objective-c e deve essere sottoposto a override con attenzione.

Nota: L'elenco di selettori riservati evolveranno con le nuove versioni dello strumento.


<!-- 2xxx: code generation -->

# <a name="em2xxx-code-generation"></a>EM2xxx: Generazione di codice


<!-- 3xxx: reserved -->
<!-- 4xxx: reserved -->
<!-- 5xxx: reserved -->
<!-- 6xxx: reserved -->
<!-- 7xxx: reserved -->
<!-- 8xxx: reserved -->
<!-- 9xxx: reserved -->
