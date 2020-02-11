---
title: >
  Parte 3-configurazione di una soluzione multipiattaforma Xamarin
description: >-
  Questo documento descrive come configurare una soluzione multipiattaforma in Xamarin

  . Vengono illustrate varie strategie di condivisione del codice, ad esempio progetti condivisi e .NET Standard.
ms.prod: xamarin
ms.assetid: 4139A6C2-D477-C563-C1AB-98CCD0D10A93
author: davidortinau
ms.author: daortin
ms.date: 03/27/2017
ms.openlocfilehash: e7cde22115830a845ed82aa907195521f36b6866
ms.sourcegitcommit: d8af612b6b3218fea396d2f180e92071c4d4bf92
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2020
ms.locfileid: "75663273"
---
# <a name="part-3---setting-up-a-xamarin-cross-platform-solution"></a>Parte 3-configurazione di una soluzione multipiattaforma Xamarin


Indipendentemente dalle piattaforme utilizzate, i progetti Xamarin utilizzano lo stesso formato di file di soluzione (il formato di file con estensione **sln** di Visual Studio). Le soluzioni possono essere condivise tra ambienti di sviluppo, anche quando non è possibile caricare singoli progetti, ad esempio un progetto Windows in Visual Studio per Mac.

Quando si crea una nuova applicazione multipiattaforma, il primo passaggio consiste nel creare una soluzione vuota. Questa sezione spiega cosa accade successivamente: configurazione dei progetti per la creazione di app per dispositivi mobili multipiattaforma.

## <a name="sharing-code"></a>Condivisione del codice

Per una descrizione dettagliata dell'implementazione della condivisione del codice tra le piattaforme, vedere il documento relativo alle [Opzioni di condivisione del codice](~/cross-platform/app-fundamentals/code-sharing.md) .

### <a name="net-standard"></a>.NET Standard

I progetti [.NET standard](~/cross-platform/app-fundamentals/net-standard.md) offrono un modo semplice per condividere il codice tra piattaforme, producendo assembly che possono essere usati in Windows, piattaforme Xamarin (iOS, Android, Mac) e Linux.
Questo è il modo consigliato per condividere il codice per le soluzioni Xamarin
.

### <a name="other-options"></a>Altre opzioni

In passato, Xamarin usava le librerie di classi portabili [(classi portabili)](~/cross-platform/app-fundamentals/pcl.md)] e i [progetti condivisi](~/cross-platform/app-fundamentals/shared-projects.md). Nessuna di queste è consigliata per i nuovi progetti. ed è consigliabile eseguire la migrazione delle app esistenti per usare .NET Standard.

## <a name="populating-the-solution"></a>Popolamento della soluzione

Indipendentemente dal metodo usato per condividere il codice, la struttura complessiva della soluzione deve implementare un'architettura a più livelli che incoraggi la condivisione del codice.
L'approccio Xamarin consiste nel raggruppare il codice in due tipi di progetto:

- **Progetto di base (o condiviso)** : scrivere codice riutilizzabile in un'unica posizione per essere condiviso tra piattaforme diverse. Usare i principi di incapsulamento per nascondere i dettagli di implementazione laddove possibile.
- **Progetti di applicazioni specifiche della piattaforma** : utilizzare il codice riutilizzabile con il minor accoppiamento possibile. A questo livello sono state aggiunte funzionalità specifiche della piattaforma, basate sui componenti esposti nel progetto di base.

### <a name="core-project"></a>Progetto di base

I progetti principali che condividono il codice devono essere .NET Standard e solo gli assembly di riferimento disponibili in tutte le piattaforme, ad esempio. gli spazi dei nomi del Framework comuni, come `System`, `System.Core` e `System.Xml`.

I progetti di base devono implementare la maggior parte delle funzionalità non dell'interfaccia utente possibili, che possono includere i livelli seguenti:

- **Livello dati** : codice che gestisce l'archiviazione dei dati fisici, ad esempio. [Sqlite-net](https://www.nuget.org/packages/sqlite-net-pcl/) o anche file XML. Le classi del livello dati vengono in genere utilizzate solo dal livello di accesso ai dati.
- **Livello di accesso ai dati** : definisce un'API che supporta le operazioni sui dati richieste per le funzionalità dell'applicazione, ad esempio i metodi per accedere a elenchi di dati, singoli elementi di dati e anche crearli, modificarli ed eliminarli.
- **Livello di accesso al servizio** : un livello facoltativo per fornire servizi cloud all'applicazione. Contiene codice che accede a risorse di rete remote (servizi Web, download di immagini e così via) e possibilmente caching dei risultati.
- **Livello business** : definizione delle classi del modello e delle classi di facciata o Manager che espongono la funzionalità alle applicazioni specifiche della piattaforma.

### <a name="platform-specific-application-projects"></a>Progetti di applicazioni specifiche della piattaforma

Per i progetti specifici della piattaforma è necessario fare riferimento agli assembly necessari per eseguire l'associazione a ogni SDK della piattaforma (Xamarin.iOS, Xamarin.Android, Xamarin.Mac o Windows) e al progetto .NET Standard.

I progetti specifici della piattaforma devono implementare:

- **Livello dell'applicazione** : funzionalità specifiche della piattaforma e associazione/conversione tra gli oggetti livello business e l'interfaccia utente.
- **Livello interfaccia utente** : schermate, controlli dell'interfaccia utente personalizzati, presentazione della logica di convalida.

## <a name="project-references"></a>Riferimenti al progetto

I riferimenti al progetto riflettono le dipendenze per un progetto. I progetti di base limitano i riferimenti agli assembly comuni, in modo che il codice sia facile da condividere.
I progetti di applicazione specifici della piattaforma fanno riferimento al progetto .NET Standard, oltre a tutti gli altri assembly specifici della piattaforma necessari per sfruttare la piattaforma di destinazione.

Esempi specifici del modo in cui i progetti devono essere strutturati vengono forniti nei case study.

## <a name="adding-files"></a>Aggiunta di file

### <a name="build-action"></a>Azione di compilazione

È importante impostare l'azione di compilazione corretta per determinati tipi di file. Questo elenco Mostra l'azione di compilazione per alcuni tipi di file comuni:

- **Tutti C# i file** -azione di compilazione: compila
- **Immagini in Xamarin.iOS & Windows** -azione di compilazione: contenuto
- **XIB e i file storyboard in Xamarin.iOS** -azione di compilazione: InterfaceDefinition
- **Immagini e layout XML in Android** -azione di compilazione: AndroidResource
- **File XAML in progetti Windows** -azione di compilazione: pagina
- **File XAML Xamarin.Forms** -azione di compilazione: EmbeddedResource

L'IDE rileva in genere il tipo di file e suggerisce l'azione di compilazione corretta.

### <a name="case-sensitivity"></a>Distinzione maiuscole/minuscole

Infine, tenere presente che alcune piattaforme hanno file System con distinzione tra maiuscole e minuscole, ad esempio
iOS e Android. Assicurarsi quindi di usare uno standard di denominazione dei file coerente e assicurarsi che i nomi file usati nel codice corrispondano esattamente al file System. Questa operazione è particolarmente importante per le immagini e altre risorse a cui si fa riferimento nel codice.
