---
title: Lavoro provider come contenuto
ms.prod: xamarin
ms.assetid: B9E2EF89-7EBE-45F5-1ED9-7D2C70BE792C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: df4c2e10e34c308e4fadb44fba9c6a14714ae1b9
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60952849"
---
# <a name="how-content-providers-work"></a>Lavoro provider come contenuto

Esistono due classi coinvolte in un `ContentProvider` interazione:

- **ContentProvider** &ndash; implementa un'API che espone un set di dati in modo standard. I metodi principali sono Query, Insert, Update e Delete.

- **ContentResolver** &ndash; un proxy statico che comunica con un `ContentProvider` per accedere ai dati, all'interno della stessa applicazione o da un'altra applicazione.

Un provider di contenuti è in genere supportato da un database SQLite, ma l'API significa che utilizza codice non è necessario conoscere il SQL sottostante. Le query vengono eseguite tramite un Uri usando le costanti per fare riferimento a nomi di colonna (in modo da ridurre le dipendenze nella struttura di dati sottostanti) e un `ICursor` viene restituito per il codice utilizzato per l'iterazione.


## <a name="consuming-a-contentprovider"></a>Utilizzo di un ContentProvider

`ContentProviders` esporre le proprie funzionalità tramite un Uri che viene registrato nel **androidmanifest. XML** dell'applicazione che pubblica i dati. Vi è una convenzione in cui l'Uri e le colonne di dati che vengono esposte devono essere disponibile come costanti per renderlo più facile da associare ai dati. Predefiniti dell'Android `ContentProviders` offrono tutti classi praticità con costanti che fanno riferimento la struttura dei dati di [ `Android.Providers` ](https://developer.xamarin.com/api/namespace/Android.Provider/) dello spazio dei nomi.



### <a name="built-in-providers"></a>Provider predefiniti

Android consente di accedere a un'ampia gamma di sistema e dati utente usando `ContentProviders`:

- *Browser* &ndash; segnalibri e la cronologia del browser (è richiesta l'autorizzazione `READ_HISTORY_BOOKMARKS` e/o `WRITE_HISTORY_BOOKMARKS`).

- *CallLog* &ndash; recenti chiamate effettuate o ricevuto con il dispositivo.

- *Contatti* &ndash; informazioni da elenco dei contatti dell'utente, tra cui le persone, telefoni, foto e i gruppi.

- *MediaStore* &ndash; contenuto del dispositivo dell'utente: audio (album, gli artisti, generi, playlist), (incluse le anteprime) di immagini e video.

- *Le impostazioni* &ndash; preferenze e impostazioni del dispositivo a livello di sistema.

- *UserDictionary* &ndash; contenuto del dizionario definito dall'utente usato per l'input di testo.

- *Messaggio vocale* &ndash; cronologia dei messaggi di posta vocale.



## <a name="classes-overview"></a>Cenni preliminari sulle classi

Le classi primarie utilizzate quando si lavora con un `ContentProvider` riportati di seguito:

[![Diagramma di classi dell'applicazione di Provider di contenuti e le interazioni tra applicazioni di uso](how-it-works-images/classdiagram1.png)](how-it-works-images/classdiagram1.png#lightbox)

In questo diagramma il `ContentProvider` implementa le query e registra URI utilizzati da altre applicazioni per individuare i dati. Il `ContentResolver` agisce come un "proxy" per il `ContentProvider` (eseguire una Query, inserimento, aggiornamento ed eliminare i metodi). Il `SQLiteOpenHelper` contiene i dati utilizzati dal `ContentProvider`, ma non è direttamente esposto a che usano le app.
Il `CursorAdapter` passa il cursore restituito dal `ContentResolver` per visualizzare in un `ListView`. Il `UriMatcher` è una classe helper che consente di analizzare gli URI durante l'elaborazione delle query.

Lo scopo di ogni classe è il seguente:

- **ContentProvider** &ndash; implementare i metodi della classe astratta per esporre i dati. L'API viene resa disponibile alle altre classi e le applicazioni tramite l'attributo Uri che viene aggiunto alla definizione della classe.

- **SQLiteOpenHelper** &ndash; consente di implementano l'archivio SQLite dati esposto dal `ContentProvider`.

- **UriMatcher** &ndash; uso `UriMatcher` nel `ContentProvider` implementazione per gestire gli URI che consentono di eseguire una query del contenuto.

- **ContentResolver** &ndash; utilizzo di codice Usa un `ContentResolver` per accedere a un `ContentProvider` istanza. Le due classi insieme occuparsi di problemi di comunicazione tra processi, consentendo di condividere facilmente tra le applicazioni dati. Utilizza codice mai crea un `ContentProvider` classe in modo esplicito; al contrario, i dati sono accessibili tramite la creazione di un cursore in base a un Uri esposto dal `ContentProvider` dell'applicazione.

- **CursorAdapter** &ndash; uso `CursorAdapter` oppure `SimpleCursorAdapter` per visualizzare i dati accessibili tramite un `ContentProvider`.

Il `ContentProvider` API consente agli utenti di eseguire una serie di operazioni sui dati, ad esempio:

-  Eseguire query sui dati per restituire gli elenchi o singoli record.
-  Modificare i singoli record.
-  Aggiungere nuovi record.
-  Eliminare i record.

Questo documento contiene un esempio che usa fornita dal sistema `ContentProvider`, nonché un semplice esempio di sola lettura che implementa un oggetto personalizzato `ContentProvider`.

