---
title: 'Parte 3: configurazione di una soluzione di piattaforma incrociata di Xamarin'
description: Questo documento descrive come configurare una soluzione multipiattaforma con Xamarin. Viene illustrata codice diverse strategie di condivisione, ad esempio condiviso progetti e .NET Standard.
ms.prod: xamarin
ms.assetid: 4139A6C2-D477-C563-C1AB-98CCD0D10A93
author: asb3993
ms.author: amburns
ms.date: 03/27/2017
ms.openlocfilehash: f802e31d851915d33cb6dbf5866f8cba3ab90303
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34781272"
---
# <a name="part-3---setting-up-a-xamarin-cross-platform-solution"></a>Parte 3: configurazione di una soluzione di piattaforma incrociata di Xamarin

Indipendentemente dalle piattaforme in uso, tutti i progetti di Xamarin utilizzano lo stesso formato di file di soluzione (Visual Studio **sln** formato di file). Le soluzioni possono essere condivise tra ambienti di sviluppo, anche quando non è possibile caricare i singoli progetti (ad esempio un progetto di Windows in Visual Studio per Mac).



Quando si crea un nuovo tra l'applicazione della piattaforma, il primo passaggio consiste nel creare una soluzione vuota. In questa sezione vengono le operazioni successive: configurare i progetti per la compilazione di App per dispositivi mobili multipiattaforma.

 <a name="Sharing_Code" />


## <a name="sharing-code"></a>Condivisione del codice

Consultare la [opzioni di condivisione del codice](~/cross-platform/app-fundamentals/code-sharing.md) documento per una descrizione dettagliata di come implementare la condivisione del codice tra le piattaforme.

 <a name="Shared_Asset_Projects" />


### <a name="shared-projects"></a>Progetti condivisi

L'approccio più semplice per la condivisione di file di codice è utilizzare un [progetto condiviso](~/cross-platform/app-fundamentals/shared-projects.md).

Questo metodo consente di condividere lo stesso codice in progetti di piattaforma diversa e utilizzare le direttive del compilatore per includere i percorsi del codice diverse, specifici della piattaforma.

 <a name="Portable_Class_Libraries" />


### <a name="portable-class-libraries-pcl"></a>Librerie di classi portabile (PCL)

In passato un file di progetto .NET (e l'assembly risultante) è state assegnate a una versione di framework specifici. In questo modo il progetto o l'assembly condiviso da diversi Framework.

Una libreria di classe portabile (PCL) è un tipo speciale di progetto che può essere usato in diverse piattaforme CLI, ad esempio xamarin. IOS e xamarin, nonché WPF, Universal Windows Platform e Xbox. La libreria può utilizzare solo un subset di completa di .NET framework, limitato dalle piattaforme di destinazione.

Altre informazioni su Xamarin [il supporto per librerie di classi portabili](~/cross-platform/app-fundamentals/pcl.md) e seguire le istruzioni per visualizzare il modo in [TaskyPortable esempio](https://github.com/xamarin/mobile-samples/tree/master/TaskyPortable) funziona.


### <a name="net-standard"></a>.NET Standard

Introdotta in 2016, [.NET Standard](~/cross-platform/app-fundamentals/net-standard.md) progetti forniscono un modo semplice per condividere il codice tra le piattaforme, creazione di assembly che può essere utilizzato in Windows, Xamarin piattaforme (iOS, Android, Mac) e Linux.

Le librerie .NET standard possono essere create e utilizzate come PCLs, ad eccezione del fatto che le API disponibili in ogni versione (dalla 1.0 alla versione 1.6) vengono individuate più facilmente e ogni versione è compatibile con i numeri di versione inferiore.



 <a name="Populating_the_Solution" />


## <a name="populating-the-solution"></a>La compilazione della soluzione

Indipendentemente da quale metodo viene usato per condividere codice, la struttura complessiva della soluzione deve implementare un'architettura a più livelli che incoraggia la condivisione del codice.
L'approccio di Xamarin consiste nel gruppo di codice in due tipi di progetto:

-   **Progetto core** -scrivere codice riutilizzabile in un'unica posizione, per essere condivisi tra piattaforme diverse. Per nascondere i dettagli di implementazione, laddove possibile, utilizzare i principi di incapsulamento.
-   **Progetti di applicazione specifico della piattaforma** : utilizzare il codice riutilizzabile con come accoppiamento minimo possibile. Funzionalità specifiche della piattaforma vengono aggiunti a questo livello, basato su componenti esposti nel progetto principale.


 <a name="Core_Project" />


### <a name="core-project"></a>Progetti di componenti di base

Progetti di codice condiviso devono fare riferimento solo gli assembly che sono disponibili in tutte le piattaforme-Internet Explorer. ad esempio spazi dei nomi comuni framework `System`, `System.Core` e `System.Xml`.

Progetti condivisi devono implementare così tante funzionalità dell'interfaccia utente non è possibile, che potrebbe includere i seguenti livelli:

-   **Livello dati** : il codice che si occupa dell'archiviazione fisica dei dati, ad esempio.  [SQLite NET](https://github.com/praeclarum/sqlite-net), ad esempio un database alternativo [Realm.io](https://realm.io/products/realm-mobile-database/) o anche file XML. Le classi di livello dati sono in genere utilizzata solo dal livello di accesso ai dati.
-   **Livello accesso dati** – definisce un'API che supporta le operazioni di dati necessari per la funzionalità dell'applicazione, ad esempio metodi per accedere a elenchi di dati, i singoli elementi di dati e inoltre creare, modificare ed eliminare.
-   **Livello di accesso servizio** : un livello facoltativo per fornire cloud services all'applicazione. Contiene il codice che accede alle risorse di rete remoto (servizi web, il download di immagini e così via) e probabilmente la memorizzazione nella cache dei risultati.
-   **Livello business** : definizione delle classi di modello e le classi di facciata o di gestione che espongono la funzionalità per le applicazioni specifiche della piattaforma.


 <a name="Platform-Specific_Application_Projects" />


### <a name="platform-specific-application-projects"></a>Progetti di applicazione specifico della piattaforma

Progetti specifici per la piattaforma devono fare riferimento l'assembly necessario per associare a SDK di ciascuna piattaforma (xamarin. IOS, xamarin, Xamarin.Mac o Windows) e il progetto di codice condiviso di base.

I progetti specifici della piattaforma devono implementare:

-   **Livello di applicazione** – funzionalità specifiche della piattaforma e la conversione o associazione tra gli oggetti di livello aziendale e l'interfaccia utente.
-   **Livello dell'interfaccia utente** : schermate, i controlli dell'interfaccia utente personalizzata, presentazione della logica di convalida.


<a name="Example" />


### <a name="example"></a>Esempio

L'architettura dell'applicazione è illustrato nella figura seguente:

 [ ![](setting-up-a-xamarin-cross-platform-solution-images/conceptualarchitecture.png "Viene illustrata l'architettura dell'applicazione in questo diagramma")](setting-up-a-xamarin-cross-platform-solution-images/conceptualarchitecture.png#lightbox)

Questa schermata mostra una configurazione di soluzione con il progetto condiviso di base, iOS e i progetti di applicazione Android. Il progetto condiviso contiene codice che fa riferimento a ognuno dei livelli dell'architettura (codice di Business, servizio, dati e l'accesso ai dati):

 ![](setting-up-a-xamarin-cross-platform-solution-images/core-solution-example.png "Il progetto condiviso contiene codice che fa riferimento a ognuno dei livelli dell'architettura (codice di Business, servizio, dati e l'accesso ai dati)")


 <a name="Project_References" />


## <a name="project-references"></a>Riferimenti al progetto

Riferimenti al progetto riflettono le dipendenze per un progetto. Progetti di componenti di base limitare i riferimenti agli assembly comuni in modo che il codice è facile condividere.
Progetti di applicazione specifico della piattaforma fa riferimento il codice condiviso, nonché qualsiasi altro assembly specifico della piattaforma che necessarie per sfruttare la piattaforma di destinazione.

I progetti di applicazione ogni fare riferimento a progetto condiviso e contengono il codice dell'interfaccia utente necessario per le funzionalità presente all'utente, come illustrato in queste schermate:

![](setting-up-a-xamarin-cross-platform-solution-images/solution-android.png "L'applicazione progetti ogni riferimento a progetto condiviso") ![](setting-up-a-xamarin-cross-platform-solution-images/solution-ios.png "l'applicazione progetti ogni riferimento a progetto condiviso")


Esempi specifici di come strutturare i progetti utilizzati in case study.

 <a name="Adding_Files" />


## <a name="adding-files"></a>Aggiunta di file

 <a name="Build_Action" />


### <a name="build-action"></a>Azione di compilazione

È importante impostare l'azione di compilazione corretta per alcuni tipi di file. Questo elenco Mostra l'azione di compilazione per alcuni tipi di file comuni:

-  **Tutti i file c#** : azione di compilazione: Compila
-   **Le immagini in xamarin. IOS e Windows** : azione di compilazione: contenuto
-   **File XI e Storyboard in xamarin. IOS** : azione di compilazione: InterfaceDefinition
-   **Le immagini e i layout di AXML Android** : azione di compilazione: AndroidResource
-  **File XAML in progetti Windows** : azione di compilazione: pagina
-  **I file XAML di xamarin. Forms** : azione di compilazione: EmbeddedResource


In genere l'IDE rileverà il tipo di file e suggerirà l'azione di compilazione corretta.

 <a name="Case_Sensitivity" />


### <a name="case-sensitivity"></a>Distinzione fra maiuscole e minuscole

Infine, tenere presente che alcune piattaforme con distinzione tra maiuscole e file System (ad es.
iOS e Android), quindi assicurati di utilizzare uno standard di denominazione di file coerente e assicurarsi che i nomi dei file usato nel codice corrispondano esattamente il file System. Ciò è particolarmente importante per le immagini e altre risorse a cui si fa riferimento nel codice.
