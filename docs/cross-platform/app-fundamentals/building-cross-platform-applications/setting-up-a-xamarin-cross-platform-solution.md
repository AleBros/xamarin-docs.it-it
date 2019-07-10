---
title: 'Parte 3: configurazione di una soluzione Xamarin multipiattaforma'
description: Questo documento descrive come configurare una soluzione multipiattaforma in Xamarin. Vengono illustrate diverse strategie, ad esempio i progetti condivisi e .NET Standard di condivisione del codice.
ms.prod: xamarin
ms.assetid: 4139A6C2-D477-C563-C1AB-98CCD0D10A93
author: asb3993
ms.author: amburns
ms.date: 03/27/2017
ms.openlocfilehash: 2117eb91f25f2fb890b419fa7c4235b8f646729d
ms.sourcegitcommit: 58d8bbc19ead3eb535fb8248710d93ba0892e05d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67675021"
---
# <a name="part-3---setting-up-a-xamarin-cross-platform-solution"></a>Parte 3: configurazione di una soluzione Xamarin multipiattaforma

Indipendentemente dalle quali piattaforme sono in uso, tutti i progetti Xamarin usano lo stesso formato di file di soluzione (Visual Studio **sln** formato file). Le soluzioni possono essere condivisi tra gli ambienti di sviluppo, anche quando non è possibile caricare progetti singoli (ad esempio, un progetto di Windows in Visual Studio per Mac).



Quando si crea una nuova applicazione multi-piattaforma, il primo passaggio consiste nel creare una soluzione vuota. Questa sezione viene illustrato ciò che accade: configurare progetti per la creazione di App per dispositivi mobili multipiattaforma.

 <a name="Sharing_Code" />


## <a name="sharing-code"></a>Condivisione del codice

Vedere le [opzioni di condivisione del codice](~/cross-platform/app-fundamentals/code-sharing.md) documento per una descrizione dettagliata di come implementare la condivisione del codice tra piattaforme.

 <a name="Shared_Asset_Projects" />


### <a name="shared-projects"></a>Progetti condivisi

L'approccio più semplice per la condivisione file di codice Usa un [progetto condiviso](~/cross-platform/app-fundamentals/shared-projects.md).

Questo metodo consente di condividere lo stesso codice nei progetti di piattaforma diversi e usare le direttive del compilatore per includere i percorsi del codice diversi, specifici della piattaforma.

 <a name="Portable_Class_Libraries" />


### <a name="portable-class-libraries-pcl"></a>Librerie di classi portabili

In passato un file di progetto .NET (e l'assembly risultante) è state assegnate a una versione specifica del framework. In questo modo il progetto o assembly condiviso da diversi Framework.

Una libreria di classi portabile (PCL) è un tipo speciale di progetto che può essere usato tra più piattaforme diverse dell'interfaccia della riga, ad esempio xamarin. IOS e xamarin. Android, così come WPF, Universal Windows Platform e Xbox. La libreria può usare solo un subset del framework .NET completo, limitato dalle piattaforme di destinazione.

Altre informazioni su Xamarin [supporto per librerie di classi portabili](~/cross-platform/app-fundamentals/pcl.md) e seguire le istruzioni per visualizzare come il [TaskyPortable esempio](https://github.com/xamarin/mobile-samples/tree/master/TaskyPortable) funziona.


### <a name="net-standard"></a>.NET Standard

Introdotta in 2016 [.NET Standard](~/cross-platform/app-fundamentals/net-standard.md) progetti forniscono un modo semplice per condividere codice tra piattaforme, producendo gli assembly che possono essere usati in Windows, Xamarin piattaforme (iOS, Android, Mac) e Linux.

Librerie .NET standard possono essere create e usate, ad esempio librerie di classi portabili, ad eccezione del fatto che le API disponibili nelle singole versioni (dalla 1.0 alla versione 1.6) vengono individuate più facilmente e ciascuna versione è compatibile con i numeri di versione più bassi.



 <a name="Populating_the_Solution" />


## <a name="populating-the-solution"></a>La compilazione della soluzione

Indipendentemente da quale metodo viene usato per condividere codice, la struttura complessiva della soluzione deve implementare un'architettura a più livelli che incoraggia la condivisione del codice.
L'approccio Xamarin consiste nel gruppo di codice in due tipi di progetto:

-   **Progetto core** : scrivere il codice riutilizzabile in un'unica posizione, per essere condivisi tra piattaforme diverse. Usare i principi di incapsulamento per nascondere i dettagli di implementazione laddove possibile.
-   **I progetti di applicazioni specifiche della piattaforma** – usano il codice riutilizzabile con come minimo accoppiamento possibile. Funzionalità specifiche della piattaforma vengono aggiunti a questo livello, basato su componenti esposti nel progetto Core.


 <a name="Core_Project" />


### <a name="core-project"></a>Progetto core

I progetti di codice condiviso devono fare riferimento solo gli assembly a cui sono disponibili in tutte le piattaforme: Internet Explorer. spazi dei nomi comuni di framework, ad esempio `System`, `System.Core` e `System.Xml`.

Progetti condivisi devono implementare tutte le funzionalità non di interfaccia utente come è possibile, che potrebbe includere i livelli seguenti:

-   **Livello dati** : il codice che si occupa dell'archivio dati fisico ad es.  [SQLite-NET](https://github.com/praeclarum/sqlite-net), ad esempio un database alternativo [Realm.io](https://realm.io/products/realm-mobile-database/) o anche i file XML. Le classi del livello dati sono in genere usata solo dal livello di accesso ai dati.
-   **Livello di accesso ai dati** – definisce un'API che supporta le operazioni di dati necessari per le funzionalità dell'applicazione, ad esempio i metodi per accedere a elenchi di dati, singoli elementi di dati e inoltre creare, modificare ed eliminarli.
-   **Livello di accesso ai servizi** : un livello facoltativo per fornire cloud services all'applicazione. Contiene il codice che accede alle risorse di rete remoto (servizi web, il download di immagini e così via) e probabilmente la memorizzazione nella cache dei risultati.
-   **Livello di business** : definizione di classi del modello e le classi di facciata o di gestione che espongono la funzionalità per le applicazioni specifiche della piattaforma.


 <a name="Platform-Specific_Application_Projects" />


### <a name="platform-specific-application-projects"></a>Progetti di applicazioni specifiche della piattaforma

Progetti specifici delle piattaforme devono fare riferimento agli assembly necessari per eseguire l'associazione a SDK entrambe le piattaforme (xamarin. IOS, xamarin. Android, xamarin. Mac o Windows), nonché il progetto di codice condiviso Core.

I progetti specifici della piattaforma devono implementare:

-   **Livello dell'applicazione** – funzionalità specifiche della piattaforma e la conversione o dell'associazione tra gli oggetti di livello aziendale e l'interfaccia utente.
-   **Livello dell'interfaccia utente** : le schermate, controlli dell'interfaccia utente personalizzata, presentazione di logica di convalida.


<a name="Example" />


### <a name="example"></a>Esempio

L'architettura dell'applicazione è illustrata nella figura seguente:

 [![](setting-up-a-xamarin-cross-platform-solution-images/conceptualarchitecture.png "Nella figura seguente è illustrata l'architettura dell'applicazione")](setting-up-a-xamarin-cross-platform-solution-images/conceptualarchitecture.png#lightbox)

Questo screenshot illustra una configurazione di soluzione con il progetto Core condiviso, iOS e i progetti applicazione Android. Il progetto condiviso contiene codice che fa riferimento a ciascuno dei livelli architetturali (codice di Business, servizio, i dati e l'accesso ai dati):

 ![](setting-up-a-xamarin-cross-platform-solution-images/core-solution-example.png "Il progetto condiviso contiene codice che fa riferimento a ciascuno dei livelli architetturali (codice di Business, servizio, i dati e l'accesso ai dati)")


 <a name="Project_References" />


## <a name="project-references"></a>Riferimenti al progetto

I riferimenti al progetto riflettono le dipendenze per un progetto. I progetti core limitano i riferimenti agli assembly common in modo che il codice è facile condividere.
I progetti di applicazioni specifiche della piattaforma di riferimento nel codice condiviso, oltre a eventuali altri assembly specifici della piattaforma che necessarie per sfruttare i vantaggi della piattaforma di destinazione.

I progetti dell'applicazione ogni fare riferimento a progetto condiviso e contengono il codice dell'interfaccia utente necessario per la funzionalità presenta all'utente, come illustrato nelle schermate illustrate:

![](setting-up-a-xamarin-cross-platform-solution-images/solution-android.png "L'applicazione progetti ogni riferimento a progetto condiviso") ![](setting-up-a-xamarin-cross-platform-solution-images/solution-ios.png "l'applicazione progetti ogni riferimento a progetto condiviso")


Vengono forniti esempi specifici del modo in cui devono essere strutturati i progetti nei case study.

 <a name="Adding_Files" />


## <a name="adding-files"></a>Aggiunta di file

 <a name="Build_Action" />


### <a name="build-action"></a>Azione di compilazione

È importante impostare l'azione di compilazione corretta per alcuni tipi di file. Questo elenco Mostra l'azione di compilazione per alcuni tipi di file comuni:

-  **Tutti i C# file** -azione di compilazione: Compile
-   **Le immagini in xamarin. IOS e Windows** -azione di compilazione: Content
-   **File XIB e Storyboard in xamarin. IOS** -azione di compilazione: InterfaceDefinition
-   **Le immagini e layout AXML Android** -azione di compilazione: AndroidResource
-  **I file XAML in progetti Windows** -azione di compilazione: Pagina
-  **I file XAML di xamarin. Forms** -azione di compilazione: EmbeddedResource


In genere l'IDE rileverà il tipo di file e suggerire l'azione di compilazione corretta.

 <a name="Case_Sensitivity" />


### <a name="case-sensitivity"></a>Distinzione fra maiuscole e minuscole

Infine, tenere presente che alcune piattaforme hanno i sistemi di file tra maiuscole e minuscole (ad es.
iOS e Android) pertanto assicurarsi di usare uno standard di denominazione di file coerente e assicurarsi che i nomi dei file usato nel codice corrispondano esattamente il file System. Ciò è particolarmente importante per le immagini e altre risorse di cui viene fatto riferimento nel codice.
