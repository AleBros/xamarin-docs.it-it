---
title: Usare i progetti condivisi per condividere il codice
description: I progetti condivisi consentono di scrivere codice comune a cui fa riferimento una serie di progetti di applicazioni diversi. Il codice viene compilato come parte di ogni progetto di riferimento e può includere direttive del compilatore per incorporare funzionalità specifiche della piattaforma nella codebase condivisa.
ms.prod: xamarin
ms.assetid: 191c71fb-44a4-4e6c-af4b-7b1107dce6af
author: davidortinau
ms.author: daortin
ms.date: 07/18/2018
ms.openlocfilehash: eee76c056d05edccd1e039bc5e4cb8107d1aceb5
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016689"
---
# <a name="shared-projects-code-sharing"></a>Condivisione del codice di progetti condivisi

_I progetti condivisi consentono di scrivere codice comune a cui fa riferimento una serie di progetti di applicazioni diversi. Il codice viene compilato come parte di ogni progetto di riferimento e può includere direttive del compilatore che consentono di incorporare funzionalità specifiche della piattaforma nella codebase condivisa._

I progetti condivisi, detti anche progetti di asset condivisi, consentono di scrivere codice condiviso tra più progetti di destinazione, incluse le applicazioni Novell.

Supportano le direttive del compilatore in modo che sia possibile includere in modo condizionale il codice specifico della piattaforma da compilare in un subset dei progetti che fanno riferimento al progetto condiviso. È inoltre disponibile il supporto dell'IDE per semplificare la gestione delle direttive del compilatore e visualizzare il modo in cui il codice verrà visualizzato in ogni applicazione.

Se è stato usato il collegamento di file in passato per condividere il codice tra i progetti, i progetti condivisi funzionano in modo simile, ma con un supporto IDE molto migliorato.

## <a name="what-is-a-shared-project"></a>Che cos'è un progetto condiviso?

A differenza della maggior parte degli altri tipi di progetto, un progetto condiviso non ha alcun output (in formato DLL), ma il codice viene compilato in ogni progetto che vi fa riferimento. Come illustrato nel diagramma seguente, concettualmente l'intero contenuto del progetto condiviso viene copiato in ogni progetto di riferimento e compilato come se facesse parte di essi.

![](shared-projects-images/sharedassetproject.png "Shared Project architecture")

Il codice in un progetto condiviso può contenere direttive del compilatore che consentono di abilitare o disabilitare le sezioni di codice a seconda del progetto di applicazione che usa il codice, suggerito dalle caselle colorate della piattaforma nel diagramma.

Un progetto condiviso non viene compilato autonomamente, ma esiste esclusivamente come raggruppamento di file di codice sorgente che possono essere inclusi in altri progetti. Quando si fa riferimento a un altro progetto, il codice viene compilato in modo efficace come *parte* del progetto. I progetti condivisi non possono fare riferimento a qualsiasi altro tipo di progetto, inclusi altri progetti condivisi.

Si noti che i progetti di applicazioni Android non possono fare riferimento ad altri progetti di applicazioni Android. ad esempio, un progetto Android unit test non può fare riferimento a un progetto di applicazione Android. Per ulteriori informazioni su questa limitazione, vedere questa [discussione sul forum](https://forums.xamarin.com/discussion/comment/98092/).

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

## <a name="visual-studio-for-mac-walkthrough"></a>Procedura dettagliata per Visual Studio per Mac

Questa sezione illustra come creare e usare un progetto condiviso usando Visual Studio per Mac. Per un esempio completo, vedere la sezione [esempio di progetto condiviso](#Shared_Project_Example) .

## <a name="creating-a-shared-project"></a>Creazione di un progetto condiviso

Per creare un nuovo progetto condiviso, passare a **File > nuova soluzione** oppure fare clic con il pulsante destro del mouse su una soluzione esistente e scegliere **Aggiungi > Aggiungi nuovo progetto...** ):

[![Nuovo progetto condiviso](shared-projects-images/xs-newsolution-sml.png "Nuova soluzione")](shared-projects-images/xs-newsolution.png#lightbox)

Nella schermata successiva scegliere il nome del progetto e fare clic su **Crea**.

Di seguito è riportato un nuovo progetto condiviso. si noti che non sono presenti riferimenti o nodi componente; questi non sono supportati per i progetti condivisi.

![Progetto condiviso vuoto](shared-projects-images/xs-empty.png "Progetto condiviso vuoto")

Affinché un progetto condiviso possa essere utile, è necessario fare riferimento a esso da almeno un progetto di compilazione (ad esempio, un'applicazione o una libreria iOS o Android o un progetto PCL). Un progetto condiviso non viene compilato quando non vi è alcun riferimento, quindi gli errori di sintassi (o qualsiasi altro) non verranno evidenziati fino a quando non vi viene fatto riferimento da altri elementi.

L'aggiunta di un riferimento a un progetto condiviso viene eseguita nello stesso modo in cui si fa riferimento a un normale progetto di libreria. Questa schermata mostra un progetto Xamarin.iOS che fa riferimento a un progetto condiviso.

![](shared-projects-images/xs-reference.png "Project reference to Shared Project")

Una volta che al progetto condiviso viene fatto riferimento da un'altra libreria o da un'altra applicazione, è possibile compilare la soluzione e visualizzare eventuali errori nel codice. Quando si fa riferimento al progetto condiviso da _due o più_ progetti, viene visualizzato un menu nella parte superiore sinistra dell'editor del codice sorgente che Mostra come scegliere i progetti che fanno riferimento a questo file.

## <a name="shared-project-options"></a>Opzioni progetto condiviso

Quando si fa clic con il pulsante destro del mouse su un progetto condiviso e si scelgono le **Opzioni** , sono presenti meno impostazioni di altri tipi di progetto Poiché i progetti condivisi non vengono compilati (autonomamente), non è possibile impostare opzioni di output o del compilatore, configurazioni di progetto, firma di assembly o comandi personalizzati. Il codice in un progetto condiviso eredita in modo efficace questi valori da qualsiasi riferimento.

La schermata **Opzioni** è mostrata di seguito: il **nome** del progetto e lo **spazio dei nomi predefinito** sono le uniche due impostazioni che in genere verranno modificate.

![](shared-projects-images/xs-sharedprojectoptions.png "Shared Project Options")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="visual-studio-walkthrough"></a>Procedura dettagliata in Visual Studio

Questa sezione illustra come creare e usare un progetto condiviso con Visual Studio. Per un'implementazione completa, vedere la sezione [esempio di progetto condiviso](#Shared_Project_Example) .

### <a name="creating-a-shared-project"></a>Creazione di un progetto condiviso

Per creare un nuovo progetto condiviso, passare a **File**  > **nuovo**  > **progetto**.

In Visual Studio 2019 immettere **Shared** nella casella di ricerca della pagina **creare un nuovo progetto** . Selezionare il modello di **progetto condiviso** e quindi fare clic su **Avanti**. Immettere un nome per il progetto e quindi selezionare **Crea**.

In Visual Studio 2017 selezionare il modello di **progetto condiviso** , quindi scegliere un nome per il progetto.

![Modello di progetto condiviso in Visual Studio 2017](shared-projects-images/vs-newsolution.png)

È anche possibile aggiungere un nuovo progetto condiviso a una soluzione esistente facendo clic con il pulsante destro del mouse sul file della soluzione e scegliendo **aggiungi > nuovo progetto**. Un nuovo progetto condiviso appare come illustrato di seguito (dopo l'aggiunta di un file di classe). Si noti che non sono presenti riferimenti o nodi componente; questi non sono supportati per i progetti condivisi.

![](shared-projects-images/vs-empty.png "Empty Shared Project")

Affinché un progetto condiviso possa essere utile, è necessario fare riferimento a esso da almeno un progetto di compilazione (ad esempio, un'applicazione o una libreria iOS o Android o un progetto PCL). Un progetto condiviso non viene compilato quando non vi è alcun riferimento, quindi gli errori di sintassi (o qualsiasi altro) non verranno evidenziati fino a quando non vi viene fatto riferimento da altri elementi.

L'aggiunta di un riferimento a un progetto condiviso viene eseguita nello stesso modo in cui si fa riferimento a un normale progetto di libreria. Questa schermata mostra un progetto Xamarin.iOS che fa riferimento a un progetto condiviso.

![](shared-projects-images/vs-reference.png "Project reference to Shared Project")

Una volta che al progetto condiviso viene fatto riferimento da un'altra libreria o da un'altra applicazione, è possibile compilare la soluzione e visualizzare eventuali errori nel codice. Quando si fa riferimento al progetto condiviso da _due o più_ progetti, viene visualizzato un menu nella parte superiore sinistra dell'editor del codice sorgente per vedere quali progetti fanno riferimento al file di codice corrente.

### <a name="shared-project-properties"></a>Proprietà progetto condiviso

Quando si seleziona un progetto condiviso, il pannello Proprietà presenta un minor numero di impostazioni rispetto ad altri tipi di progetto. Poiché i progetti condivisi non vengono compilati (autonomamente), non è possibile impostare opzioni di output o del compilatore, configurazioni di progetto, firma di assembly o comandi personalizzati. Il codice in un progetto condiviso eredita in modo efficace questi valori da qualsiasi riferimento.

Il pannello **Proprietà** è illustrato di seguito. lo **spazio dei nomi radice** è l'unica impostazione che è possibile modificare.

![](shared-projects-images/vs-sharedprojectproperties.png "Shared Project Properties")

-----

<a name="Shared_Project_Example"/>

## <a name="shared-project-example"></a>Esempio di progetto condiviso

L'esempio di [attività](https://github.com/xamarin/mobile-samples/tree/master/Tasky) usa un progetto condiviso per contenere il codice comune usato dalle applicazioni iOS, Android e Windows Phone. Entrambi i file di codice sorgente `SQLite.cs` e `TaskRepository.cs` utilizzano le direttive del compilatore, ad esempio `#if __ANDROID__`) per produrre output diverso per ognuna delle applicazioni che vi fanno riferimento.

La struttura completa della soluzione è illustrata di seguito (rispettivamente in Visual Studio per Mac e Visual Studio):

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

![](shared-projects-images/xs-examplesolution.png "Visual Studio for Mac solution")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](shared-projects-images/vs-examplesolution.png "Visual Studio solution")

-----

Il progetto Windows Phone può essere esplorato dall'interno di Visual Studio per Mac, anche se tale tipo di progetto non è supportato per la compilazione in Visual Studio per Mac.

Le applicazioni in esecuzione sono illustrate di seguito:

![](shared-projects-images/example.png "iOS, Android, Windows Phone examples")

## <a name="summary"></a>Riepilogo

In questo documento è stato descritto il funzionamento di progetti condivisi, il modo in cui possono essere creati e utilizzati sia in Visual Studio per Mac che in Visual Studio e viene introdotta una semplice applicazione di esempio che illustra un progetto condiviso in azione.

## <a name="related-links"></a>Collegamenti correlati

- [App di esempio Tasky](https://github.com/xamarin/mobile-samples/tree/master/Tasky)
- [Librerie di classi portabili (esempio)](~/cross-platform/app-fundamentals/pcl.md)
- [Opzioni di condivisione del codice (esempio)](~/cross-platform/app-fundamentals/code-sharing.md)
