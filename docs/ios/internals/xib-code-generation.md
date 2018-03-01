---
title: Generazione di codice .xib
ms.topic: article
ms.prod: xamarin
ms.assetid: 365991A8-E07A-0420-D28E-BC4D32065E1A
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: c98d4100a758e624c851ed2294cfe0c6b7f16fdd
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="xib-code-generation"></a>Generazione di codice .xib

> [!IMPORTANT]
>  Questo documento viene illustrato Visual Studio per l'integrazione del Mac con generatore del Xcode di interfaccia a solo come azioni e prese non vengono utilizzate nella finestra di progettazione di Xamarin per iOS. Per ulteriori informazioni sulla finestra di progettazione iOS, consultare il [iOS progettazione](~/ios/user-interface/designer/index.md) documento.

Lo strumento generatore di interfaccia di Apple ("b") può essere utilizzato per progettare visivamente interfacce utente. Le definizioni di interfaccia create da IB vengono salvate in **.xib** file. Widget e altri oggetti nel **.xib** file possono essere assegnati a una "classe identity", che può essere un tipo personalizzato definito dall'utente. Ciò consente personalizzare il comportamento di widget e per scrivere widget personalizzati.

Queste classi utente sono in genere sottoclassi delle classi controller dell'interfaccia utente. Questi ultimi includono *prese* (analoghe alle proprietà) e *azioni* (analoghi agli eventi) che può essere connesso a oggetti dell'interfaccia. In fase di esecuzione, quando viene caricato il file IB, vengono creati gli oggetti e i punti vendita e le azioni sono connessi in modo dinamico ai vari oggetti dell'interfaccia utente. Quando si definiscono le classi gestite, è necessario definire tutte le azioni e le prese a corrispondano a quelli che IB prevista. Visual Studio per Mac utilizza un modello simile CodeBehind per semplificare l'operazione. Questa operazione è simile a ciò che esegue Xcode per Objective-C, ma il modello di generazione del codice e le convenzioni sono stata modificate per essere più note agli sviluppatori di .NET.

Utilizzo di **.xib** file non è attualmente supportato in xamarin per Visual Studio.

## <a name="xib-files-and-custom-classes"></a>.xib file e le classi personalizzate

Nonché utilizza tipi esistenti da Cocoa tocco, è possibile definire tipi personalizzati in **.xib** file. È inoltre possibile utilizzare i tipi definiti in altre **.xib** file oppure definito esclusivamente nel codice c#. Attualmente, il generatore di interfaccia non è compatibile con i dettagli dei tipi definiti all'esterno di corrente **.xib** file, in modo non elencarli o Mostra le prese personalizzate. Rimozione di questa limitazione è pianificata in futuro.

Le classi personalizzate possono essere definite in un **.xib** file utilizzando il comando "Aggiungere sottoclasse" nella scheda "Classi" del generatore di interfaccia. Viene fatto riferimento a queste classi "CodeBehind". Se il **.xib** file ha un ". xib.designer.cs" file controparte nel progetto, quindi Visual Studio per Mac verrà automaticamente riempirla con definizioni di classi parziali per tutte le classi personalizzate presenti il **.xib**. Viene fatto riferimento a queste classi parziali come "classi della finestra di progettazione".

## <a name="generating-code"></a>Generazione di codice

Per qualsiasi **{0} .xib** file con un'azione di compilazione *pagina*, se un **{0}.xib.designer.cs** file è presente anche nel progetto, Visual Studio per Mac genererà le classi parziali nel per tutte le classi utente è possibile trovare nel file di progettazione di **.xib** file, con le proprietà per i punti vendita e i metodi parziali per tutte le azioni. Semplicemente per la presenza di questo file è abilitata la generazione di codice.

Il file viene automaticamente aggiornato quando il **.xib** file le modifiche e Visual Studio per Mac riassume lo stato attivo. Il file della finestra di progettazione non deve essere modificato manualmente, poiché le modifiche saranno sovrascritta successivo Visual Studio per gli aggiornamenti di Mac il file.

## <a name="registration-and-namespaces"></a>Registrazione e spazi dei nomi

Visual Studio per Mac genera le classi della finestra di progettazione utilizza spazio dei nomi predefinito del progetto per il percorso del file della finestra di progettazione, per renderlo coerente con namespacing di progetto .NET normale. Lo spazio dei nomi di file della finestra di progettazione viene gestita dal "spazio dei nomi predefinito" del progetto e le relative impostazioni "Criteri di denominazione .NET". Tenere presente che se lo spazio dei nomi predefinito del progetto viene modificato, MD verrà rigenerata le classi nello spazio dei nomi nuova, pertanto è possibile che le classi parziali non corrispondono più ai.

Per rendere visibile la classe dal runtime Objective-C, Visual Studio per Mac si applica un `[Register (name)]` attributo alla classe. Sebbene xamarin registra automaticamente `NSObject`-classi derivate, vengono utilizzati i nomi di .NET completo. L'attributo applicato da Visual Studio per Mac esegue l'override di questa opzione per verificare che ogni classe è registrato con il nome utilizzato nella **.xib** file. Se si utilizzano le classi personalizzate in IB senza utilizzare Visual Studio per Mac per generare i file della finestra di progettazione, è necessario applicare manualmente per rendere le classi gestite corrispondano ai nomi di classe Objective-C previsto.

Le classi non possono essere definite in più di un **.xib**, o sono in conflitto.

## <a name="non-designer-class-parts"></a>Parti di classe non è una finestra di progettazione

Progettazione classi parziali non deve essere utilizzato come-è. Punti vendita è private e viene specificata alcuna classe base. È previsto che ogni classe avrà una parte di classe "non-finestra di progettazione" corrispondente in un altro file, che imposta la classe di base, viene utilizzato o espone le prese e definisce i costruttori che sono necessarie per un'istanza della classe dal codice nativo durante il caricamento di **.xib**. Il valore predefinito **.xib** modelli eseguire questa operazione, ma per tutte le classi personalizzate aggiuntive definite un **.xib**, è necessario aggiungere manualmente la parte non è una finestra di progettazione.

Il motivo è la necessità di flessibilità. Ad esempio, più classi CodeBehind Impossibile comune gestiti astratta, che crea una sottoclasse della classe da sottoclassare dall'IB sottoclasse.

È convenzionale per inserire gli elementi un **{0}.xib.cs** file accanto il **{0}.xib.designer.cs** file di progettazione.

<a name="generated" />

## <a name="generated-actions-and-outlets"></a>Punti vendita e le azioni generate

Nella finestra di progettazione classi parziali, Visual Studio per Mac genera proprietà corrispondente a qualsiasi prese connesse definite IB e metodi parziali corrispondente a qualsiasi azione connessi.

### <a name="outlet-properties"></a>Proprietà di uscita

Classi della finestra di progettazione che contengono le proprietà corrispondenti a tutte le altre definite nella classe personalizzata. Il fatto che si tratta di proprietà è un dettaglio di implementazione di xamarin. IOS Bridge Objective C, per consentire l'associazione differita. È consigliabile che siano equivalenti campi privati, deve essere utilizzata solo dalla classe CodeBehind. Se si desidera renderli pubblici, aggiungere le proprietà della funzione di accesso per la parte di classe non è una finestra di progettazione come si farebbe per qualsiasi altro campo privato.

Se per un tipo di sono definite proprietà presa **id** (equivalente a `NSObject`), quindi il generatore di codice della finestra di progettazione attualmente determina il tipo di possibili più attendibili in base a oggetti collegati a tale punto, per motivi di praticità.
Tuttavia, questo potrebbe non essere supportato nelle versioni future, pertanto si consiglia di tipo in modo esplicito fortemente prese durante la definizione di classe personalizzata.

### <a name="action-properties"></a>Proprietà azione

Progettazione classi contengono metodi parziali corrispondenti a tutte le azioni definite nella classe personalizzata. Questi sono metodi senza alcuna implementazione. Lo scopo dei metodi parziali è duplice:

1.  Se si digita `partial` nel corpo della classe della parte di classe non è una finestra di progettazione, Visual Studio per Mac sarà disponibile per il completamento automatico le firme di tutti i metodi parziali non implementato.
1.  Le firme del metodo parziale hanno un attributo applicato che espone al mondo Objective-C, in modo possono ottenere gestiti per l'azione corrispondente.


Se si desidera, si può ignorare il metodo parziale e implementare l'azione applicando l'attributo a un metodo diverso o si lascia passare a una classe di base.

Se le azioni definite per un tipo di mittente di `id` (equivalente a `NSObject`), quindi il generatore di codice della finestra di progettazione attualmente determina il tipo di possibili più attendibili in base a oggetti collegati a tale azione. Tuttavia, questo potrebbe non essere supportato nelle versioni future, pertanto si consiglia di tipo in modo esplicito fortemente le azioni quando si definisce la classe personalizzata.

Si noti che questi metodi parziali vengono creati solo per c#, perché CodeDOM non supporta i metodi parziali, in modo che non vengono generati per le altre lingue.

## <a name="cross-xib-class-usage"></a>Utilizzo della classe tra XI

In alcuni casi, gli utenti desiderano fare riferimento alla stessa classe da più **.xib** file, ad esempio con i controller di scheda. Questa operazione può essere eseguita da esplicitamente la definizione della classe di riferimento da un altro **.xib** file o definendo lo stesso nome di classe tra il secondo **.xib**.

Quest'ultimo caso può risultare problematico. Si tratta fino a Visual Studio per l'elaborazione Mac **.xib** file singolarmente. Impossibile e rileva automaticamente merge definizioni duplicate, pertanto si rischia di con conflitti applicando l'attributo di registro più volte quando nella stessa classe parziale è definita in più file di progettazione. Le versioni recenti di Visual Studio per Mac tentano di risolvere il problema, ma ciò potrebbe non funzionare come previsto. In futuro è probabile che diventi non supportato e invece Visual Studio per Mac renderà tutti i tipi definiti in tutte **.xib** file e codice gestito nel progetto direttamente visibile da tutti **.xib** file.

## <a name="type-resolution"></a>Risoluzione del tipo

Tipi usati nel IB sono i nomi dei tipi di Objective-C. Questi sono mappati a tipi CLR tramite l'utilizzo di attributi di registrazione. Durante la generazione di codice di uscita e l'azione, Visual Studio per Mac risolvere i tipi CLR corrispondenti per tutti i tipi di Objective-C racchiuso tra i principali di xamarin. IOS e i relativi nomi di tipo completi.

Il generatore di codice, tuttavia, non è possibile risolvere i tipi CLR dai nomi di tipo Objective-C nel codice utente o in librerie, attualmente pertanto in tali casi viene restituito il nome del tipo verbatim. Ciò significa che il tipo CLR corrispondente deve avere lo stesso nome del tipo Objective-C e stesso spazio dei nomi del codice che lo usa. Questo prevista con è il momento in futuro considerando tutti i tipi di Objective-C nel progetto durante la generazione di codice.
