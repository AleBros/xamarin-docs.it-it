---
title: Funzionamento dei provider di contenuti
ms.prod: xamarin
ms.assetid: B9E2EF89-7EBE-45F5-1ED9-7D2C70BE792C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: e61be6f0189eb825c15fd75764a16706e588ebc9
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "73020513"
---
# <a name="how-content-providers-work"></a>Funzionamento dei provider di contenuti

Esistono due classi coinvolte in un'interazione:There are two classes involved in a `ContentProvider` interaction:

- **ContentProvider** &ndash; Implementa un'API che espone un set di dati in modo standard. I metodi principali sono Query, Insert, Update e Delete.

- **ContentResolver** &ndash; Un proxy statico che `ContentProvider` comunica con i dati per accedere ai dati, dall'interno della stessa applicazione o da un'altra applicazione.

Un provider di contenuti è in genere supportato da un database SQLite, ma l'API significa che l'utilizzo di codice non è necessario conoscere l'SQL sottostante. Le query vengono eseguite tramite un URI usando costanti per fare riferimento ai nomi di colonna (per ridurre le dipendenze dalla struttura di dati sottostante) e `ICursor` viene restituito un viene restituito per il codice consumer su cui eseguire l'iterazione.

## <a name="consuming-a-contentprovider"></a>Utilizzo di un oggetto ContentProviderConsuming a ContentProvider

`ContentProviders`esporre la funzionalità tramite un URI registrato nel **file AndroidManifest.xml** dell'applicazione che pubblica i dati. Esiste una convenzione in cui l'URI e le colonne di dati esposte devono essere disponibili come costanti per semplificare l'associazione ai dati. I linguaggi predefiniti `ContentProviders` di Android forniscono classi di convenienza [`Android.Providers`](xref:Android.Provider) con costanti che fanno riferimento alla struttura dei dati nello spazio dei nomi.

### <a name="built-in-providers"></a>Provider integrati

Android offre l'accesso a una vasta `ContentProviders`gamma di dati di sistema e utente utilizzando:

- *Segnalibri del browser* &ndash; e `READ_HISTORY_BOOKMARKS` cronologia del `WRITE_HISTORY_BOOKMARKS`browser (richiede l'autorizzazione e/o ).

- *CallLog* &ndash; chiamate recenti effettuate o ricevute con il dispositivo.

- *Contatti* &ndash; informazioni dettagliate dall'elenco dei contatti dell'utente, tra cui persone, telefoni, foto & gruppi.

- *Contenuti MediaStore* &ndash; del dispositivo dell'utente: audio (album, artisti, generi, playlist), immagini (incluse le miniature) & video.

- *Impostazioni impostazioni* &ndash; e preferenze del dispositivo a livello di sistema.

- *UserDictionary* &ndash; contenuto del dizionario definito dall'utente utilizzato per l'input di testo predittivo.

- *Cronologia della segreteria* &ndash; telefonica dei messaggi della segreteria telefonica.

## <a name="classes-overview"></a>Cenni preliminari sulle classi

Di seguito sono riportate `ContentProvider` le classi principali utilizzate quando si lavora con un:The primary classes used when working with a are shown here:

[![Diagramma classi dell'applicazione del provider di contenuti e utilizzo delle interazioni tra applicazioni](how-it-works-images/classdiagram1.png)](how-it-works-images/classdiagram1.png#lightbox)

In questo diagramma, implementa `ContentProvider` le query e registra gli URI utilizzati da altre applicazioni per individuare i dati. Funge `ContentResolver` da 'proxy' per `ContentProvider` il (Query, Insert, Update, e Delete metodi). I `SQLiteOpenHelper` dati contiene `ContentProvider`utilizzati da , ma non sono esposti direttamente alle app consumer.
Passa `CursorAdapter` il cursore restituito `ContentResolver` da da `ListView`visualizzare in un oggetto . È `UriMatcher` una classe helper che analizza gli URI durante l'elaborazione delle query.

Lo scopo di ogni classe è descritto di seguito:

- **ContentProvider** &ndash; Implementare i metodi di questa classe astratta per esporre i dati. L'API viene resa disponibile ad altre classi e applicazioni tramite l'attributo Uri aggiunto alla definizione della classe.

- **SQLiteOpenHelper** &ndash; Consente di implementare l'archivio `ContentProvider`dati SQLite esposto dall'oggetto .

- **UriMatcher** &ndash; `UriMatcher` Usare `ContentProvider` nell'implementazione per gestire gli URI usati per eseguire query sul contenuto.

- **ContentResolver** &ndash; Utilizza un `ContentResolver` oggetto `ContentProvider` per accedere a un'istanza. Le due classi si occupano insieme dei problemi di comunicazione tra processi, consentendo di condividere facilmente i dati tra le applicazioni. L'utilizzo di `ContentProvider` codice non crea mai una classe esplicita; al contrario, si accede ai dati creando `ContentProvider` un cursore basato su un URI esposto dall'applicazione.

- **CursorAdapter** &ndash; `CursorAdapter` Utilizzare `SimpleCursorAdapter` o per visualizzare `ContentProvider`i dati a cui si accede tramite un oggetto .

L'API `ContentProvider` consente ai consumer di eseguire una serie di operazioni sui dati, ad esempio:The API allows consumers to perform a variety of operations on the data, such as:

- Eseguire query sui dati per restituire elenchi o singoli record.
- Modificare i singoli record.
- Aggiungere nuovi record.
- Eliminare i record.

Questo documento contiene un esempio che `ContentProvider`utilizza un oggetto fornito dal sistema, `ContentProvider`nonché un semplice esempio di sola lettura che implementa un oggetto personalizzato .
