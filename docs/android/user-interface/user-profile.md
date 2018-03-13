---
title: Profilo utente
ms.topic: article
ms.prod: xamarin
ms.assetid: 6BB01F75-5E98-49A1-BBA0-C2680905C59D
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/21/2017
ms.openlocfilehash: cf8230c5832104fd17b14532f1d32822a1fc0097
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2018
---
# <a name="user-profile"></a>Profilo utente

Android è supportata l'enumerazione delle contatti con il `ContactsContract` provider dall'API di livello 5. Ad esempio, all'elenco contatti è semplice come l'utilizzo di `ContactContracts.Contacts` classe, come illustrato nel codice seguente:

```csharp
var uri = ContactsContract.Contacts.ContentUri;
           
string[] projection = {
    ContactsContract.Contacts.InterfaceConsts.Id,
    ContactsContract.Contacts.InterfaceConsts.DisplayName };
           
var cursor = ManagedQuery (uri, projection, null, null, null);
           
if (cursor.MoveToFirst ()) {
    do {
        Console.WriteLine ("Contact ID: {0}, Contact Name: {1}",
            cursor.GetString (cursor.GetColumnIndex (projection [0])),
            cursor.GetString (cursor.GetColumnIndex (projection [1])));
                   
    } while (cursor.MoveToNext());
}
```

Con Android 4 (API livello 14), un nuovo `ContactsContact.Profile` classe è disponibile tramite il provider ContactsContract. Il `ContactsContact.Profile` fornisce l'accesso a un profilo personale per il proprietario di un dispositivo, che include dati di contatto, ad esempio del proprietario del dispositivo nome e numero di telefono.


## <a name="required-permissions"></a>Autorizzazioni necessarie

Per leggere e scrivere dati di contatto, è necessario richiedere le applicazioni di `Read_Contacts` e `Write_Contacts` autorizzazioni, rispettivamente. Inoltre, per leggere e modificare il profilo utente, le applicazioni devono richiedere la `Read_Profile` e `Write_Profile` le autorizzazioni.


## <a name="updating-profile-data"></a>Aggiornamento dei dati di profilo

Una volta queste autorizzazioni sono state impostate, un'applicazione può utilizzare le tecniche di Android normale per interagire con i dati del profilo utente. Ad esempio, per aggiornare il nome del profilo visualizzato occorre chiamare `ContentResolver.Update` con un `Uri` recuperati tramite il `ContactsContract.Profile.ContentRawContactsUri` proprietà, come illustrato di seguito:

```csharp
var values = new ContentValues ();
          
values.Put (ContactsContract.Contacts.InterfaceConsts.DisplayName,
    "John Doe");
           
ContentResolver.Update (ContactsContract.Profile.ContentRawContactsUri,
    values, null, null);
```


## <a name="reading-profile-data"></a>Lettura di dati di profilo

Esecuzione di una query per il `ContactsContact.Profile.ContentUri` legge nuovamente i dati di profilo. Ad esempio, il codice seguente leggerà nome visualizzato del profilo utente:

```csharp
string[] projection = {
    ContactsContract.Contacts.InterfaceConsts.DisplayName };
           
var cursor = ManagedQuery (uri, projection, null, null, null);

if (cursor.MoveToFirst ()) {
    Console.WriteLine(
        cursor.GetString (cursor.GetColumnIndex (projection [0])));
}
```


## <a name="navigating-to-the-people-app"></a>Passare all'app di persone

Infine, per passare al profilo all'utente la nuova app di persone che viene fornito con Android 4, creare semplicemente un intento con un `ActionView` azione e un `ContactsContract.Profile.ContentUri`e passarlo al `StartActivity` metodo simile al seguente:

```csharp
var intent = new Intent (Intent.ActionView,
    ContactsContract.Profile.ContentUri);           
StartActivity (intent);
```

Quando si esegue il codice sopra riportato, l'app agli utenti verrà caricato nel profilo utente, come illustrato nella schermata seguente:

[![Schermata di persone app la visualizzazione del profilo utente John Doe](user-profile-images/15-people-app.png)](user-profile-images/15-people-app.png#lightbox)

Utilizzo con il profilo utente è ora simile all'interazione con altri dati in Android e offre un ulteriore livello di personalizzazione di dispositivo.



## <a name="related-links"></a>Collegamenti correlati

- [ContactsProviderDemo (esempio)](https://developer.xamarin.com/samples/monodroid/ContactsProviderDemo/)
- [Introduzione a Sandwich gelato](http://www.android.com/about/ice-cream-sandwich/)
- [Piattaforma 4.0 Android](http://developer.android.com/sdk/android-4.0.html)
