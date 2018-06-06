---
title: Utilizzando i progetti per condividere codice condivisi
description: Progetti condivisi consentono di scrivere codice comune a cui fa riferimento un numero di progetti di applicazione diverso. Il codice viene compilato come parte di ogni progetto di riferimento e può includere le direttive del compilatore per incorporare funzionalità specifiche della piattaforma di base di codice condiviso.
ms.prod: xamarin
ms.assetid: 191c71fb-44a4-4e6c-af4b-7b1107dce6af
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 749daac36cce26c9d24d04b89e933aab994791b4
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34781803"
---
# <a name="using-shared-projects-to-share-code"></a>Utilizzando i progetti per condividere codice condivisi

_Progetti condivisi consentono di scrivere codice comune a cui fa riferimento un numero di progetti di applicazione diverso. Il codice viene compilato come parte di ogni progetto di riferimento e può includere le direttive del compilatore per incorporare funzionalità specifiche della piattaforma di base di codice condiviso._

Progetti condivisi (anche detti progetti Asset condivisi) consentono di scrivere codice che viene condiviso tra più progetti di destinazione, incluse le applicazioni di Xamarin.

Direttive del compilatore supportano in modo che è possibile includere in modo condizionale il codice specifico della piattaforma per essere compilato in un subset dei progetti che fanno riferimento a progetto condiviso. È inoltre disponibile il supporto IDE per gestire le direttive del compilatore e visualizzare il codice come apparirà in ogni applicazione.

Se è stato usato il file di collegamento in passato per condividere il codice tra progetti, i progetti condivisi funziona in modo analogo, ma con supporto IDE migliorato.



## <a name="what-is-a-shared-project"></a>Che cos'è un progetto condiviso?

A differenza di molti altri tipi di progetto un progetto condiviso non dispone di alcun output (sotto forma di DLL), invece, il codice viene compilato in ogni progetto che fa riferimento a essa. Come illustrato nel diagramma seguente, a livello concettuale l'intero contenuto del progetto condiviso "copiate" in ogni progetto di riferimento e compilato come se fosse una parte di essi.

 ![](shared-projects-images/sharedassetproject.png "Architettura di progetto condiviso")

Il codice in un progetto condiviso può contenere le direttive del compilatore abilitare o disabilitare le sezioni di codice, a seconda di quale applicazione progetto utilizza il codice che viene suggerito dalle caselle colorate piattaforma nel diagramma.

Un progetto condiviso non vengono compilato in modo autonomo, esiste esclusivamente da un gruppo di file del codice sorgente che possono essere inclusi in altri progetti. Quando si fa riferimento a un altro progetto, il codice viene compilato in modo efficace come *parte* del progetto. Progetti condivisi non possono fare riferimento a qualsiasi altro tipo di progetto (inclusi altri progetti condivisi).

Si noti che i progetti di applicazione Android non possono fare riferimento altri progetti di applicazione Android, ad esempio, un progetto di test unità Android non è possibile fare riferimento a un progetto di applicazione Android. Per ulteriori informazioni su questa limitazione, vedere questo [forum di discussione](http://forums.xamarin.com/discussion/comment/98092/).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)



## <a name="visual-studio-for-mac-walkthrough"></a>Procedura dettagliata per Visual Studio per Mac


In questa sezione illustra in dettaglio come creare e utilizzare un progetto condiviso utilizzando Visual Studio per Mac. Consultare la per [condiviso di esempio di progetto](#Shared_Project_Example) sezione per un esempio completo.


## <a name="creating-a-shared-project"></a>Creazione di un progetto condiviso


Per creare un nuovo progetto condiviso passare a **File > nuova soluzione...**  e scegliere un nome.


![](shared-projects-images/xs-newsolution.png "Nuova soluzione")


È anche possibile aggiungere un nuovo progetto condiviso a una soluzione facendo clic sul file di soluzione e scegliendo **Aggiungi > Aggiungi nuovo progetto...** . Un nuovo aspetto di progetto condiviso come illustrato di seguito, si noti non sono presenti riferimenti o nodi componente; non sono supportati per i progetti condivisi.


![](shared-projects-images/xs-empty.png "Progetto condiviso vuoto")


Per un progetto condiviso essere utile, è necessario fare riferimento ad almeno uno in grado di compilazione progetto (ad esempio un iOS o Android applicazione libreria o un progetto libreria di classi Portabile). Un progetto condiviso non vengono compilato quando non ha nulla a che fa riferimento, in tal caso sintassi (o qualsiasi altro) errori non verranno evidenziati fino a quando non è stato fatto riferimento da altri elementi.



Aggiunta di un riferimento a un progetto condiviso viene eseguita nello stesso modo di riferimento a un progetto di libreria regolare. Questa schermata è riportato un progetto xamarin che fanno riferimento a un progetto condiviso.


![](shared-projects-images/xs-reference.png "Riferimento al progetto condiviso")


Una volta che il progetto condiviso fa riferimento un'altra libreria o l'applicazione è possibile compilare la soluzione e visualizzare gli eventuali errori nel codice. Quando si fa riferimento a progetto condiviso _due o più_ altri progetti, viene visualizzato un menu in alto a sinistra dell'editor del codice sorgente che mostra sceglierne i progetti di fare riferimento al file.



## <a name="shared-project-options"></a>Opzioni progetto condiviso


Quando si fare clic su un progetto condiviso e scegliere **opzioni** sono meno impostazioni altri tipi di progetto. Poiché i progetti condivisi non vengono compilati (in modo autonomo), è possibile impostare le opzioni del compilatore o di output, le configurazioni di progetto, firma dell'assembly o i comandi personalizzati. Il codice in un progetto condiviso in modo efficace eredita questi valori di qualsiasi fa riferimento a essi.



Il **opzioni** schermata è illustrata di seguito, il progetto **nome** e **Default Namespace** solo due impostazioni che in genere verrà modificata.


![](shared-projects-images/xs-sharedprojectoptions.png "Opzioni progetto condiviso")



# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)



## <a name="visual-studio-walkthrough"></a>Procedura dettagliata in Visual Studio


In questa sezione illustra in dettaglio come creare e utilizzare un progetto condiviso utilizzando Visual Studio. Consultare la per [condiviso di esempio di progetto](#Shared_Project_Example) sezione per un'implementazione completa.


### <a name="creating-a-shared-project"></a>Creazione di un progetto condiviso


Per creare un nuovo progetto condiviso passare a **File > nuova soluzione...**  e scegliere un nome per il progetto e la soluzione.


![](shared-projects-images/vs-newsolution.png "Nuova soluzione")


È anche possibile aggiungere un nuovo progetto condiviso a una soluzione facendo clic sul file di soluzione e scegliendo **Aggiungi > Nuovo progetto...** . Un nuovo progetto condiviso è simile alla seguente (dopo l'aggiunta di un file di classe), si noti non sono presenti riferimenti o nodi componente; non sono supportati per i progetti condivisi.


![](shared-projects-images/vs-empty.png "Progetto condiviso vuoto")


Per un progetto condiviso essere utile, è necessario fare riferimento ad almeno uno in grado di compilazione progetto (ad esempio un iOS o Android applicazione libreria o un progetto libreria di classi Portabile). Un progetto condiviso non vengono compilato quando non ha nulla a che fa riferimento, in tal caso sintassi (o qualsiasi altro) errori non verranno evidenziati fino a quando non è stato fatto riferimento da altri elementi.



Aggiunta di un riferimento a un progetto condiviso viene eseguita nello stesso modo di riferimento a un progetto di libreria regolare. Questa schermata è riportato un progetto xamarin che fanno riferimento a un progetto condiviso.


![](shared-projects-images/vs-reference.png "Riferimento al progetto condiviso")


Una volta che il progetto condiviso fa riferimento un'altra libreria o l'applicazione è possibile compilare la soluzione e visualizzare gli eventuali errori nel codice. Quando si fa riferimento a progetto condiviso _due o più_ altri progetti, viene visualizzato di un menu in alto a sinistra dell'editor del codice sorgente per visualizzare i progetti di fare riferimento al file di codice corrente.


### <a name="shared-project-properties"></a>Proprietà del progetto condiviso


Quando si seleziona un progetto condiviso sono meno impostazioni nel Pannello proprietà rispetto ad altri tipi di progetto. Poiché i progetti condivisi non vengono compilati (in modo autonomo), è possibile impostare le opzioni del compilatore o di output, le configurazioni di progetto, firma dell'assembly o i comandi personalizzati. Il codice in un progetto condiviso in modo efficace eredita questi valori di qualsiasi fa riferimento a essi.



Il **proprietà** pannello visualizzato sotto - il **radice Namespace** è l'unica impostazione che è possibile modificare.


![](shared-projects-images/vs-sharedprojectproperties.png "Proprietà del progetto condiviso")



-----

<a name="Shared_Project_Example"/>

## <a name="shared-project-example"></a>Esempio di progetto condiviso

Il [Tasky](https://github.com/xamarin/mobile-samples/tree/master/Tasky) utilizza un progetto condiviso per contenere il codice comune utilizzato in entrambi iOS, Android e Windows Phone nelle applicazioni di esempio. Sia il `SQLite.cs` e `TaskRepository.cs` file del codice sorgente di utilizzare le direttive del compilatore (ad es. `#if __ANDROID__`) per generare un output diverso per ognuna delle applicazioni che vi fanno riferimento.

La struttura della soluzione completa è illustrata di seguito (in Visual Studio per Mac e Visual Studio rispettivamente):

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

 ![](shared-projects-images/xs-examplesolution.png "Visual Studio per la soluzione Mac")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

 ![](shared-projects-images/vs-examplesolution.png "Soluzione Visual Studio")

-----

Il progetto Windows Phone può essere definito dall'interno di Visual Studio per Mac, anche se tale tipo di progetto non è supportata per la compilazione in Visual Studio per Mac.

Vengono indicate le applicazioni in esecuzione.

 ![](shared-projects-images/example.png "esempi di iOS, Android, Windows Phone")



## <a name="summary"></a>Riepilogo

Questo documento è descritto il funzionamento di progetti condivisi, come creati e utilizzati in Visual Studio per Mac e Visual Studio e introdotta un'applicazione di esempio semplice che illustra un progetto condiviso in azione.

## <a name="related-links"></a>Collegamenti correlati

- [App di esempio tasky](https://github.com/xamarin/mobile-samples/tree/master/Tasky)
- [Librerie di classi portabili (esempio)](~/cross-platform/app-fundamentals/pcl.md)
- [Sharing Code Options (esempio)](~/cross-platform/app-fundamentals/code-sharing.md)
