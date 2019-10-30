---
title: Parte 3-configurazione di una soluzione multipiattaforma Novell
description: Questo documento descrive come configurare una soluzione multipiattaforma in Novell. Vengono illustrate varie strategie di condivisione del codice, ad esempio progetti condivisi e .NET Standard.
ms.prod: xamarin
ms.assetid: 4139A6C2-D477-C563-C1AB-98CCD0D10A93
author: davidortinau
ms.author: daortin
ms.date: 03/27/2017
ms.openlocfilehash: 843887282c9a5af671d46699ae2f601fd32902e0
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016876"
---
# <a name="part-3---setting-up-a-xamarin-cross-platform-solution"></a>Parte 3-configurazione di una soluzione multipiattaforma Novell

Indipendentemente dalle piattaforme utilizzate, i progetti Novell utilizzano lo stesso formato di file di soluzione (il formato di file con estensione **sln** di Visual Studio). Le soluzioni possono essere condivise tra ambienti di sviluppo, anche quando non è possibile caricare singoli progetti, ad esempio un progetto Windows in Visual Studio per Mac.

Quando si crea una nuova applicazione multipiattaforma, il primo passaggio consiste nel creare una soluzione vuota. Questa sezione spiega cosa accade successivamente: configurazione dei progetti per la creazione di app per dispositivi mobili multipiattaforma.

 <a name="Sharing_Code" />

## <a name="sharing-code"></a>Condivisione del codice

Per una descrizione dettagliata dell'implementazione della condivisione del codice tra le piattaforme, vedere il documento relativo alle [Opzioni di condivisione del codice](~/cross-platform/app-fundamentals/code-sharing.md) .

 <a name="Shared_Asset_Projects" />

### <a name="shared-projects"></a>Progetti condivisi

L'approccio più semplice per condividere file di codice consiste nell'usare un [progetto condiviso](~/cross-platform/app-fundamentals/shared-projects.md).

Questo metodo consente di condividere lo stesso codice tra diversi progetti di piattaforma e di usare le direttive del compilatore per includere percorsi di codice diversi, specifici della piattaforma.

 <a name="Portable_Class_Libraries" />

### <a name="portable-class-libraries-pcl"></a>Librerie di classi portabili

In passato, un file di progetto .NET (e l'assembly risultante) è stato indirizzato a una versione specifica del Framework. In questo modo si impedisce che il progetto o l'assembly venga condiviso da Framework diversi.

Una libreria di classi portabile (PCL) è un tipo speciale di progetto che può essere usato in diverse piattaforme CLI, ad esempio Novell. iOS e Novell. Android, nonché WPF, piattaforma UWP (Universal Windows Platform) e Xbox. La libreria può utilizzare solo un subset di .NET Framework completo, limitato dalle piattaforme di destinazione.

Altre informazioni sul supporto di Novell [per le librerie di classi](~/cross-platform/app-fundamentals/pcl.md) portabili e seguire le istruzioni per vedere come funziona l'esempio di [TaskyPortable](https://github.com/xamarin/mobile-samples/tree/master/TaskyPortable) .

### <a name="net-standard"></a>.NET Standard

Introdotta in 2016, [.NET standard](~/cross-platform/app-fundamentals/net-standard.md) progetti forniscono un modo semplice per condividere il codice tra piattaforme, producendo assembly che possono essere usati in Windows, piattaforme Novell (iOS, Android, Mac) e Linux.

È possibile creare librerie di .NET Standard e usarle come classi portabili, ad eccezione del fatto che le API disponibili in ogni versione (da 1,0 a 1,6) sono più facilmente individuate e ogni versione è compatibile con i numeri di versione inferiori.

 <a name="Populating_the_Solution" />

## <a name="populating-the-solution"></a>Popolamento della soluzione

Indipendentemente dal metodo usato per condividere il codice, la struttura complessiva della soluzione deve implementare un'architettura a più livelli che incoraggi la condivisione del codice.
L'approccio Novell consiste nel raggruppare il codice in due tipi di progetto:

- **Progetto di base** : scrivere codice riutilizzabile in un'unica posizione, da condividere tra piattaforme diverse. Usare i principi di incapsulamento per nascondere i dettagli di implementazione laddove possibile.
- **Progetti di applicazioni specifiche della piattaforma** : utilizzare il codice riutilizzabile con il minor accoppiamento possibile. A questo livello sono state aggiunte funzionalità specifiche della piattaforma, basate sui componenti esposti nel progetto di base.

 <a name="Core_Project" />

### <a name="core-project"></a>Progetto di base

I progetti di codice condiviso devono fare riferimento solo a assembly disponibili in tutte le piattaforme, ad esempio. gli spazi dei nomi del Framework comuni, come `System`, `System.Core` e `System.Xml`.

I progetti condivisi devono implementare la maggior parte delle funzionalità non dell'interfaccia utente possibili, che potrebbero includere i livelli seguenti:

- **Livello dati** : codice che gestisce l'archiviazione dei dati fisici, ad esempio.  [Sqlite-net](https://github.com/praeclarum/sqlite-net), un database alternativo come [Realm.io](https://realm.io/products/realm-mobile-database/) o anche file XML. Le classi del livello dati vengono in genere utilizzate solo dal livello di accesso ai dati.
- **Livello di accesso ai dati** : definisce un'API che supporta le operazioni sui dati richieste per le funzionalità dell'applicazione, ad esempio i metodi per accedere a elenchi di dati, singoli elementi di dati e anche crearli, modificarli ed eliminarli.
- **Livello di accesso al servizio** : un livello facoltativo per fornire servizi cloud all'applicazione. Contiene codice che accede a risorse di rete remote (servizi Web, download di immagini e così via) e possibilmente caching dei risultati.
- **Livello business** : definizione delle classi del modello e delle classi di facciata o Manager che espongono la funzionalità alle applicazioni specifiche della piattaforma.

 <a name="Platform-Specific_Application_Projects" />

### <a name="platform-specific-application-projects"></a>Progetti di applicazioni specifiche della piattaforma

Per i progetti specifici della piattaforma è necessario fare riferimento agli assembly necessari per eseguire l'associazione a ogni SDK della piattaforma (Novell. iOS, Novell. Android, Novell. Mac o Windows) e al progetto di codice condiviso principale.

I progetti specifici della piattaforma devono implementare:

- **Livello dell'applicazione** : funzionalità specifiche della piattaforma e associazione/conversione tra gli oggetti livello business e l'interfaccia utente.
- **Livello interfaccia utente** : schermate, controlli dell'interfaccia utente personalizzati, presentazione della logica di convalida.

<a name="Example" />

### <a name="example"></a>Esempio

In questo diagramma viene illustrata l'architettura dell'applicazione:

 [![](setting-up-a-xamarin-cross-platform-solution-images/conceptualarchitecture.png "The application architecture is illustrated in this diagram")](setting-up-a-xamarin-cross-platform-solution-images/conceptualarchitecture.png#lightbox)

Questo screenshot mostra la configurazione di una soluzione con i progetti di applicazioni core condivise, iOS e Android. Il progetto condiviso contiene codice correlato a ognuno dei livelli architetturali (codice aziendale, servizio, dati e accesso ai dati):

 ![](setting-up-a-xamarin-cross-platform-solution-images/core-solution-example.png "The Shared Project contains code relating to each of the architectural layers (Business, Service, Data and Data Access code)")

 <a name="Project_References" />

## <a name="project-references"></a>Riferimenti al progetto

I riferimenti al progetto riflettono le dipendenze per un progetto. I progetti di base limitano i riferimenti agli assembly comuni, in modo che il codice sia facile da condividere.
I progetti di applicazione specifici della piattaforma fanno riferimento al codice condiviso, oltre a tutti gli altri assembly specifici della piattaforma necessari per sfruttare la piattaforma di destinazione.

L'applicazione proietta ogni progetto condiviso di riferimento e contiene il codice dell'interfaccia utente necessario per presentare le funzionalità all'utente, come illustrato nelle schermate seguenti:

![](setting-up-a-xamarin-cross-platform-solution-images/solution-android.png "L'applicazione proietta ogni progetto condiviso di riferimento") ![](setting-up-a-xamarin-cross-platform-solution-images/solution-ios.png "L'applicazione proietta ogni progetto condiviso di riferimento")

Esempi specifici del modo in cui i progetti devono essere strutturati vengono forniti nei case study.

 <a name="Adding_Files" />

## <a name="adding-files"></a>Aggiunta di file

 <a name="Build_Action" />

### <a name="build-action"></a>Azione di compilazione

È importante impostare l'azione di compilazione corretta per determinati tipi di file. Questo elenco Mostra l'azione di compilazione per alcuni tipi di file comuni:

- **Tutti C# i file** -azione di compilazione: compila
- **Immagini in Novell. iOS & Windows** -azione di compilazione: contenuto
- **XIB e i file storyboard in Novell. iOS** -azione di compilazione: InterfaceDefinition
- **Immagini e layout AXML in Android** -azione di compilazione: AndroidResource
- **File XAML in progetti Windows** -azione di compilazione: pagina
- **File XAML Novell. Forms** -azione di compilazione: EmbeddedResource

L'IDE rileva in genere il tipo di file e suggerisce l'azione di compilazione corretta.

 <a name="Case_Sensitivity" />

### <a name="case-sensitivity"></a>Distinzione fra maiuscole e minuscole

Infine, tenere presente che alcune piattaforme hanno file System con distinzione tra maiuscole e minuscole, ad esempio
iOS e Android. Assicurarsi quindi di usare uno standard di denominazione dei file coerente e assicurarsi che i nomi file usati nel codice corrispondano esattamente al file System. Questa operazione è particolarmente importante per le immagini e altre risorse a cui si fa riferimento nel codice.
