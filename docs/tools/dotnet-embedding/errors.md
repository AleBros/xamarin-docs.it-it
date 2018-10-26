---
title: Errori di incorporamento di .NET
description: Questo documento descrive gli errori generati dall'incorporamento di .NET. Gli errori sono elencati in base al codice e ha una descrizione per semplificare la risoluzione dei problemi.
ms.prod: xamarin
ms.assetid: 932C3F0C-D968-42D1-BB14-D97C73361983
author: lobrien
ms.author: laobri
ms.date: 04/11/2018
ms.openlocfilehash: 5c3dd406f1132f51a86ddf574ab7ad0b279bc9ec
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50106312"
---
# <a name="net-embedding-errors"></a>Errori di incorporamento di .NET

## <a name="em0xxx-binding-error-messages"></a>EM0xxx: I messaggi di errore di associazione

Ad esempio, i parametri, ambiente

<!-- 0xxx: the generator itself, e.g. parameters, environment -->

<a name="EM0000" />

### <a name="em0000-unexpected-error---please-fill-a-bug-report-at-httpsgithubcommonoembeddinator-4000issues"></a>EM0000: Errore imprevisto. compilare un report sui bug a https://github.com/mono/Embeddinator-4000/issues

Si è verificata una condizione di errore imprevisto. Verificare [segnalare un problema](https://github.com/mono/Embeddinator-4000/issues) quante più informazioni possibili, tra cui:

* Compilazione completa dei log, con livello di dettaglio massimo
* Un minimo di test case di riprodurre l'errore
* Tutte le informazioni di versione

Il modo più semplice per ottenere informazioni sulla versione esatta è usare il **Xamarin Studio** dal menu **informazioni su Xamarin Studio** elemento **Mostra dettagli** pulsante e copiare e incollare la versione informazioni (è possibile usare la **informazioni sulla copia** pulsante).

<a name="EM0001" />

### <a name="em0001-could-not-create-output-directory-x"></a>EM0001: Impossibile creare la directory di Output `X`

Il nome della directory specificato da `-o=DIR` non esiste e non può essere creato. Potrebbe essere un nome non valido per il file system.

<a name="EM0002" />

### <a name="em0002-option-x-is-not-supported"></a>EM0002: Opzione `X` non è supportato

Lo strumento non supporta l'opzione `X`. È possibile che un'altra versione dello strumento supporti o che non è applicabile in questo ambiente.

<a name="EM0003" />

### <a name="em0003-the-platform-x-is-not-valid"></a>EM0003: La piattaforma `X` non è valido.

Lo strumento non supporta la piattaforma `X`. È possibile che un'altra versione dello strumento supporti o che non è applicabile in questo ambiente.

<a name="EM0004" />

### <a name="em0004-the-target-x-is-not-valid"></a>EM0004: La destinazione `X` non è valido.

Lo strumento non supporta la destinazione `X`. È possibile che un'altra versione dello strumento supporti o che non è applicabile in questo ambiente.

<a name="EM0005" />

### <a name="em0005-the-compilation-target-x-is-not-valid"></a>EM0005: Destinazione della compilazione `X` non è valido.

Lo strumento non supporta la destinazione di compilazione `X`. È possibile che un'altra versione dello strumento supporti o che non è applicabile in questo ambiente.

<a name="EM0006" />

### <a name="em0006-could-not-find-the-xcode-location"></a>EM0006: Impossibile trovare il percorso di Xcode.

Lo strumento non è stato possibile trovare il percorso Xcode attualmente selezionato usando la `xcode-select -p` comando. Verificare che questo comando ha esito positivo e restituisce il percorso di Xcode corretto.

<a name="EM0007" />

### <a name="em0007-could-not-get-the-sdk-version-for-sdk"></a>EM0007: Impossibile ottenere la versione del sdk per '{sdk}'.

Lo strumento non è stato possibile ottenere la versione SDK usando il `xcrun --show-sdk-version --sdk {sdk}` comando. Verificare che questo comando ha esito positivo e restituisce la versione del SDK.

<a name="EM0008" />

### <a name="em0008-the-architecture-arch-is-not-valid-for-platform-valid-architectures-for-platform-are-architectures"></a>EM0008: L'architettura '{arch}' non è valido per la piattaforma di {}. Le architetture valide per la piattaforma di {} sono: '{architetture}'.

L'architettura nel messaggio di errore non è valido per la piattaforma di destinazione. Verificare che l'opzione - abi viene passata un'architettura valida.

<a name="EM0009" />

### <a name="em0009-the-feature-x-is-not-currently-implemented-by-the-generator"></a>EM0009: La funzionalità `X` non è attualmente implementato dal generatore

Si tratta di un problema noto che si intende risolvere in una versione futura del generatore. I contributi sono Benvenuti.

<a name="EM0010" />

### <a name="em0010-cant-merge-the-frameworks-simulatorframework-and-deviceframework-because-the-file-file-exists-in-both"></a>EM0010: Non è possibile unire i framework '{simulatorFramework}' e '{deviceFramework}' perché il file '{file}' è disponibile in entrambe.

Lo strumento non è stato possibile unire i Framework indicati nel messaggio di errore, perché è presente un file comune tra di essi.

Questo dato può indicare un bug in .NET incorporamento; inviare un report sui bug a [ https://github.com/mono/Embeddinator-4000/issues ](https://github.com/mono/Embeddinator-4000/issues) con un test case.

<a name="EM0011" />

### <a name="em0011-the-assembly-x-does-not-exist"></a>EM0011: L'assembly `X` non esiste.

Lo strumento non è stato possibile trovare l'assembly `X` specificate negli argomenti.

<a name="EM0012" />

### <a name="em0012-the-assembly-name-x-is-not-unique"></a>EM0012: Il nome dell'assembly `X` non è univoco

Più di un assembly fornito ha il nome stesso, interno e non sarebbe possibile distinguerli in fase di esecuzione.

La causa più probabile è che un assembly è specificato più volte per gli argomenti della riga di comando. Tuttavia non sono un mantiene ancora assembly rinominato è il nome originale e più copie coesiste.

<a name="EM0013" />

### <a name="em0013-cant-find-the-assembly-x-referenced-by-y"></a>EM0013: Impossibile trovare l'assembly 'X', 'Y' a cui fanno riferimento.

Lo strumento non è stato possibile trovare l'assembly 'X', fa riferimento all'assembly 'Y'. Verificare che tutti gli assembly cui viene fatto riferimento siano nella stessa directory dell'assembly da associare.

<a name="EM0014" />

### <a name="em0014-could-not-find-product-product-minversion-is-required"></a>EM0014: Non è riuscito a trovare {product} ({product} {min_version} è obbligatorio).

La dipendenza indicata nel messaggio di errore non è stata trovata nel sistema.

<a name="EM0015" />

### <a name="em0015-could-not-find-a-valid-version-of-product-found-version-but-at-least-minversion-is-required"></a>EM0015: Non è riuscito a trovare una versione valida del {product} ({versione}, rilevata, ma almeno {min_version} è obbligatorio).

Le dipendenze indicate nel messaggio di errore messaggio è stato trovato nel sistema, ma è troppo vecchio. Aggiornare una versione più recente.

<a name="EM0016" />

### <a name="em0016-could-not-create-symlink-file---target-error-number"></a>EM0016: Non è possibile creare collegamento simbolico "{file}" -> '{target}': errore {number}

Impossibile creare il collegamento simbolico indicato nel messaggio di errore.

<a name="EM0026" />

### <a name="em0026-could-not-parse-the-command-line-argument-a-"></a>EM0026 Impossibile analizzare l'argomento della riga di comando 'A': *

La sintassi per l'opzione della riga di comando `A` nelze analyzovat dallo strumento. È probabile che non sono corrette, verificare con la documentazione o la Guida per la sintassi corretta.

<a name="EM0099" />

### <a name="em0099-internal-error--please-file-a-bug-report-with-a-test-case-httpsgithubcommonoembeddinator-4000issues"></a>EM0099: Errore interno *. Inviare un report sui bug con un test case (https://github.com/mono/Embeddinator-4000/issues).

Questo messaggio di errore viene segnalato quando un controllo di coerenza interno in .NET l'incorporamento ha esito negativo.

Questo indica un bug in .NET incorporamento; inviare un report sui bug a [ https://github.com/mono/Embeddinator-4000/issues ](https://github.com/mono/Embeddinator-4000/issues) con un test case.

<!-- 1xxx: code processing -->

## <a name="em1xxx-code-processing"></a>EM1xxx: L'elaborazione del Code

<a name="EM1010" />

### <a name="em1010-type-t-is-not-generated-because-x-are-not-supported"></a>EM1010: Digitare `T` non viene generato perché `X` non sono supportati.

Si tratta di un **avviso** che il tipo `T` verranno ignorate (vale a dire non deve essere generato) perché utilizza `X`, una funzionalità che non è supportata.

Nota: Le funzionalità supportate si evolvono con le nuove versioni dello strumento.

<a name="EM1011" />

### <a name="em1011-type-t-is-not-generated-because-it-lacks-marshaling-code-with-a-native-counterpart"></a>EM1011: Digitare `T` non viene generato perché manca il codice di marshalling con una controparte di native.

Si tratta di un **avviso** che il tipo `T` verrà ignorata (vale a dire non deve essere generato) poiché espone un elemento da .NET framework che richiede molto marshalling.

Nota: Questo è qualcosa che potrebbe ottenere supportati, con alcune limitazioni, in una futura versione dello strumento.

<a name="EM1020" />

### <a name="em1020-constructor-c-is-not-generated-because-of-parameter-type-t-is-not-supported"></a>EM1020: Costruttore `C` non viene generato a causa del tipo di parametro `T` non è supportato.

Si tratta di un **avviso** che il costruttore `C` verranno ignorate (ad esempio nulla deve essere generato) perché un parametro di tipo `T` non è supportato.

Dovrebbe esserci un precedente avviso contenente il motivo per cui ulteriori informazioni digitare `T` non è supportato.

Nota: Le funzionalità supportate si evolvono con le nuove versioni dello strumento.

<a name="EM1021" />

### <a name="em1021-constructor-c-has-default-values-for-which-no-wrapper-is-generated"></a>EM1021: Costruttore `C` ha valori predefiniti per il quale non viene generato alcun wrapper.

Si tratta di un **avviso** che i parametri predefiniti di costruttore `C` non genera alcun codice aggiuntivo. La causa più comune è che un metodo esistente ha già la stessa firma. Ad esempio, in .net è possibile disporre di:

```
public class MyType {
    public MyType () { ... }
    public MyType (int i = 0) { ... }
}
```

In questi casi solo due generati `init` verranno creati i selettori, sia la chiamata a Mono, ma non sarà disponibile alcun wrapper per le versioni successive.

<a name="EM1030" />

### <a name="em1030-method-m-is-not-generated-because-return-type-t-is-not-supported"></a>EM1030: Metodo `M` non viene generato perché il tipo restituito `T` non è supportato.

Si tratta di un **avviso** che il metodo `M` verranno ignorate (ad esempio nulla deve essere generato) perché è il tipo restituito `T` non è supportato.

Dovrebbe esserci un precedente avviso contenente il motivo per cui ulteriori informazioni digitare `T` non è supportato.

Nota: Le funzionalità supportate si evolvono con le nuove versioni dello strumento.

<a name="EM1031" />

### <a name="em1031-method-m-is-not-generated-because-of-parameter-type-t-is-not-supported"></a>EM1031: Metodo `M` non viene generato a causa del tipo di parametro `T` non è supportato.

Si tratta di un **avviso** che il metodo `M` verranno ignorate (ad esempio nulla deve essere generato) perché un parametro di tipo `T` non è supportato.

Dovrebbe esserci un precedente avviso contenente il motivo per cui ulteriori informazioni digitare `T` non è supportato.

Nota: Le funzionalità supportate si evolvono con le nuove versioni dello strumento.

<a name="EM1032" />

### <a name="em1032-method-m-has-default-values-for-which-no-wrapper-is-generated"></a>EM1032: Metodo `M` ha valori predefiniti per il quale non viene generato alcun wrapper.

Si tratta di un **avviso** che i parametri predefiniti di metodo `M` non genera alcun codice aggiuntivo. La causa più comune è che un metodo esistente ha già la stessa firma. Ad esempio, in .net è possibile disporre di:

```
public class MyType {
    public int Increment () { ... }
    public int Increment (int i = 0) { ... }
}
```

In questi casi solo due generati `increment` verranno creati i selettori, sia la chiamata a Mono, ma non sarà disponibile alcun wrapper per le versioni successive.

<a name="EM1033" />

### <a name="em1033-method-m-is-not-generated-because-another-method-exposes-the-operator-with-a-friendly-name"></a>EM1033: Metodo `M` non viene generato perché un altro metodo espone l'operatore con un nome descrittivo.

Si tratta di un **avviso** che il metodo `M` non viene generato perché un altro metodo espone l'operatore con un nome descrittivo. (https://msdn.microsoft.com/library/ms229032(v=vs.110).aspx)

<a name="EM1034" />

### <a name="em1034-extension-method-m-is-not-generated-inside-a-category-because-they-cannot-be-created-on-primitive-type-t-a-normal-static-method-was-generated"></a>: EM1034 Metodo di estensione `M` non viene generato all'interno di una categoria, poiché questi non possono essere creati nel tipo primitivo `T`. È stato generato un normale metodo statico.

Si tratta di un **avviso** che un metodo di estensione in un primivite tipo (ad esempio `System.Int32`) è stato trovato. In Objective-C non è possibile creare categorie di tipo primitivo. Invece il generatore produrrà un normale metodo statico.

<a name="EM1040" />

### <a name="em1040-property-p-is-not-generated-because-of-parameter-type-t-is-not-supported"></a>: EM1040 Proprietà `P` non viene generato a causa del tipo di parametro `T` non è supportato.

Si tratta di un **avviso** che la proprietà `P` verranno ignorate (vale a dire non deve essere generato) perché il tipo esposto `T` non è supportato.

Dovrebbe esserci un precedente avviso contenente il motivo per cui ulteriori informazioni digitare `T` non è supportato.

Nota: Le funzionalità supportate si evolvono con le nuove versioni dello strumento.

<a name="EM1041" />

### <a name="em1041-indexed-properties-on-t-is-not-generated-because-multiple-indexed-properties-are-not-supported"></a>EM1041: Proprietà indicizzate nella `T` non viene generato perché più proprietà indicizzate non sono supportate.

Si tratta di un **avviso** che le proprietà indicizzate in `T` verrà ignorata (vale a dire non deve essere generato) perché non sono supportate più proprietà indicizzate.

<a name="EM1050" />

### <a name="em1050-field-f-is-not-generated-because-of-field-type-t-is-not-supported"></a>: EM1050 Campo `F` non viene generato a causa del tipo di campo `T` non è supportato.

Si tratta di un **avviso** che il campo `F` verranno ignorate (vale a dire non deve essere generato) perché il tipo esposto `T` non è supportato.

Dovrebbe esserci un precedente avviso contenente il motivo per cui ulteriori informazioni digitare `T` non è supportato.

Nota: Le funzionalità supportate si evolvono con le nuove versioni dello strumento.

<a name="EM1051" />

### <a name="em1051-element-e-is-generated-instead-as-f-because-its-name-conflicts-with-an-important-objective-c-selector"></a>EM1051: Elemento `E` viene generata invece come `F` perché il relativo nome in conflitto con un'importante selettori objective-c.

Si tratta di un **avviso** che l'elemento `E` verrà generato invece come `F` perché il relativo nome in conflitto con un'importante selettori objective-c.

I selettori nella [NSObjectProtocol](https://developer.apple.com/reference/objectivec/1418956-nsobject?language=objc) hanno un significato importante in objective-c e deve essere sostituito con attenzione.

Nota: L'elenco di selettori riservati si evolverà con le nuove versioni dello strumento.

<a name="EM1052" />

### <a name="em1052-element-e-is-not-generated-its-name-conflicts-with-other-elements-on-the-same-class"></a>EM1052: Elemento `E` non viene generato il nome è in conflitto con altri elementi nella stessa classe.

Si tratta di un **avviso** quell'elemento `E` non viene generato quando il relativo nome è in conflitto con altri elementi nella stessa classe.

<a name="EM1053" />

### <a name="em1053-target-e-is-not-supported-for-xamarinios-and-xamarinmac-only-the-framework-option-is-considered-supported-and-should-be-used"></a>EM1053: Destinazione `E` non è supportata per xamarin. IOS e xamarin. Mac. Solo il `framework` opzione viene considerata supportata e deve essere utilizzato.

Si tratta di un **avviso** che hanno come destinazione `E` viene considerato non è supportato per casi d'uso di xamarin. IOS e xamarin. Mac. 

Consumo di librerie statiche o dinamiche .NET incorporamento potrebbe richiedere passaggi ulteriori operazioni o modifiche di lieve e deve essere evitato nella maggior parte dei casi d'uso.

Provare a rimuovere le `--target` parametro o passare `--target=framework` invece.

<!-- 2xxx: code generation -->

## <a name="em2xxx-code-generation"></a>EM2xxx: Generazione di codice

<!-- 3xxx: reserved -->
<!-- 4xxx: reserved -->
<!-- 5xxx: reserved -->
<!-- 6xxx: reserved -->
<!-- 7xxx: reserved -->
<!-- 8xxx: reserved -->
<!-- 9xxx: reserved -->
