---
title: Errori durante l'incorporamento .NET
ms.prod: xamarin
ms.assetid: 932C3F0C-D968-42D1-BB14-D97C73361983
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 04/11/2018
ms.openlocfilehash: 677242ea12f8fd87d82f337eafd96a1743ad806a
ms.sourcegitcommit: 4b0582a0f06598f3ff8ad5b817946459fed3c42a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/03/2018
---
# <a name="net-embedding-errors"></a>Incorporamento di errori .NET

## <a name="em0xxx-binding-error-messages"></a>EM0xxx: I messaggi di errore di associazione

Ad esempio, i parametri, ambiente

<!-- 0xxx: the generator itself, e.g. parameters, environment -->

<a name="EM0000" />

### <a name="em0000-unexpected-error---please-fill-a-bug-report-at-httpsgithubcommonoembeddinator-4000issues"></a>EM0000: Errore imprevisto - compilare un report di bug in https://github.com/mono/Embeddinator-4000/issues

Si è verificato un errore imprevisto. . [Un problema del file](https://github.com/mono/Embeddinator-4000/issues) con le informazioni possibili, tra cui:

* Log, di compilazione completa con massimo livello di dettaglio
* Un test case minimo riprodurre l'errore
* Tutte le informazioni di versione

Il modo più semplice per ottenere informazioni sulla versione esatta consiste nell'utilizzare il **Xamarin Studio** menu **su Xamarin Studio** elemento **Mostra dettagli** pulsante e copiare e incollare la versione informazioni (è possibile utilizzare il **copia informazioni** pulsante).

<a name="EM0001" />

### <a name="em0001-could-not-create-output-directory-x"></a>EM0001: Impossibile creare la directory di Output `X`

Il nome della directory specificato da `-o=DIR` non esiste e non può essere creato. Potrebbe essere un nome non valido per il file system.

<a name="EM0002" />

### <a name="em0002-option-x-is-not-supported"></a>EM0002: Opzione `X` non è supportata

Lo strumento non supporta l'opzione `X`. È possibile che un'altra versione dello strumento supporta o non si applica in questo ambiente.

<a name="EM0003" />

### <a name="em0003-the-platform-x-is-not-valid"></a>EM0003: La piattaforma `X` non è valido.

Lo strumento non supporta la piattaforma `X`. È possibile che un'altra versione dello strumento supporta o non si applica in questo ambiente.

<a name="EM0004" />

### <a name="em0004-the-target-x-is-not-valid"></a>EM0004: La destinazione `X` non è valido.

Lo strumento non supporta la destinazione `X`. È possibile che un'altra versione dello strumento supporta o non si applica in questo ambiente.

<a name="EM0005" />

### <a name="em0005-the-compilation-target-x-is-not-valid"></a>EM0005: Destinazione della compilazione `X` non è valido.

Lo strumento non supporta la destinazione di compilazione `X`. È possibile che un'altra versione dello strumento supporta o non si applica in questo ambiente.

<a name="EM0006" />

### <a name="em0006-could-not-find-the-xcode-location"></a>EM0006: Impossibile trovare il percorso di Xcode.

Lo strumento non è stato possibile trovare il percorso Xcode attualmente selezionato usando la `xcode-select -p` comando. Verificare che questo comando ha esito positivo e restituisce il percorso corretto di Xcode.

<a name="EM0007" />

### <a name="em0007-could-not-get-the-sdk-version-for-sdk"></a>EM0007: Impossibile ottenere la versione del sdk per '{sdk}'.

Lo strumento non è stato possibile ottenere la versione SDK utilizzando il `xcrun --show-sdk-version --sdk {sdk}` comando. Verificare che questo comando ha esito positivo e restituisce la versione del SDK.

<a name="EM0008" />

### <a name="em0008-the-architecture-arch-is-not-valid-for-platform-valid-architectures-for-platform-are-architectures"></a>EM0008: L'architettura '{arch}' non è valido per la piattaforma di {}. Le architetture valide per la piattaforma di {} sono: '{architetture}'.

L'architettura nel messaggio di errore non è valido per la piattaforma di destinazione. Verificare che l'opzione - abi viene passato a un'architettura valida.

<a name="EM0009" />

### <a name="em0009-the-feature-x-is-not-currently-implemented-by-the-generator"></a>EM0009: La funzionalità `X` non è attualmente implementato dal generatore

Si tratta di un problema noto che si intende risolvere in una versione futura del generatore. Contributi sono iniziale.

<a name="EM0010" />

### <a name="em0010-cant-merge-the-frameworks-simulatorframework-and-deviceframework-because-the-file-file-exists-in-both"></a>EM0010: Impossibile eseguire il merge di Framework '{simulatorFramework}' e '{deviceFramework}' perché il file ' {}' esiste in entrambi.

Lo strumento non può unire i Framework indicati nel messaggio di errore, perché è presente un file comune tra di essi.

Ciò potrebbe indicare un errore durante l'incorporamento .NET; inviare una segnalazione di bug in [ https://github.com/mono/Embeddinator-4000/issues ](https://github.com/mono/Embeddinator-4000/issues) con un test case.

<a name="EM0011" />

### <a name="em0011-the-assembly-x-does-not-exist"></a>EM0011: L'assembly `X` non esiste.

Lo strumento non è riuscito a trovare l'assembly `X` specificato negli argomenti.

<a name="EM0012" />

### <a name="em0012-the-assembly-name-x-is-not-unique"></a>EM0012: Il nome dell'assembly `X` non univoco

Più di un assembly fornito ha il nome stesso, interno e non sarebbe possibile distinguere tra di essi in fase di esecuzione.

La causa più probabile è che un assembly è stato specificato più volte agli argomenti della riga di comando. Tuttavia non sono un mantiene ancora assembly rinominato è il nome originale e più copie coesiste.

<a name="EM0013" />

### <a name="em0013-cant-find-the-assembly-x-referenced-by-y"></a>EM0013: Impossibile trovare l'assembly 'X', 'Y' a cui fa riferimento.

Lo strumento Impossibile trovare l'assembly 'X', a cui fa riferimento l'assembly 'Y'. Verificare che tutti gli assembly cui viene fatto riferimenti siano nella stessa directory dell'assembly da associare.

<a name="EM0014" />

### <a name="em0014-could-not-find-product-product-minversion-is-required"></a>EM0014: Impossibile trovare {prodotto} ({prodotto} {min_version} è obbligatorio).

Impossibile trovare la dipendenza indicata nel messaggio di errore nel sistema.

<a name="EM0015" />

### <a name="em0015-could-not-find-a-valid-version-of-product-found-version-but-at-least-minversion-is-required"></a>EM0015: Impossibile trovare una versione valida di {} (trovato {version}, ma almeno {min_version} è obbligatorio).

La dipendenza indicato nell'errore messaggio è stato trovato nel sistema, ma è troppo vecchio. Eseguire l'aggiornamento a una versione più recente.

<a name="EM0016" />

### <a name="em0016-could-not-create-symlink-file---target-error-number"></a>EM0016: Collegamento simbolico non è stato possibile creare '{file}' -> '{target}': errore {numero}

Impossibile creare il collegamento simbolico indicato nel messaggio di errore.

<a name="EM0026" />

### <a name="em0026-could-not-parse-the-command-line-argument-a-"></a>EM0026 Impossibile analizzare l'argomento della riga di comando 'A': *

La sintassi per l'opzione della riga di comando `A` non può essere analizzato dallo strumento. È probabile che non è corretto, verificare con la documentazione o la Guida per la sintassi corretta.

<a name="EM0099" />

### <a name="em0099-internal-error--please-file-a-bug-report-with-a-test-case-httpsgithubcommonoembeddinator-4000issues"></a>: EM0099 Errore interno *. Inviare una segnalazione di bug con un test case (https://github.com/mono/Embeddinator-4000/issues).

Questo messaggio di errore viene segnalato quando una verifica di coerenza interna in .NET incorporamento non riesce.

Questo indica un bug in .NET incorporamento; inviare una segnalazione di bug in [ https://github.com/mono/Embeddinator-4000/issues ](https://github.com/mono/Embeddinator-4000/issues) con un test case.

<!-- 1xxx: code processing -->

## <a name="em1xxx-code-processing"></a>EM1xxx: Elaborazione del codice

<a name="EM1010" />

### <a name="em1010-type-t-is-not-generated-because-x-are-not-supported"></a>EM1010: Digitare `T` non viene generato perché `X` non sono supportati.

Si tratta di un **avviso** che il tipo `T` verranno ignorate (ad esempio non verrà generato) perché utilizza `X`, una funzionalità che non è supportata.

Nota: Evolveranno funzionalità supportate con le nuove versioni dello strumento.

<a name="EM1011" />

### <a name="em1011-type-t-is-not-generated-because-it-lacks-marshaling-code-with-a-native-counterpart"></a>EM1011: Digitare `T` non viene generato perché manca codice di marshalling con un equivalente nativo.

Si tratta di un **avviso** che il tipo `T` verranno ignorate (ad esempio non verrà generato) poiché espone da .NET framework che richiede molto marshalling.

Nota: Questo è un valore che potrebbe ottenere supportati, con alcune limitazioni, in una versione futura dello strumento.

<a name="EM1020" />

### <a name="em1020-constructor-c-is-not-generated-because-of-parameter-type-t-is-not-supported"></a>EM1020: Costruttore `C` non viene generato a causa di tipo di parametro `T` non è supportata.

Si tratta di un **avviso** che il costruttore `C` verranno ignorate (ad esempio non verrà generato) perché un parametro di tipo `T` non è supportata.

Dovrebbe esserci un avviso precedente contenente ulteriori informazioni, perché digitare `T` non è supportata.

Nota: Evolveranno funzionalità supportate con le nuove versioni dello strumento.

<a name="EM1021" />

### <a name="em1021-constructor-c-has-default-values-for-which-no-wrapper-is-generated"></a>EM1021: Costruttore `C` dispone di valori predefiniti per il quale non viene generato alcun wrapper.

Si tratta di un **avviso** che i parametri predefiniti di costruttore `C` non generano codice aggiuntivo. La causa più comune è che un metodo esistente già la stessa firma. Ad esempio, in .net è possibile disporre di:

```
public class MyType {
    public MyType () { ... }
    public MyType (int i = 0) { ... }
}
```

In questi casi solo due generati `init` verranno creati i selettori, sia la chiamata a Mono, ma non sarà disponibile alcun wrapper per le versioni successive.

<a name="EM1030" />

### <a name="em1030-method-m-is-not-generated-because-return-type-t-is-not-supported"></a>EM1030: Metodo `M` non viene generato perché il tipo restituito `T` non è supportata.

Si tratta di un **avviso** che il metodo `M` verranno ignorate (ad esempio non verrà generato) perché è un tipo restituito `T` non è supportata.

Dovrebbe esserci un avviso precedente contenente ulteriori informazioni, perché digitare `T` non è supportata.

Nota: Evolveranno funzionalità supportate con le nuove versioni dello strumento.

<a name="EM1031" />

### <a name="em1031-method-m-is-not-generated-because-of-parameter-type-t-is-not-supported"></a>EM1031: Metodo `M` non viene generato a causa di tipo di parametro `T` non è supportata.

Si tratta di un **avviso** che il metodo `M` verranno ignorate (ad esempio non verrà generato) perché un parametro di tipo `T` non è supportata.

Dovrebbe esserci un avviso precedente contenente ulteriori informazioni, perché digitare `T` non è supportata.

Nota: Evolveranno funzionalità supportate con le nuove versioni dello strumento.

<a name="EM1032" />

### <a name="em1032-method-m-has-default-values-for-which-no-wrapper-is-generated"></a>EM1032: Metodo `M` dispone di valori predefiniti per il quale non viene generato alcun wrapper.

Si tratta di un **avviso** che i parametri predefiniti del metodo `M` non generano codice aggiuntivo. La causa più comune è che un metodo esistente già la stessa firma. Ad esempio, in .net è possibile disporre di:

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

### <a name="em1034-extension-method-m-is-not-generated-inside-a-category-because-they-cannot-be-created-on-primitive-type-t-a-normal-static-method-was-generated"></a>EM1034: Metodo di estensione `M` non viene generato all'interno di una categoria perché non è possibile creare nel tipo primitivo `T`. È stato generato un metodo statico normale.

Si tratta di un **avviso** che tipo di un metodo di estensione in un primivite (ad esempio `System.Int32`) è stato trovato. In Objective-C non è possibile creare categorie di tipo primitivo. Invece il generatore produrrà un metodo statico normale.

<a name="EM1040" />

### <a name="em1040-property-p-is-not-generated-because-of-parameter-type-t-is-not-supported"></a>EM1040: Proprietà `P` non viene generato a causa di tipo di parametro `T` non è supportata.

Si tratta di un **avviso** che la proprietà `P` verranno ignorate (ad esempio non verrà generato) perché il tipo esposto `T` non è supportata.

Dovrebbe esserci un avviso precedente contenente ulteriori informazioni, perché digitare `T` non è supportata.

Nota: Evolveranno funzionalità supportate con le nuove versioni dello strumento.

<a name="EM1041" />

### <a name="em1041-indexed-properties-on-t-is-not-generated-because-multiple-indexed-properties-are-not-supported"></a>EM1041: Proprietà indicizzate in `T` non viene generato perché più proprietà indicizzate non sono supportate.

Si tratta di un **avviso** che le proprietà indicizzate `T` verranno ignorate (ad esempio non verrà generato) perché non sono supportate più proprietà indicizzate.

<a name="EM1050" />

### <a name="em1050-field-f-is-not-generated-because-of-field-type-t-is-not-supported"></a>EM1050: Campo `F` non viene generato a causa di tipo di campo `T` non è supportata.

Si tratta di un **avviso** che il campo `F` verranno ignorate (ad esempio non verrà generato) perché il tipo esposto `T` non è supportata.

Dovrebbe esserci un avviso precedente contenente ulteriori informazioni, perché digitare `T` non è supportata.

Nota: Evolveranno funzionalità supportate con le nuove versioni dello strumento.

<a name="EM1051" />

### <a name="em1051-element-e-is-generated-instead-as-f-because-its-name-conflicts-with-an-important-objective-c-selector"></a>EM1051: Elemento `E` viene invece generato come `F` perché il relativo nome in conflitto con un'importante selettore objective-c.

Si tratta di un **avviso** che l'elemento `E` verrà generato come `F` perché il relativo nome in conflitto con un selettore objective-c importanti.

Selettori nel [NSObjectProtocol](https://developer.apple.com/reference/objectivec/1418956-nsobject?language=objc) hanno un significato importante in objective-c e deve essere sottoposto a override con attenzione.

Nota: L'elenco di selettori riservati evolveranno con le nuove versioni dello strumento.

<a name="EM1052" />

### <a name="em1052-element-e-is-not-generated-its-name-conflicts-with-other-elements-on-the-same-class"></a>EM1052: Elemento `E` non viene generato il nome è in conflitto con altri elementi nella stessa classe.

Si tratta di un **avviso** quell'elemento `E` non viene generato come il nome è in conflitto con altri elementi nella stessa classe.

<a name="EM1053" />

### <a name="em1053-target-e-is-not-supported-for-xamarinios-and-xamarinmac-only-the-framework-option-is-considered-supported-and-should-be-used"></a>EM1053: Destinazione `E` non è supportata per xamarin. IOS e Xamarin.Mac. Solo il `framework` opzione viene considerata supportata e deve essere utilizzato.

Si tratta di un **avviso** destinati `E` viene considerata non supportato per xamarin. IOS e Xamarin.Mac casi d'uso. 

Utilizzo delle librerie .NET incorporamento statiche o dinamiche potrebbe richiedere passaggi ulteriori operazioni o modifiche e deve essere evitato nella maggior parte dei casi d'uso.

Provare a rimuovere il `--target` parametro o passare `--target=framework` invece.

<!-- 2xxx: code generation -->

## <a name="em2xxx-code-generation"></a>EM2xxx: Generazione del codice

<!-- 3xxx: reserved -->
<!-- 4xxx: reserved -->
<!-- 5xxx: reserved -->
<!-- 6xxx: reserved -->
<!-- 7xxx: reserved -->
<!-- 8xxx: reserved -->
<!-- 9xxx: reserved -->
