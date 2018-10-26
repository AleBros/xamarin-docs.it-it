---
title: Generazione di codice xib in xamarin. IOS
description: Questo documento descrive come xamarin. IOS genera codice per eseguire il mapping di file con estensione xib per C#, rendendo accessibili controlli visivi a livello di codice.
ms.prod: xamarin
ms.assetid: 365991A8-E07A-0420-D28E-BC4D32065E1A
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 57988374b4383f5659e29edff3834958b8f99f1b
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50103920"
---
# <a name="xib-code-generation-in-xamarinios"></a>Generazione di codice xib in xamarin. IOS

> [!IMPORTANT]
>  Questo documento viene illustrato Visual Studio per l'integrazione del Mac con Interface Builder di Xcode, solo come Outlet e azioni non vengono usati nella finestra di progettazione di Xamarin per iOS. Per altre informazioni su iOS Designer, vedere la [iOS Designer](~/ios/user-interface/designer/index.md) documento.

Lo strumento Apple Interface Builder ("IB") è utilizzabile per progettare visivamente interfacce utente. Le definizioni di interfaccia create da IB vengono salvate nella **xib** file. Widget e altri oggetti nel **xib** file possono essere stati assegnati una "classe identità", che può essere un tipo personalizzato definito dall'utente. In questo modo è possibile personalizzare il comportamento di widget e scrivere widget personalizzati.

Queste classi utente sono in genere le sottoclassi delle classi controller dell'interfaccia utente. Hanno *Outlet* (analoghe alle proprietà) e *azioni* (analogo agli eventi) che possono essere connesse a oggetti dell'interfaccia. In fase di esecuzione, quando viene caricato il file IB, gli oggetti vengono creati e gli Outlet e le azioni sono connessi in modo dinamico per i vari oggetti dell'interfaccia utente. Quando si definiscono le classi gestite, è necessario definire tutte le azioni e gli Outlet in modo che corrispondano quelli che si aspetta IB. Visual Studio per Mac Usa un modello simile a CodeBehind per semplificare l'operazione. Ciò è simile al funzionamento di Xcode per Objective-C, ma il modello di generazione del codice e le convenzioni sono stata modificate per essere più familiare agli sviluppatori .NET.

Lavora **xib** file non è attualmente supportata in xamarin. IOS per Visual Studio.

## <a name="xib-files-and-custom-classes"></a>file con estensione xib e classi personalizzate

E l'uso di tipi esistenti da Cocoa Touch, è possibile definire tipi personalizzati nei **xib** file. È anche possibile usare i tipi definiti in altre **xib** file o definito esclusivamente in C# codice. Attualmente, non è compatibile con i dettagli dei tipi definiti all'esterno di corrente Interface Builder **xib** file, in modo che non verrà elencarli o mostrare loro personalizzato Outlet e azioni. Rimozione di questa limitazione è previsto per un intervallo di tempo in futuro.

Le classi personalizzate possono essere definite in un **xib** file usando il comando "Aggiungi sottoclasse" nella scheda "Classi" di Interface Builder. Si fa riferimento a queste classi "CodeBehind". Se il **xib** file ha un ". xib.designer.cs" file controparte nel progetto, quindi Visual Studio per Mac viene riempito automaticamente con le definizioni di classi parziali per tutte le classi personalizzate nel **xib**. Si fa riferimento a queste classi parziali come "classi di finestra di progettazione".

## <a name="generating-code"></a>Generazione di codice

Per qualsiasi  **{0}xib** file con un'azione di compilazione *pagina*, se un  **{0}. xib.designer.cs** file presente anche nel progetto, Visual Studio per Mac Genera classi parziali nel file di progettazione per tutte le classi utente rilevato all'interno di **xib** file, con le proprietà per i punti vendita e i metodi parziali per tutte le azioni. Generazione di codice è abilitata è sufficiente per la presenza di questo file.

Il file di progettazione viene automaticamente aggiornato quando le **xib** file le modifiche e Visual Studio per Mac riacquisirà lo stato attivo. Il file di progettazione non deve essere modificato manualmente, come le modifiche verranno sovrascritta prossimo Visual Studio per gli aggiornamenti di Mac il file.

## <a name="registration-and-namespaces"></a>Registrazione e gli spazi dei nomi

Visual Studio per Mac genera le classi della finestra di progettazione utilizzano lo spazio dei nomi predefinito del progetto per il percorso di file di progettazione, per assicurare la coerenza con namespacing di progetto .NET normale. Lo spazio dei nomi di file della finestra di progettazione è basato sulle "spazio dei nomi predefinito" del progetto e le relative impostazioni "Criteri di denominazione .NET". Tenere presente che se lo spazio dei nomi predefinito del progetto viene modificato, MD verrà rigenerare le classi nello spazio dei nomi nuovo, quindi si può verificare che le classi parziali non corrispondono più ai.

Per rendere la classe individuabili dal runtime di Objective-C, Visual Studio per Mac si applica un `[Register (name)]` attributo alla classe. Sebbene xamarin. IOS registra automaticamente `NSObject`-classi derivate, Usa i nomi di .NET completo. L'attributo applicato da Visual Studio per Mac esegue l'override di questa opzione per assicurarsi che ogni classe è registrato con il nome usato nel **xib** file. Se si usano le classi personalizzate in IB senza usare Visual Studio per Mac per generare i file della finestra di progettazione, è necessario applicare manualmente per rendere le classi gestite corrispondono ai nomi di classe Objective-C previsto.

Classi non possono essere definite in più **xib**, o causerebbero conflitti.

## <a name="non-designer-class-parts"></a>Parti di classe non è una finestra di progettazione

Progettazione classi parziali non deve essere utilizzato come-è. Outlet sono privati e viene specificata alcuna classe di base. È previsto che ogni classe avrà una parte di classe "non-finestra di progettazione" corrispondente in un altro file, che imposta la classe di base, utilizza o espone il Outlet e definisce i costruttori che sono necessari per creare un'istanza della classe dal codice nativo quando si caricano i **xib**. Il valore predefinito **xib** modelli di eseguire questa operazione, ma per tutte le classi personalizzate aggiuntive definite un **xib**, è necessario aggiungere manualmente la parte non è una finestra di progettazione.

Il motivo è l'esigenza di flessibilità. Ad esempio, più classi CodeBehind potrebbero sottoclasse che un comune gestiti classe astratta, che crea una sottoclasse della classe da sottoclassare dall'IB.

È convenzionale per questi elementi vengono inseriti una  **{0}. xib.cs** file accanto il  **{0}. xib.designer.cs** file di progettazione.

<a name="generated" />

## <a name="generated-actions-and-outlets"></a>Outlet e azioni generate

Nella finestra di progettazione classi parziali, Visual Studio per Mac genera proprietà che corrispondono a qualsiasi Outlet connesso definiti in IB e metodi parziali corrispondenti a eventuali azioni connesse.

### <a name="outlet-properties"></a>Proprietà Outlet

Finestra di progettazione classi contengono proprietà che corrispondono a tutti gli Outlet definiti nella classe personalizzata. Il fatto che queste sono di proprietà è un dettaglio di implementazione di xamarin. IOS a bridge Objective C, per consentire l'associazione differita. Si dovrebbero prenderli in considerazione sia equivalente ai campi privati, può essere utilizzato solo dalla classe CodeBehind. Se si vuole renderli pubblici, aggiungere le proprietà della funzione di accesso per la parte di classe non è una finestra di progettazione, come si farebbe per qualsiasi altro campo privato.

Se le proprietà outlet sono definite per un tipo di `id` (equivalente a `NSObject`), quindi il generatore di codice della finestra di progettazione attualmente determina il tipo di possibili più attendibili in base agli oggetti connessi a tale negozio, per motivi di praticità.
Tuttavia, ciò potrebbe non essere supportata nelle versioni future, pertanto è consigliabile che in modo esplicito tipizzare le prese quando si definisce la classe personalizzata.

### <a name="action-properties"></a>Proprietà delle azioni

Finestra di progettazione classi contengono metodi parziali corrispondenti a tutte le azioni definite nella classe personalizzata. Questi sono metodi senza implementazione. Lo scopo dei metodi parziali è duplice:

1.  Se si digita `partial` nel corpo della classe della parte classe non è una finestra di progettazione, Visual Studio per Mac offrirà il completamento automatico le firme di tutti i metodi parziali non implementato.
2.  Le firme di metodo parziale hanno un attributo applicato che espone al mondo Objective-C, in modo che ottenere gestiti come l'azione corrispondente.


Se si desidera, si può ignorare il metodo parziale e implementare l'azione applicando l'attributo a un metodo diverso o lasciarla passare a una classe di base.

Se le azioni vengono definite per presentare un tipo di mittente del `id` (equivalente a `NSObject`), quindi il generatore di codice della finestra di progettazione attualmente determina il tipo di possibili più attendibili in base agli oggetti connessi a tale azione. Tuttavia, ciò potrebbe non essere supportata nelle versioni future, pertanto è consigliabile che si digita in modo esplicito fortemente le azioni quando si definisce la classe personalizzata.

Si noti che questi metodi parziali vengono creati solo per C#, in quanto CodeDOM non supporta i metodi parziali, in modo che non vengono generati per le altre lingue.

## <a name="cross-xib-class-usage"></a>Uso della classe cross-XIB

In alcuni casi, gli utenti desiderano fare riferimento alla classe stessa da più **xib** file, ad esempio con i controller di scheda. Questa operazione può essere eseguita da esplicitamente la definizione della classe di riferimento da un altro **xib** file o per definire lo stesso nome di classe nuovamente nel secondo **xib**.

Quest'ultimo caso può risultare problematico a causa di Visual Studio per l'elaborazione di Mac **xib** file singolarmente. Automaticamente non possa rilevare e unire definizioni duplicate, in modo che potrebbero avere conflitti applicando l'attributo di registro più volte quando la stessa classe parziale è definita in più file di progettazione. Le versioni recenti di Visual Studio per Mac tentano di risolvere il problema, ma potrebbe non funzionare come previsto. In futuro è probabile che più supportate e invece di Visual Studio per Mac renderà tutti i tipi definiti in tutte le **xib** i file e il codice gestito nel progetto direttamente visibile da tutti **xib** file.

## <a name="type-resolution"></a>Risoluzione del tipo

I tipi usati in IB sono i nomi dei tipi di Objective-C. Questi vengono mappati ai tipi CLR tramite l'uso di attributi di registrazione. Durante la generazione di codice outlet e azioni, Visual Studio per Mac risolvere i tipi CLR corrispondenti per tutti i tipi di Objective-C sottoposto a wrapping da di base di xamarin. IOS e i nomi di tipo completi.

Tuttavia, il generatore di codice non è attualmente risolvere tipi Common Language Runtime dai nomi di tipo Objective-C nel codice utente o le librerie, in modo che in questi casi restituisce il nome del tipo verbatim. Ciò significa che il tipo CLR corrispondente deve avere lo stesso nome del tipo di Objective-C e spazio dei nomi stesso come il codice che lo usa. Questa operazione è pianificata per la risoluzione corrisponde a uno in futuro considerando tutti i tipi di Objective-C nel progetto durante la generazione del codice.
