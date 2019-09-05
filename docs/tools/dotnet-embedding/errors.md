---
title: Errori di incorporamento .NET
description: Questo documento descrive gli errori generati dall'incorporamento .NET. Gli errori sono elencati in base al codice ed è stata fornita una descrizione per facilitare la risoluzione dei problemi.
ms.prod: xamarin
ms.assetid: 932C3F0C-D968-42D1-BB14-D97C73361983
author: conceptdev
ms.author: crdun
ms.date: 04/11/2018
ms.openlocfilehash: be62ba212cb92e031f760ef3f267bc3eac91c748
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70282159"
---
# <a name="net-embedding-errors"></a>Errori di incorporamento .NET

## <a name="em0xxx-binding-error-messages"></a>EM0xxx: Messaggi di errore di associazione

ad esempio parametri, ambiente

<!-- 0xxx: the generator itself, e.g. parameters, environment -->

<a name="EM0000" />

### <a name="em0000-unexpected-error---please-fill-a-bug-report-at-httpsgithubcommonoembeddinator-4000issues"></a>EM0000: Errore imprevisto. compilare un report sui bug in https://github.com/mono/Embeddinator-4000/issues

Si è verificata una condizione di errore imprevista. Inviare [un problema](https://github.com/mono/Embeddinator-4000/issues) con il maggior numero di informazioni possibile, tra cui:

* Log di compilazione completi, con livello di dettaglio massimo
* Un test case minimo che riproduce l'errore
* Tutte le informazioni sulla versione

Il modo più semplice per ottenere le informazioni esatte sulla versione consiste nell'usare il menu **Xamarin Studio** , informazioni **su Xamarin Studio** elemento, visualizzare il pulsante **Dettagli** e copiare e incollare le informazioni sulla versione. è possibile usare il pulsante **copia informazioni** .

<a name="EM0001" />

### <a name="em0001-could-not-create-output-directory-x"></a>EM0001: Non è stato possibile creare la directory di output`X`

Il nome di directory specificato `-o=DIR` da non esiste e non è stato possibile crearlo. Potrebbe trattarsi di un nome non valido per la file system.

<a name="EM0002" />

### <a name="em0002-option-x-is-not-supported"></a>EM0002: L' `X` opzione non è supportata

Lo strumento non supporta l'opzione `X`. È possibile che un'altra versione dello strumento lo supporti o che non si applichi a questo ambiente.

<a name="EM0003" />

### <a name="em0003-the-platform-x-is-not-valid"></a>EM0003: Piattaforma `X` non valida.

Lo strumento non supporta la piattaforma `X`. È possibile che un'altra versione dello strumento lo supporti o che non si applichi a questo ambiente.

<a name="EM0004" />

### <a name="em0004-the-target-x-is-not-valid"></a>EM0004: Destinazione `X` non valida.

Lo strumento non supporta la destinazione `X`. È possibile che un'altra versione dello strumento lo supporti o che non si applichi a questo ambiente.

<a name="EM0005" />

### <a name="em0005-the-compilation-target-x-is-not-valid"></a>EM0005: La destinazione `X` di compilazione non è valida.

Lo strumento non supporta la destinazione `X`di compilazione. È possibile che un'altra versione dello strumento lo supporti o che non si applichi a questo ambiente.

<a name="EM0006" />

### <a name="em0006-could-not-find-the-xcode-location"></a>EM0006: Il percorso di Xcode non è stato trovato.

Lo strumento non è riuscito a trovare il percorso di Xcode attualmente `xcode-select -p` selezionato tramite il comando. Verificare che il comando abbia esito positivo e che restituisca la posizione di Xcode corretta.

<a name="EM0007" />

### <a name="em0007-could-not-get-the-sdk-version-for-sdk"></a>EM0007: Non è stato possibile ottenere la versione dell'SDK per ' {SDK}'.

Lo strumento non è riuscito a ottenere la versione dell' `xcrun --show-sdk-version --sdk {sdk}` SDK utilizzando il comando. Verificare che il comando abbia esito positivo e che restituisca la versione dell'SDK.

<a name="EM0008" />

### <a name="em0008-the-architecture-arch-is-not-valid-for-platform-valid-architectures-for-platform-are-architectures"></a>EM0008: L'architettura ' {Arch}' non è valida per {Platform}. Le architetture valide per {Platform} sono:' {Architectures}'.

L'architettura del messaggio di errore non è valida per la piattaforma di destinazione. Verificare che l'opzione--Abi sia stata passata a un'architettura valida.

<a name="EM0009" />

### <a name="em0009-the-feature-x-is-not-currently-implemented-by-the-generator"></a>EM0009: La funzionalità `X` non è attualmente implementata dal generatore

Si tratta di un problema noto che si intende correggere in una versione futura del generatore. I contributi sono benvenuti.

<a name="EM0010" />

### <a name="em0010-cant-merge-the-frameworks-simulatorframework-and-deviceframework-because-the-file-file-exists-in-both"></a>EM0010: Non è possibile unire i Framework ' {simulatorFramework}' è {deviceFramework}' perché il file ' {file}' esiste in entrambi.

Lo strumento non è stato in grado di unire i Framework indicati nel messaggio di errore perché esiste un file comune tra di essi.

Questo potrebbe indicare un bug nell'incorporamento di .NET; inviare un report sui bug in [https://github.com/mono/Embeddinator-4000/issues](https://github.com/mono/Embeddinator-4000/issues) con un test case.

<a name="EM0011" />

### <a name="em0011-the-assembly-x-does-not-exist"></a>EM0011: Assembly `X` inesistente.

Lo strumento non è stato in grado `X` di trovare l'assembly specificato negli argomenti.

<a name="EM0012" />

### <a name="em0012-the-assembly-name-x-is-not-unique"></a>EM0012: Il nome `X` dell'assembly non è univoco

Più assembly specificati hanno lo stesso nome interno e non sarebbe possibile distinguerli in fase di esecuzione.

La causa più probabile è che un assembly venga specificato più volte negli argomenti della riga di comando. Tuttavia, un assembly rinominato mantiene ancora il nome originale e più copie non possono coesistere.

<a name="EM0013" />

### <a name="em0013-cant-find-the-assembly-x-referenced-by-y"></a>EM0013: Non è possibile trovare l'assembly ' X ', a cui fa riferimento ' Y '.

Lo strumento non è stato in grado di trovare l'assembly ' X ' a cui fa riferimento l'assembly ' Y '. Assicurarsi che tutti gli assembly a cui si fa riferimento si trovino nella stessa directory dell'assembly da associare.

<a name="EM0014" />

### <a name="em0014-could-not-find-product-product-min_version-is-required"></a>EM0014: Non è stato possibile trovare {Product} ({Product} {min_version} è obbligatorio).

Impossibile trovare nel sistema la dipendenza indicata nel messaggio di errore.

<a name="EM0015" />

### <a name="em0015-could-not-find-a-valid-version-of-product-found-version-but-at-least-min_version-is-required"></a>EM0015: Non è stato possibile trovare una versione valida di {Product} (trovata {Version}, ma è necessario almeno {min_version}).

La dipendenza indicata nel messaggio di errore è stata rilevata nel sistema, ma è troppo vecchia. Eseguire l'aggiornamento a una versione più recente.

<a name="EM0016" />

### <a name="em0016-could-not-create-symlink-file---target-error-number"></a>EM0016: Non è stato possibile creare il collegamento simbolico ' {file}'->' {target}': errore {Number}

Non è stato possibile creare il collegamento simbolico indicato nel messaggio di errore.

<a name="EM0026" />

### <a name="em0026-could-not-parse-the-command-line-argument-a-"></a>EM0026 non è riuscito ad analizzare l'argomento della riga di comando ' A ': *

La sintassi specificata per l'opzione `A` della riga di comando non può essere analizzata dallo strumento. Probabilmente non è corretto. per la sintassi corretta, consultare la documentazione o la guida.

<a name="EM0099" />

### <a name="em0099-internal-error--please-file-a-bug-report-with-a-test-case-httpsgithubcommonoembeddinator-4000issues"></a>EM0099: Errore interno *. Inviare un report sui bug con un test case (https://github.com/mono/Embeddinator-4000/issues).

Questo messaggio di errore viene segnalato quando un controllo di coerenza interno nell'incorporamento di .NET ha esito negativo.

Indica un bug nell'incorporamento di .NET; inviare un report sui bug in [https://github.com/mono/Embeddinator-4000/issues](https://github.com/mono/Embeddinator-4000/issues) con un test case.

<!-- 1xxx: code processing -->

## <a name="em1xxx-code-processing"></a>EM1xxx: Elaborazione del codice

<a name="EM1010" />

### <a name="em1010-type-t-is-not-generated-because-x-are-not-supported"></a>EM1010: Il `T` tipo non viene generato `X` perché non è supportato.

Si tratta di un **avviso** che indica `T` che il tipo verrà ignorato (ovvero non verrà generato nulla) perché usa `X`, una funzionalità non supportata.

Nota: Le funzionalità supportate si evolveranno con le nuove versioni dello strumento.

<a name="EM1011" />

### <a name="em1011-type-t-is-not-generated-because-it-lacks-marshaling-code-with-a-native-counterpart"></a>EM1011: Il `T` tipo non viene generato perché manca il marshalling del codice con una controparte nativa.

Si tratta di un **avviso** che indica `T` che il tipo verrà ignorato (ovvero non verrà generato nulla) perché espone qualcosa da .NET Framework che richiede il marshalling aggiuntivo.

Nota: Si tratta di un elemento che potrebbe essere supportato, con alcune limitazioni, in una versione futura dello strumento.

<a name="EM1020" />

### <a name="em1020-constructor-c-is-not-generated-because-of-parameter-type-t-is-not-supported"></a>EM1020: Il `C` costruttore non è stato generato perché il `T` tipo di parametro non è supportato.

Si tratta di un **avviso** che indica `C` che il costruttore verrà ignorato, ovvero non verrà generato nulla, perché un parametro di `T` tipo non è supportato.

Dovrebbe esserci un avviso precedente che fornisce altre informazioni perché il `T` tipo non è supportato.

Nota: Le funzionalità supportate si evolveranno con le nuove versioni dello strumento.

<a name="EM1021" />

### <a name="em1021-constructor-c-has-default-values-for-which-no-wrapper-is-generated"></a>EM1021: Il `C` costruttore dispone di valori predefiniti per i quali non viene generato alcun wrapper.

Si tratta di un **avviso** che indica che i parametri `C` predefiniti del costruttore non generano codice aggiuntivo. La ragione più comune è che un metodo esistente ha già la stessa firma. ad esempio in .NET è possibile avere:

```csharp
public class MyType {
    public MyType () { ... }
    public MyType (int i = 0) { ... }
}
```

In questi casi verranno creati solo `init` due selettori generati, che eseguono entrambe le chiamate in mono, ma non esiste alcun wrapper per il successivo.

<a name="EM1030" />

### <a name="em1030-method-m-is-not-generated-because-return-type-t-is-not-supported"></a>EM1030: Il `M` metodo non viene generato perché il `T` tipo restituito non è supportato.

Si tratta di un **avviso** che indica `M` che il metodo verrà ignorato, ovvero che non verrà generato alcun elemento, perché il `T` tipo restituito non è supportato.

Dovrebbe esserci un avviso precedente che fornisce altre informazioni perché il `T` tipo non è supportato.

Nota: Le funzionalità supportate si evolveranno con le nuove versioni dello strumento.

<a name="EM1031" />

### <a name="em1031-method-m-is-not-generated-because-of-parameter-type-t-is-not-supported"></a>EM1031: Il `M` metodo non viene generato perché il tipo `T` di parametro non è supportato.

Si tratta di un **avviso** che indica `M` che il metodo verrà ignorato, ovvero che non verrà generato alcun elemento, perché un `T` parametro di tipo non è supportato.

Dovrebbe esserci un avviso precedente che fornisce altre informazioni perché il `T` tipo non è supportato.

Nota: Le funzionalità supportate si evolveranno con le nuove versioni dello strumento.

<a name="EM1032" />

### <a name="em1032-method-m-has-default-values-for-which-no-wrapper-is-generated"></a>EM1032: Il `M` metodo ha valori predefiniti per i quali non viene generato alcun wrapper.

Si tratta di un **avviso** che indica che i parametri `M` predefiniti del metodo non generano codice aggiuntivo. La ragione più comune è che un metodo esistente ha già la stessa firma. ad esempio in .NET è possibile avere:

```csharp
public class MyType {
    public int Increment () { ... }
    public int Increment (int i = 0) { ... }
}
```

In questi casi verranno creati solo `increment` due selettori generati, che eseguono entrambe le chiamate in mono, ma non esiste alcun wrapper per il successivo.

<a name="EM1033" />

### <a name="em1033-method-m-is-not-generated-because-another-method-exposes-the-operator-with-a-friendly-name"></a>EM1033: Il `M` metodo non viene generato perché un altro metodo espone l'operatore con un nome descrittivo.

Si tratta di un **avviso** che indica `M` che il metodo non viene generato perché un altro metodo espone l'operatore con un nome descrittivo. (https://msdn.microsoft.com/library/ms229032(v=vs.110).aspx)

<a name="EM1034" />

### <a name="em1034-extension-method-m-is-not-generated-inside-a-category-because-they-cannot-be-created-on-primitive-type-t-a-normal-static-method-was-generated"></a>EM1034: Il metodo `M` di estensione non viene generato all'interno di una categoria perché non è possibile `T`crearlo in un tipo primitivo. È stato generato un metodo statico normale.

Si tratta di un **avviso** che indica che è stato trovato un metodo di estensione `System.Int32`su un tipo primivite (ad esempio). In Objective-C non è possibile creare categorie per il tipo primitivo. Il generatore produrrà invece un metodo statico normale.

<a name="EM1040" />

### <a name="em1040-property-p-is-not-generated-because-of-parameter-type-t-is-not-supported"></a>EM1040: La `P` proprietà non è generata perché il tipo `T` di parametro non è supportato.

Si tratta di un **avviso** che indica `P` che la proprietà verrà ignorata, ovvero che non verrà generato nulla, perché `T` il tipo esposto non è supportato.

Dovrebbe esserci un avviso precedente che fornisce altre informazioni perché il `T` tipo non è supportato.

Nota: Le funzionalità supportate si evolveranno con le nuove versioni dello strumento.

<a name="EM1041" />

### <a name="em1041-indexed-properties-on-t-is-not-generated-because-multiple-indexed-properties-are-not-supported"></a>EM1041: Le proprietà indicizzate `T` in non vengono generate perché non sono supportate più proprietà indicizzate.

Si tratta di un **avviso** che indica che le proprietà `T` indicizzate in verranno ignorate, ovvero che non verranno generati elementi, perché non sono supportate più proprietà indicizzate.

<a name="EM1050" />

### <a name="em1050-field-f-is-not-generated-because-of-field-type-t-is-not-supported"></a>EM1050: Il `F` campo non è generato perché il tipo `T` di campo non è supportato.

Si tratta di un **avviso** che indica `F` che il campo verrà ignorato, ovvero non verrà generato alcun elemento, perché il `T` tipo esposto non è supportato.

Dovrebbe esserci un avviso precedente che fornisce altre informazioni perché il `T` tipo non è supportato.

Nota: Le funzionalità supportate si evolveranno con le nuove versioni dello strumento.

<a name="EM1051" />

### <a name="em1051-element-e-is-generated-instead-as-f-because-its-name-conflicts-with-an-important-objective-c-selector"></a>EM1051: Viene `E` invece generato un elemento `F` come perché il nome è in conflitto con un selettore Objective-c importante.

Si tratta di un **avviso** che indica `E` che l'elemento verrà generato `F` invece come perché il nome è in conflitto con un selettore Objective-c importante.

I selettori in [NSObjectProtocol](https://developer.apple.com/reference/objectivec/1418956-nsobject?language=objc) hanno un significato importante in Objective-c e devono essere sottoposti a override con cautela.

Nota: L'elenco dei selettori riservati verrà sviluppato con le nuove versioni dello strumento.

<a name="EM1052" />

### <a name="em1052-element-e-is-not-generated-its-name-conflicts-with-other-elements-on-the-same-class"></a>EM1052: Il `E` nome dell'elemento non è in conflitto con altri elementi nella stessa classe.

Si tratta di un **avviso** che `E` indica che l'elemento non viene generato perché il nome è in conflitto con altri elementi nella stessa classe.

<a name="EM1053" />

### <a name="em1053-target-e-is-not-supported-for-xamarinios-and-xamarinmac-only-the-framework-option-is-considered-supported-and-should-be-used"></a>EM1053: Target `E` non è supportato per Novell. iOS e Novell. Mac. Solo l' `framework` opzione è considerata supportata e deve essere usata.

Si tratta di un **avviso** che `E` indica che la destinazione è considerata non supportata per i casi d'uso Novell. iOS e Novell. Mac. 

Il consumo di librerie di incorporamento .NET statiche o dinamiche può richiedere passaggi aggiuntivi o modifiche di lavoro e deve essere evitato nella maggior parte dei casi d'uso.

Provare a rimuovere `--target` il parametro o `--target=framework` passare.

<!-- 2xxx: code generation -->

## <a name="em2xxx-code-generation"></a>EM2xxx: Generazione del codice

<!-- 3xxx: reserved -->
<!-- 4xxx: reserved -->
<!-- 5xxx: reserved -->
<!-- 6xxx: reserved -->
<!-- 7xxx: reserved -->
<!-- 8xxx: reserved -->
<!-- 9xxx: reserved -->
