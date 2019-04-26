---
title: Usare i progetti per condividere codice condivisi
description: Progetti condivisi consentono di scrivere codice comune a cui viene fatto riferimento da un numero di progetti di applicazione diversi. Il codice viene compilato come parte di ogni progetto di riferimento e può includere le direttive del compilatore per incorporare funzionalità specifiche della piattaforma di base di codice condiviso.
ms.prod: xamarin
ms.assetid: 191c71fb-44a4-4e6c-af4b-7b1107dce6af
author: conceptdev
ms.author: crdun
ms.date: 07/18/2018
ms.openlocfilehash: 61096635cd94d0fdd0abe6fda59c4efa41eeceb1
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61193229"
---
# <a name="shared-projects-code-sharing"></a>Condividere i progetti di condivisione del codice

_Progetti condivisi consentono di scrivere codice comune a cui viene fatto riferimento da un numero di progetti di applicazione diversi. Il codice viene compilato come parte di ogni progetto di riferimento e può includere le direttive del compilatore per incorporare funzionalità specifiche della piattaforma di base di codice condiviso._

Progetti condivisi (talvolta denominati progetti di Asset condivisi) consentono di scrivere codice che è condiviso tra più progetti di destinazione, tra cui le applicazioni Xamarin.

Supportano le direttive del compilatore in modo che sia possibile includere in modo condizionale il codice specifico della piattaforma da compilare in un subset dei progetti che fanno riferimento a progetto condiviso. È inoltre disponibile il supporto IDE per gestire le direttive del compilatore e visualizzare l'aspetto di codice in ogni applicazione.

Se è stato usato il collegamento di file in precedenza per condividere codice tra progetti, i progetti condivisi funziona in modo analogo, ma con una maggiore supporto IDE.

## <a name="what-is-a-shared-project"></a>Che cos'è un progetto condiviso?

A differenza di molti altri tipi di progetto un progetto condiviso non dispone di alcun output (sotto forma di DLL), ma il codice viene compilato in ogni progetto che fa riferimento a essa. Questa operazione viene illustrata nel diagramma seguente: l'intero contenuto del progetto condiviso è concettualmente "copiato" in ogni progetto di riferimento e compilato come se fosse una parte di essi.

![](shared-projects-images/sharedassetproject.png "Architettura di progetto condiviso")

Il codice in un progetto condiviso può contenere le direttive del compilatore che consentiranno di attivare o disabilitare le sezioni di codice a seconda di quale applicazione progetto usa il codice che viene suggerito dalle finestre di piattaforma colorato nel diagramma.

Un progetto condiviso non vengono compilato in modo autonomo, esiste esclusivamente come un raggruppamento di file del codice sorgente che può essere incluso in altri progetti. Quando viene fatto riferimento da un altro progetto, il codice viene compilato in modo efficace come *parte* di tale progetto. Progetti condivisi non possono fare riferimento a qualsiasi altro tipo di progetto (inclusi altri progetti condivisi).

Si noti che i progetti applicazione Android non possono fare riferimento altri progetti applicazione Android, ad esempio, un progetto Android unit test non è possibile fare riferimento a un progetto di applicazione Android. Per altre informazioni su questa limitazione, vedere questo [forum di discussione](http://forums.xamarin.com/discussion/comment/98092/).

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

## <a name="visual-studio-for-mac-walkthrough"></a>Procedura dettagliata per Visual Studio per Mac

In questa sezione illustra in dettaglio come creare e usare un progetto condiviso in Visual Studio per Mac. Fare riferimento gli a [esempio di progetto condivisi](#Shared_Project_Example) sezione per un esempio completo.

## <a name="creating-a-shared-project"></a>Creazione di un progetto condiviso

Per creare un nuovo progetto condiviso passare a **File > nuova soluzione...**  (o fare clic con il pulsante destro una soluzione esistente e scegliendo **Aggiungi > Aggiungi nuovo progetto...** ):

[![Nuovo progetto condiviso](shared-projects-images/xs-newsolution-sml.png "nuova soluzione")](shared-projects-images/xs-newsolution.png#lightbox)

Nella schermata successiva, scegliere il nome del progetto e fare clic su **Create**.

Di seguito è riportato un nuovo progetto condiviso - si noti che vi siano presenti riferimenti o nodi componente; non sono supportati per i progetti condivisi.

![Progetto condiviso vuoto](shared-projects-images/xs-empty.png "vuoto progetto condiviso")

Per un progetto condiviso essere utile, è necessario farvi riferimento da almeno un progetto in grado di compilazione (ad esempio iOS o Android applicazione libreria o un progetto libreria di classi Portabile). Un progetto condiviso non vengono compilato quando non ha niente che fa riferimento, in tal caso sintassi (o qualsiasi altro) gli errori non verranno evidenziati fino a quando non è stato fatto riferimento da un altro elemento.

Aggiunta di un riferimento a un progetto condiviso viene eseguita esattamente come riferimento a un progetto di libreria regolare. In questo screenshot Mostra un progetto xamarin. IOS che fanno riferimento a un progetto condiviso.

![](shared-projects-images/xs-reference.png "Riferimento al progetto a progetto condiviso")

Una volta che il progetto condiviso viene fatto riferimento da un'altra libreria o l'applicazione è possibile compilare la soluzione e visualizzare eventuali errori nel codice. Quando si fa riferimento a progetto condiviso _due o più_ altri progetti, viene visualizzato un menu in alto a sinistra dell'editor del codice sorgente che illustra scelga i progetti che fanno riferimento a questo file.

## <a name="shared-project-options"></a>Le opzioni del progetto condiviso

Quando fare clic su un progetto condiviso e si sceglie **opzioni** presenti meno impostazioni altri tipi di progetto. Poiché i progetti condivisi non vengono compilati (in modo indipendente), è possibile impostare le opzioni di output o un compilatore, le configurazioni di progetto, la firma degli assembly o i comandi personalizzati. Il codice in un progetto condiviso eredita in modo efficiente questi valori da qualsiasi elemento fa riferimento a essi.



Il **opzioni** schermata è illustrata di seguito - progetto **nome** e il **Default Namespace** sono solo due impostazioni che è in genere cambia.


![](shared-projects-images/xs-sharedprojectoptions.png "Le opzioni del progetto condiviso")



# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)



## <a name="visual-studio-walkthrough"></a>Procedura dettagliata in Visual Studio


Questa sezione illustra come creare e usare un progetto condiviso usando Visual Studio. Fare riferimento gli a [esempio di progetto condivisi](#Shared_Project_Example) sezione per un'implementazione completa.

### <a name="creating-a-shared-project"></a>Creazione di un progetto condiviso

Per creare un nuovo progetto condiviso passare a **File > nuova soluzione...**  e scegliere un nome per il progetto e soluzione.

![](shared-projects-images/vs-newsolution.png "Nuova soluzione")

È anche possibile aggiungere un nuovo progetto condiviso per una soluzione facendo clic sul file di soluzione e scegliendo **Aggiungi > Nuovo progetto...** . Un nuovo progetto condiviso è simile alla seguente (dopo l'aggiunta di un file di classe), si noti che non sono presenti riferimenti o nodi componente; non sono supportati per i progetti condivisi.

![](shared-projects-images/vs-empty.png "Progetto condiviso vuoto")

Per un progetto condiviso essere utile, è necessario farvi riferimento da almeno un progetto in grado di compilazione (ad esempio iOS o Android applicazione libreria o un progetto libreria di classi Portabile). Un progetto condiviso non vengono compilato quando non ha niente che fa riferimento, in tal caso sintassi (o qualsiasi altro) gli errori non verranno evidenziati fino a quando non è stato fatto riferimento da un altro elemento.

Aggiunta di un riferimento a un progetto condiviso viene eseguita esattamente come riferimento a un progetto di libreria regolare. In questo screenshot Mostra un progetto xamarin. IOS che fanno riferimento a un progetto condiviso.

![](shared-projects-images/vs-reference.png "Riferimento al progetto a progetto condiviso")

Una volta che il progetto condiviso viene fatto riferimento da un'altra libreria o l'applicazione è possibile compilare la soluzione e visualizzare eventuali errori nel codice. Quando si fa riferimento a progetto condiviso _due o più_ altri progetti, visualizzare un menu in alto a sinistra dell'editor del codice sorgente per visualizzare i progetti che fanno riferimento a file di codice corrente.


### <a name="shared-project-properties"></a>Proprietà del progetto condiviso


Quando si seleziona un progetto condiviso sono meno impostazioni nel pannello delle proprietà rispetto ad altri tipi di progetto. Poiché i progetti condivisi non vengono compilati (in modo indipendente), è possibile impostare le opzioni di output o un compilatore, le configurazioni di progetto, la firma degli assembly o i comandi personalizzati. Il codice in un progetto condiviso eredita in modo efficiente questi valori da qualsiasi elemento fa riferimento a essi.

Il **delle proprietà** pannello viene visualizzato sotto - i **radice Namespace** è l'unica impostazione che è possibile modificare.

![](shared-projects-images/vs-sharedprojectproperties.png "Proprietà del progetto condiviso")

-----

<a name="Shared_Project_Example"/>

## <a name="shared-project-example"></a>Esempio di progetto condiviso

Il [Tasky](https://github.com/xamarin/mobile-samples/tree/master/Tasky) esempio Usa un progetto condiviso per contenere il codice comune usato in entrambi i iOS, Android e Windows Phone nelle applicazioni. Sia la `SQLite.cs` e `TaskRepository.cs` file del codice sorgente di utilizzare le direttive del compilatore (ad es. `#if __ANDROID__`) per produrre un output diverso per ognuna delle applicazioni che vi fanno riferimento.

La struttura della soluzione completa è la seguente (in Visual Studio per Mac e Visual Studio rispettivamente):

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

![](shared-projects-images/xs-examplesolution.png "Visual Studio per la soluzione di Mac")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](shared-projects-images/vs-examplesolution.png "Soluzione di Visual Studio")

-----

Il progetto Windows Phone è possibile spostarsi da Visual Studio per Mac, anche se tale tipo di progetto non è supportato per la compilazione in Visual Studio per Mac.

Le applicazioni in esecuzione sono illustrate di seguito:

![](shared-projects-images/example.png "esempi di iOS, Android, Windows Phone")

## <a name="summary"></a>Riepilogo

Questo documento descritto il funzionamento di progetti condivisi, come possono essere creati e usati in Visual Studio per Mac e Visual Studio e introdotta un'applicazione di esempio semplice che illustra un progetto condiviso in azione.

## <a name="related-links"></a>Collegamenti correlati

- [App di esempio tasky](https://github.com/xamarin/mobile-samples/tree/master/Tasky)
- [Librerie di classi portabili (esempio)](~/cross-platform/app-fundamentals/pcl.md)
- [La condivisione di codice le opzioni (esempio)](~/cross-platform/app-fundamentals/code-sharing.md)
