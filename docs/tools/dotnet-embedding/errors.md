---
title: Errori di incorporamento .NET
description: Questo documento descrive gli errori generati dall'incorporamento .NET. Gli errori sono elencati in base al codice ed è stata fornita una descrizione per facilitare la risoluzione dei problemi.
ms.prod: xamarin
ms.assetid: 932C3F0C-D968-42D1-BB14-D97C73361983
author: davidortinau
ms.author: daortin
ms.date: 04/11/2018
ms.openlocfilehash: b7cdf56ac4d917c8692f33d388adc003fabd9cac
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73007240"
---
# <a name="net-embedding-errors"></a>Errori di incorporamento .NET

## <a name="em0xxx-binding-error-messages"></a>EM0xxx: associazione dei messaggi di errore

Ad esempio, parametri, ambiente

<!-- 0xxx: the generator itself, e.g. parameters, environment -->

<a name="EM0000" />

### <a name="em0000-unexpected-error---please-fill-a-bug-report-at-httpsgithubcommonoembeddinator-4000issues"></a>EM0000: errore imprevisto. compilare un report sui bug all'https://github.com/mono/Embeddinator-4000/issues

Si è verificata una condizione di errore imprevista. Inviare [un problema](https://github.com/mono/Embeddinator-4000/issues) con il maggior numero di informazioni possibile, tra cui:

* Log di compilazione completi, con livello di dettaglio massimo
* Un test case minimo che riproduce l'errore
* Tutte le informazioni sulla versione

Il modo più semplice per ottenere le informazioni esatte sulla versione consiste nell'usare il menu **Xamarin Studio** , informazioni **su Xamarin Studio** elemento, visualizzare il pulsante **Dettagli** e copiare e incollare le informazioni sulla versione. è possibile usare il pulsante **copia informazioni** .

<a name="EM0001" />

### <a name="em0001-could-not-create-output-directory-x"></a>EM0001: non è stato possibile creare la directory di output `X`

Il nome di directory specificato da `-o=DIR` non esiste e non è stato possibile crearlo. Potrebbe trattarsi di un nome non valido per la file system.

<a name="EM0002" />

### <a name="em0002-option-x-is-not-supported"></a>EM0002: l'opzione `X` non è supportata

Lo strumento non supporta l'opzione `X`. È possibile che un'altra versione dello strumento lo supporti o che non si applichi a questo ambiente.

<a name="EM0003" />

### <a name="em0003-the-platform-x-is-not-valid"></a>EM0003: il `X` della piattaforma non è valido.

Lo strumento non supporta la piattaforma `X`. È possibile che un'altra versione dello strumento lo supporti o che non si applichi a questo ambiente.

<a name="EM0004" />

### <a name="em0004-the-target-x-is-not-valid"></a>EM0004: il `X` di destinazione non è valido.

Lo strumento non supporta la `X`di destinazione. È possibile che un'altra versione dello strumento lo supporti o che non si applichi a questo ambiente.

<a name="EM0005" />

### <a name="em0005-the-compilation-target-x-is-not-valid"></a>EM0005: il `X` di destinazione della compilazione non è valido.

Lo strumento non supporta la `X`di destinazione della compilazione. È possibile che un'altra versione dello strumento lo supporti o che non si applichi a questo ambiente.

<a name="EM0006" />

### <a name="em0006-could-not-find-the-xcode-location"></a>EM0006: non è stato possibile trovare il percorso di Xcode.

Lo strumento non è riuscito a trovare il percorso di Xcode attualmente selezionato usando il comando `xcode-select -p`. Verificare che il comando abbia esito positivo e che restituisca la posizione di Xcode corretta.

<a name="EM0007" />

### <a name="em0007-could-not-get-the-sdk-version-for-sdk"></a>EM0007: non è stato possibile ottenere la versione dell'SDK per ' {SDK}'.

Lo strumento non è riuscito a ottenere la versione dell'SDK utilizzando il comando `xcrun --show-sdk-version --sdk {sdk}`. Verificare che il comando abbia esito positivo e che restituisca la versione dell'SDK.

<a name="EM0008" />

### <a name="em0008-the-architecture-arch-is-not-valid-for-platform-valid-architectures-for-platform-are-architectures"></a>EM0008: l'architettura ' {Arch}' non è valida per {Platform}. Le architetture valide per {Platform} sono:' {Architectures}'.

L'architettura del messaggio di errore non è valida per la piattaforma di destinazione. Verificare che l'opzione--Abi sia stata passata a un'architettura valida.

<a name="EM0009" />

### <a name="em0009-the-feature-x-is-not-currently-implemented-by-the-generator"></a>EM0009: la funzionalità `X` non è attualmente implementata dal generatore

Si tratta di un problema noto che si intende correggere in una versione futura del generatore. I contributi sono benvenuti.

<a name="EM0010" />

### <a name="em0010-cant-merge-the-frameworks-simulatorframework-and-deviceframework-because-the-file-file-exists-in-both"></a>EM0010: non è possibile unire i Framework ' {simulatorFramework}' è {deviceFramework}' perché il file ' {file}' esiste in entrambi.

Lo strumento non è stato in grado di unire i Framework indicati nel messaggio di errore perché esiste un file comune tra di essi.

Questo potrebbe indicare un bug nell'incorporamento di .NET; inviare un report sui bug in [https://github.com/mono/Embeddinator-4000/issues](https://github.com/mono/Embeddinator-4000/issues) con una test case.

<a name="EM0011" />

### <a name="em0011-the-assembly-x-does-not-exist"></a>EM0011: l'assembly `X` non esiste.

Lo strumento non è stato in grado di trovare l'assembly `X` specificato negli argomenti.

<a name="EM0012" />

### <a name="em0012-the-assembly-name-x-is-not-unique"></a>EM0012: il nome dell'assembly `X` non è univoco

Più assembly specificati hanno lo stesso nome interno e non sarebbe possibile distinguerli in fase di esecuzione.

La causa più probabile è che un assembly venga specificato più volte negli argomenti della riga di comando. Tuttavia, un assembly rinominato mantiene ancora il nome originale e più copie non possono coesistere.

<a name="EM0013" />

### <a name="em0013-cant-find-the-assembly-x-referenced-by-y"></a>EM0013: Impossibile trovare l'assembly ' X ', a cui fa riferimento "Y".

Lo strumento non è stato in grado di trovare l'assembly ' X ' a cui fa riferimento l'assembly ' Y '. Assicurarsi che tutti gli assembly a cui si fa riferimento si trovino nella stessa directory dell'assembly da associare.

<a name="EM0014" />

### <a name="em0014-could-not-find-product-product-min_version-is-required"></a>EM0014: non è stato possibile trovare {Product} ({Product} {min_version} è obbligatorio).

Impossibile trovare nel sistema la dipendenza indicata nel messaggio di errore.

<a name="EM0015" />

### <a name="em0015-could-not-find-a-valid-version-of-product-found-version-but-at-least-min_version-is-required"></a>EM0015: non è stato possibile trovare una versione valida di {Product} (trovata {Version}, ma è necessario almeno {min_version}).

La dipendenza indicata nel messaggio di errore è stata rilevata nel sistema, ma è troppo vecchia. Eseguire l'aggiornamento a una versione più recente.

<a name="EM0016" />

### <a name="em0016-could-not-create-symlink-file---target-error-number"></a>EM0016: non è stato possibile creare il collegamento simbolico ' {file}'->' {target}': errore {Number}

Non è stato possibile creare il collegamento simbolico indicato nel messaggio di errore.

<a name="EM0026" />

### <a name="em0026-could-not-parse-the-command-line-argument-a-"></a>EM0026 non è riuscito ad analizzare l'argomento della riga di comando ' A ': *

La sintassi specificata per l'opzione della riga di comando `A` non può essere analizzata dallo strumento. Probabilmente non è corretto. per la sintassi corretta, consultare la documentazione o la guida.

<a name="EM0099" />

### <a name="em0099-internal-error--please-file-a-bug-report-with-a-test-case-httpsgithubcommonoembeddinator-4000issues"></a>EM0099: errore interno *. Inviare un report sui bug con un test case (https://github.com/mono/Embeddinator-4000/issues).

Questo messaggio di errore viene segnalato quando un controllo di coerenza interno nell'incorporamento di .NET ha esito negativo.

Indica un bug nell'incorporamento di .NET; inviare un report sui bug in [https://github.com/mono/Embeddinator-4000/issues](https://github.com/mono/Embeddinator-4000/issues) con una test case.

<!-- 1xxx: code processing -->

## <a name="em1xxx-code-processing"></a>EM1xxx: elaborazione del codice

<a name="EM1010" />

### <a name="em1010-type-t-is-not-generated-because-x-are-not-supported"></a>EM1010: il tipo `T` non viene generato perché `X` non sono supportati.

Si tratta di un **avviso** che indica che il tipo `T` verrà ignorato, ovvero non verrà generato nulla, perché utilizza `X`, una funzionalità non supportata.

Nota: le funzionalità supportate si evolveranno con le nuove versioni dello strumento.

<a name="EM1011" />

### <a name="em1011-type-t-is-not-generated-because-it-lacks-marshaling-code-with-a-native-counterpart"></a>EM1011: il tipo `T` non viene generato perché manca il marshalling del codice con una controparte nativa.

Si tratta di un **avviso** che indica che il tipo `T` verrà ignorato (ad esempio, non verrà generato alcun elemento) perché espone qualcosa da .NET Framework che richiede il marshalling aggiuntivo.

Nota: si tratta di un elemento che potrebbe essere supportato, con alcune limitazioni, in una versione futura dello strumento.

<a name="EM1020" />

### <a name="em1020-constructor-c-is-not-generated-because-of-parameter-type-t-is-not-supported"></a>EM1020: il costruttore `C` non viene generato perché il tipo di parametro `T` non è supportato.

Si tratta di un **avviso** che indica che il costruttore `C` verrà ignorato, ovvero non verrà generato alcun elemento, perché un parametro di tipo `T` non è supportato.

Verrà visualizzato un avviso precedente che fornisce ulteriori informazioni perché il tipo `T` non è supportato.

Nota: le funzionalità supportate si evolveranno con le nuove versioni dello strumento.

<a name="EM1021" />

### <a name="em1021-constructor-c-has-default-values-for-which-no-wrapper-is-generated"></a>EM1021: il costruttore `C` dispone di valori predefiniti per i quali non viene generato alcun wrapper.

Si tratta di un **avviso** che indica che i parametri predefiniti del costruttore `C` non generano codice aggiuntivo. La ragione più comune è che un metodo esistente ha già la stessa firma. Ad esempio, in .NET è possibile avere:

```csharp
public class MyType {
    public MyType () { ... }
    public MyType (int i = 0) { ... }
}
```

In questi casi verranno creati solo due selettori `init` generati, che eseguono entrambe le chiamate in mono, ma non esiste alcun wrapper per il successivo.

<a name="EM1030" />

### <a name="em1030-method-m-is-not-generated-because-return-type-t-is-not-supported"></a>EM1030: il metodo `M` non viene generato perché il tipo restituito `T` non è supportato.

Si tratta di un **avviso** che indica che il metodo `M` verrà ignorato, ovvero non verrà generato alcun elemento, perché il tipo restituito `T` non è supportato.

Verrà visualizzato un avviso precedente che fornisce ulteriori informazioni perché il tipo `T` non è supportato.

Nota: le funzionalità supportate si evolveranno con le nuove versioni dello strumento.

<a name="EM1031" />

### <a name="em1031-method-m-is-not-generated-because-of-parameter-type-t-is-not-supported"></a>EM1031: il metodo `M` non viene generato perché il tipo di parametro `T` non è supportato.

Si tratta di un **avviso** che indica che il metodo `M` verrà ignorato, ovvero non verrà generato alcun valore perché un parametro di tipo `T` non è supportato.

Verrà visualizzato un avviso precedente che fornisce ulteriori informazioni perché il tipo `T` non è supportato.

Nota: le funzionalità supportate si evolveranno con le nuove versioni dello strumento.

<a name="EM1032" />

### <a name="em1032-method-m-has-default-values-for-which-no-wrapper-is-generated"></a>EM1032: il metodo `M` dispone di valori predefiniti per i quali non viene generato alcun wrapper.

Si tratta di un **avviso** che indica che i parametri predefiniti del metodo `M` non generano codice aggiuntivo. La ragione più comune è che un metodo esistente ha già la stessa firma. Ad esempio, in .NET è possibile avere:

```csharp
public class MyType {
    public int Increment () { ... }
    public int Increment (int i = 0) { ... }
}
```

In questi casi verranno creati solo due selettori `increment` generati, che eseguono entrambe le chiamate in mono, ma non esiste alcun wrapper per il successivo.

<a name="EM1033" />

### <a name="em1033-method-m-is-not-generated-because-another-method-exposes-the-operator-with-a-friendly-name"></a>EM1033: il metodo `M` non viene generato perché un altro metodo espone l'operatore con un nome descrittivo.

Si tratta di un **avviso** che indica che il metodo `M` non viene generato perché un altro metodo espone l'operatore con un nome descrittivo. (https://msdn.microsoft.com/library/ms229032(v=vs.110).aspx)

<a name="EM1034" />

### <a name="em1034-extension-method-m-is-not-generated-inside-a-category-because-they-cannot-be-created-on-primitive-type-t-a-normal-static-method-was-generated"></a>EM1034: il metodo di estensione `M` non viene generato all'interno di una categoria perché non è possibile crearlo in un tipo primitivo `T`. È stato generato un metodo statico normale.

Si tratta di un **avviso** che indica che è stato trovato un metodo di estensione su un tipo primivite, ad esempio `System.Int32`. In Objective-C non è possibile creare categorie per il tipo primitivo. Il generatore produrrà invece un metodo statico normale.

<a name="EM1040" />

### <a name="em1040-property-p-is-not-generated-because-of-parameter-type-t-is-not-supported"></a>EM1040: la proprietà `P` non viene generata perché il tipo di parametro `T` non è supportato.

Si tratta di un **avviso** che indica che la proprietà `P` verrà ignorata, ovvero che non verrà generato nulla, perché il tipo esposto `T` non è supportato.

Verrà visualizzato un avviso precedente che fornisce ulteriori informazioni perché il tipo `T` non è supportato.

Nota: le funzionalità supportate si evolveranno con le nuove versioni dello strumento.

<a name="EM1041" />

### <a name="em1041-indexed-properties-on-t-is-not-generated-because-multiple-indexed-properties-are-not-supported"></a>EM1041: le proprietà indicizzate su `T` non vengono generate perché non sono supportate più proprietà indicizzate.

Si tratta di un **avviso** che indica che le proprietà indicizzate in `T` verranno ignorate, ovvero non verrà generato alcun dato, perché non sono supportate più proprietà indicizzate.

<a name="EM1050" />

### <a name="em1050-field-f-is-not-generated-because-of-field-type-t-is-not-supported"></a>EM1050: il campo `F` non viene generato perché il tipo di campo `T` non è supportato.

Si tratta di un **avviso** che indica che il campo `F` verrà ignorato, ovvero non verrà generato nulla, perché il tipo esposto `T` non è supportato.

Verrà visualizzato un avviso precedente che fornisce ulteriori informazioni perché il tipo `T` non è supportato.

Nota: le funzionalità supportate si evolveranno con le nuove versioni dello strumento.

<a name="EM1051" />

### <a name="em1051-element-e-is-generated-instead-as-f-because-its-name-conflicts-with-an-important-objective-c-selector"></a>EM1051: l'elemento `E` viene generato invece come `F` perché il nome è in conflitto con un selettore Objective-c importante.

Si tratta di un **avviso** che indica che l'elemento `E` verrà generato invece come `F` perché il nome è in conflitto con un selettore Objective-c importante.

I selettori in [NSObjectProtocol](https://developer.apple.com/reference/objectivec/1418956-nsobject?language=objc) hanno un significato importante in Objective-c e devono essere sottoposti a override con cautela.

Nota: l'elenco dei selettori riservati verrà sviluppato con le nuove versioni dello strumento.

<a name="EM1052" />

### <a name="em1052-element-e-is-not-generated-its-name-conflicts-with-other-elements-on-the-same-class"></a>EM1052: il nome dell'elemento `E` non è in conflitto con altri elementi nella stessa classe.

Si tratta di un **avviso** che indica che l'elemento `E` non viene generato perché il nome è in conflitto con altri elementi nella stessa classe.

<a name="EM1053" />

### <a name="em1053-target-e-is-not-supported-for-xamarinios-and-xamarinmac-only-the-framework-option-is-considered-supported-and-should-be-used"></a>EM1053: la `E` di destinazione non è supportata per Novell. iOS e Novell. Mac. Solo l'opzione `framework` è considerata supportata e deve essere usata.

Si tratta di un **avviso** che indica che la destinazione `E` è considerata non supportata per i casi d'uso Novell. iOS e Novell. Mac. 

Il consumo di librerie di incorporamento .NET statiche o dinamiche può richiedere passaggi aggiuntivi o modifiche di lavoro e deve essere evitato nella maggior parte dei casi d'uso.

Provare a rimuovere il parametro `--target` o passare `--target=framework`.

<!-- 2xxx: code generation -->

## <a name="em2xxx-code-generation"></a>EM2xxx: generazione del codice

<!-- 3xxx: reserved -->
<!-- 4xxx: reserved -->
<!-- 5xxx: reserved -->
<!-- 6xxx: reserved -->
<!-- 7xxx: reserved -->
<!-- 8xxx: reserved -->
<!-- 9xxx: reserved -->
