---
title: Generazione del codice XIB in Novell. iOS
description: Questo documento descrive il modo in cui Novell. iOS genera il codice per eseguire C#il mapping dei file XIB a, rendendo accessibili i controlli visivi a livello di programmazione.
ms.prod: xamarin
ms.assetid: 365991A8-E07A-0420-D28E-BC4D32065E1A
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/21/2017
ms.openlocfilehash: 367f0b6a44482762f8609eb959e154c942ff14f5
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70291826"
---
# <a name="xib-code-generation-in-xamarinios"></a>Generazione del codice XIB in Novell. iOS

> [!IMPORTANT]
> Questo documento illustra l'integrazione di Visual Studio per Mac solo con Interface Builder di Xcode, in quanto le azioni e gli Outlet non vengono usati nella Xamarin Designer per iOS. Per altre informazioni su iOS designer, vedere il documento di [iOS designer](~/ios/user-interface/designer/index.md) .

Lo strumento di Interface Builder Apple ("IB") può essere usato per progettare visivamente interfacce utente. Le definizioni di interfaccia create da IB vengono salvate nei file con **estensione XIB** . Ai widget e ad altri oggetti nei file con **estensione XIB** può essere assegnata una "identità della classe", che può essere un tipo personalizzato definito dall'utente. In questo modo è possibile personalizzare il comportamento dei widget e scrivere widget personalizzati.

Queste classi utente sono in genere sottoclassi delle classi del controller dell'interfaccia utente. Hanno *Outlet* (analoghe alle proprietà) e *azioni* (analoghe agli eventi) che possono essere connesse a oggetti interfaccia. In fase di esecuzione, quando viene caricato il file IB, vengono creati gli oggetti e gli Outlet e le azioni sono connessi in modo dinamico ai vari oggetti dell'interfaccia utente. Quando si definiscono queste classi gestite, è necessario definire tutte le azioni e gli outlet in modo che corrispondano a quelli previsti da IB. Per semplificare questa operazione, Visual Studio per Mac usa un modello simile a codebehind. Questo comportamento è simile a quello di Xcode per Objective-C, ma le convenzioni e il modello di generazione del codice sono stati modificati per essere più esperti degli sviluppatori .NET.

L'uso di file con **estensione XIB** non è attualmente supportato in Novell. iOS per Visual Studio.

## <a name="xib-files-and-custom-classes"></a>File con estensione XIB e classi personalizzate

Oltre a usare i tipi esistenti da Cocoa Touch, è possibile definire tipi personalizzati nei file con **estensione XIB** . È anche possibile usare i tipi definiti in altri file con **estensione XIB** o definiti esclusivamente nel C# codice. Attualmente, Interface Builder non è in grado di riconoscere i dettagli dei tipi definiti all'esterno del file con **estensione XIB** corrente, quindi non li elenca né visualizzano le azioni e gli outlet personalizzati. La rimozione di questa limitazione è prevista per qualche tempo in futuro.

È possibile definire classi personalizzate in un file con **estensione XIB** usando il comando "Aggiungi sottoclasse" nella scheda "classi" della Interface Builder. Si fa riferimento a queste classi come classi "codebehind". Se il file con **estensione XIB** include un file di controparte ". xib.designer.cs" nel progetto, Visual Studio per Mac compilerà automaticamente le definizioni delle classi parziali per tutte le classi personalizzate in **. xib**. Si fa riferimento a queste classi parziali come "classi della finestra di progettazione".

## <a name="generating-code"></a>Generazione di codice

Per qualsiasi  **{0}file con estensione XIB** con un'azione di compilazione della *pagina*, se nel progetto esiste anche un  **{0}file con estensione XIB.designer.cs** , Visual Studio per Mac genererà le classi parziali nel file di progettazione per tutte le classi utente è possibile trovare nel file con **estensione XIB** , con le proprietà per gli Outlet e i metodi parziali per tutte le azioni. La generazione del codice è abilitata semplicemente dalla presenza del file.

Il file di progettazione viene aggiornato automaticamente quando il file con **estensione XIB** cambia e Visual Studio per Mac riacquisisce lo stato attivo. Il file di progettazione non deve essere modificato manualmente, in quanto le modifiche verranno sovrascritte la volta successiva Visual Studio per Mac aggiornerà il file.

## <a name="registration-and-namespaces"></a>Registrazione e spazi dei nomi

Visual Studio per Mac genera le classi della finestra di progettazione utilizzando lo spazio dei nomi predefinito del progetto per il percorso del file della finestra di progettazione, in modo che sia coerente con il normale progetto .NET namespacing. Lo spazio dei nomi dei file di progettazione è determinato dallo spazio dei nomi predefinito del progetto e dalle impostazioni relative ai criteri di denominazione .NET. Si tenga presente che se lo spazio dei nomi predefinito del progetto viene modificato, MD rigenererà le classi nel nuovo spazio dei nomi, in modo che le classi parziali non corrispondano più.

Per rendere individuabile la classe dal runtime di Objective-C, Visual Studio per Mac applica un `[Register (name)]` attributo alla classe. Sebbene Novell. iOS registri `NSObject`automaticamente le classi derivate da, USA i nomi .NET completi. L'attributo applicato da Visual Studio per Mac esegue l'override di questo per assicurarsi che ogni classe venga registrata con il nome usato nel file **. xib** . Se si usano classi personalizzate in IB senza usare Visual Studio per Mac per generare file di progettazione, potrebbe essere necessario applicare questa operazione manualmente per fare in modo che le classi gestite corrispondano ai nomi previsti della classe Objective-C.

Le classi non possono essere definite in più di un **. xib**o saranno in conflitto.

## <a name="non-designer-class-parts"></a>Parti della classe non della finestra di progettazione

Le classi parziali della finestra di progettazione non sono progettate per essere utilizzate così come sono. Gli Outlet sono privati e non viene specificata alcuna classe base. Si prevede che ogni classe avrà una parte della classe "non di progettazione" corrispondente in un altro file, che imposta la classe di base, USA o espone gli Outlet e definisce i costruttori necessari per creare un'istanza della classe dal codice nativo durante il caricamento del file **. xib** . I modelli default **. xib** eseguono questa operazione, ma per eventuali classi personalizzate aggiuntive definite in un **XIB**è necessario aggiungere manualmente la parte non di progettazione.

Il motivo è la necessità di flessibilità. Ad esempio, più classi codebehind potrebbero sottoclassare una classe astratta gestita comune, che sottoclassa la classe per essere sottoclassata da IB.

È normale inserirli in un  **{0}file con estensione XIB.cs** accanto al  **{0}file con estensione XIB.designer.cs** designer.

<a name="generated" />

## <a name="generated-actions-and-outlets"></a>Azioni e Outlet generati

Nelle classi della finestra di progettazione parziale, Visual Studio per Mac genera proprietà corrispondenti a tutti gli outlet connessi definiti in IB e metodi parziali corrispondenti a qualsiasi azione connessa.

### <a name="outlet-properties"></a>Proprietà Outlet

Le classi della finestra di progettazione contengono proprietà corrispondenti a tutti gli outlet definiti nella classe personalizzata. Il fatto che si tratta di proprietà è un dettaglio di implementazione di Novell. iOS al Bridge Objective C per abilitare l'associazione lazy. È necessario considerarle equivalenti ai campi privati, destinate a essere usate solo dalla classe codebehind. Se si desidera renderli pubblici, aggiungere le proprietà della funzione di accesso alla parte della classe non della finestra di progettazione, come per qualsiasi altro campo privato.

Se le proprietà di uscita sono definite in modo da `id` avere un tipo `NSObject`equivalente a, il generatore di codice della finestra di progettazione determina attualmente il tipo più sicuro in base agli oggetti connessi a tale Outlet, per praticità.
Tuttavia, questo potrebbe non essere supportato nelle versioni future, quindi è consigliabile tipizzare in modo esplicito gli outlet quando si definisce la classe personalizzata.

### <a name="action-properties"></a>Proprietà azione

Le classi della finestra di progettazione contengono metodi parziali corrispondenti a tutte le azioni definite nella classe personalizzata. Si tratta di metodi senza un'implementazione. Lo scopo dei metodi parziali è duplice:

1. Se si digita `partial` il corpo della classe della parte della classe non della finestra di progettazione, Visual Studio per Mac offrirà il completamento automatico delle firme di tutti i metodi parziali non implementati.
2. Alle firme dei metodi parziali è applicato un attributo che li espone all'ambiente Objective-C, in modo che possano essere gestiti come azione corrispondente.


Se si desidera, è possibile ignorare il metodo parziale e implementare l'azione applicando l'attributo a un metodo diverso oppure lasciarlo passare a una classe di base.

Se le azioni sono definite per avere un tipo di `id` mittente (equivalente `NSObject`a), il generatore di codice della finestra di progettazione determina attualmente il tipo più sicuro in base agli oggetti connessi a tale azione. Tuttavia, questo potrebbe non essere supportato nelle versioni future, quindi è consigliabile specificare in modo esplicito le azioni quando si definisce la classe personalizzata.

Si noti che questi metodi parziali vengono creati C#solo per, perché CodeDom non supporta i metodi parziali, pertanto non vengono generati per altri linguaggi.

## <a name="cross-xib-class-usage"></a>Utilizzo della classe tra XIB

In alcuni casi, gli utenti desiderano fare riferimento alla stessa classe da più file con **estensione XIB** , ad esempio con i controller di tabulazione. A tale scopo, esplicitamente fa riferimento alla definizione di classe da un altro file con **estensione XIB** o definendo nuovamente lo stesso nome di classe nel secondo **. xib**.

Il secondo caso può essere problematico a causa della Visual Studio per Mac l'elaborazione di file **XIB** singolarmente. Non è in grado di rilevare e unire automaticamente le definizioni duplicate, pertanto è possibile che si verifichino conflitti nell'applicare più volte l'attributo Register quando la stessa classe parziale è definita in più file di progettazione. Le versioni recenti di Visual Studio per Mac tentano di risolvere il problema, ma potrebbe non funzionare sempre come previsto. In futuro, è probabile che non sia più supportato, ma Visual Studio per Mac renderà tutti i tipi definiti in tutti i file **. xib** e il codice gestito nel progetto direttamente visibile da tutti i file **. xib** .

## <a name="type-resolution"></a>Risoluzione dei tipi

I tipi usati in IB sono nomi di tipi Objective-C. Viene eseguito il mapping ai tipi CLR tramite l'utilizzo di attributi Register. Quando si genera il codice di uscita e azione, Visual Studio per Mac risolverà i tipi CLR corrispondenti per tutti i tipi Objective-C inclusi nel core Novell. iOS e completerà i nomi dei tipi.

Tuttavia, il generatore di codice non può attualmente risolvere i tipi CLR da nomi di tipi Objective-C nel codice utente o nelle librerie, quindi, in questi casi, restituisce il nome del tipo Verbatim. Questo significa che il tipo CLR corrispondente deve avere lo stesso nome del tipo Objective-C e trovarsi nello stesso spazio dei nomi del codice che lo usa. Questo è pianificato per essere risolto a breve in futuro considerando tutti i tipi Objective-C nel progetto durante la generazione del codice.
