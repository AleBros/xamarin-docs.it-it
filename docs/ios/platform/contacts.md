---
title: Contatti e ContactsUI in Novell. iOS
description: Questo articolo illustra l'uso dei nuovi framework dell'interfaccia utente contatti e contatti in un'app Novell. iOS. Questi framework sostituiscono la rubrica esistente e l'interfaccia utente di Rubrica usata nelle versioni precedenti di iOS.
ms.prod: xamarin
ms.assetid: 7b6fb66a-5e19-4a5a-9ed2-f6b02af099af
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/20/2017
ms.openlocfilehash: 438ed93bafa37496e6a97ea2fe98ca6a515682cf
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032562"
---
# <a name="contacts-and-contactsui-in-xamarinios"></a>Contatti e ContactsUI in Novell. iOS

_Questo articolo illustra l'uso dei nuovi framework dell'interfaccia utente contatti e contatti in un'app Novell. iOS. Questi framework sostituiscono la rubrica esistente e l'interfaccia utente di Rubrica usata nelle versioni precedenti di iOS._

Con l'introduzione di iOS 9, Apple ha rilasciato due nuovi Framework, `Contacts` e `ContactsUI`, che sostituiscono i Framework dell'interfaccia utente della Rubrica e della rubrica esistente usati da iOS 8 e versioni precedenti.

I due nuovi Framework contengono le funzionalità seguenti:

- [**Contatti**](#contacts) : fornisce l'accesso ai dati dell'elenco dei contatti dell'utente.
  Poiché la maggior parte delle app richiede solo l'accesso in sola lettura, questo Framework è stato ottimizzato per l'accesso thread-safe di sola lettura.

- [**ContactsUI**](#contactsui) : fornisce gli elementi dell'interfaccia utente di Novell. iOS per visualizzare, modificare, selezionare e creare contatti nei dispositivi iOS.

[![](contacts-images/add01.png "An example Contact Sheet on an iOS device")](contacts-images/add01.png#lightbox)

> [!IMPORTANT]
> I Framework `AddressBook` e `AddressBookUI` esistenti usati da iOS 8 (e versioni precedenti) sono stati deprecati in iOS 9 e devono essere sostituiti con i nuovi framework di `Contacts` e `ContactsUI` il prima possibile per qualsiasi app Novell. iOS esistente. Le nuove app devono essere scritte in base ai nuovi Framework.

Nelle sezioni seguenti verranno esaminati questi nuovi Framework e come implementarli in un'app Novell. iOS.

<a name="contacts" />

## <a name="the-contacts-framework"></a>Framework contatti

Il Framework Contacts fornisce a Novell. iOS l'accesso alle informazioni di contatto dell'utente. Poiché la maggior parte delle app richiede solo l'accesso in sola lettura, questo Framework è stato ottimizzato per l'accesso thread-safe di sola lettura.

<a name="Contact_Objects" />

### <a name="contact-objects"></a>Oggetti contatto

La classe `CNContact` fornisce accesso thread-safe, in sola lettura alle proprietà di un contatto, ad esempio nome, indirizzo o numeri di telefono. `CNContact` funzioni come una `NSDictionary` e contiene più raccolte di proprietà di sola lettura (ad esempio, indirizzi o numeri di telefono):

[![](contacts-images/contactobjects.png "Contact Object overview")](contacts-images/contactobjects.png#lightbox)

Per tutte le proprietà che possono avere più valori, ad esempio indirizzo di posta elettronica o numeri di telefono, verranno rappresentati come una matrice di oggetti `NSLabeledValue`. `NSLabeledValue` è una tupla thread-safe costituita da un set di sola lettura di etichette e valori in cui l'etichetta definisce il valore per l'utente (ad esempio, posta elettronica domestica o aziendale). Il Framework Contacts fornisce una selezione di etichette predefinite (tramite il `CNLabelKey` e `CNLabelPhoneNumberKey` classi statiche) che è possibile usare nell'app oppure è possibile definire etichette personalizzate per le proprie esigenze.

Per tutte le app Novell. iOS che devono modificare i valori di un contatto esistente (o crearne di nuovi), usare la versione `NSMutableContact` della classe e le relative sottoclassi, ad esempio `CNMutablePostalAddress`.

Il codice seguente, ad esempio, creerà un nuovo contatto e lo aggiungerà alla raccolta di contatti dell'utente:

```csharp
// Create a new Mutable Contact (read/write)
var contact = new CNMutableContact();

// Set standard properties
contact.GivenName = "John";
contact.FamilyName = "Appleseed";

// Add email addresses
var homeEmail = new CNLabeledValue<NSString>(CNLabelKey.Home, new NSString("john.appleseed@mac.com"));
var workEmail = new CNLabeledValue<NSString>(CNLabelKey.Work, new NSString("john.appleseed@apple.com"));
contact.EmailAddresses = new CNLabeledValue<NSString>[] { homeEmail, workEmail };

// Add phone numbers
var cellPhone = new CNLabeledValue<CNPhoneNumber>(CNLabelPhoneNumberKey.iPhone, new CNPhoneNumber("713-555-1212"));
var workPhone = new CNLabeledValue<CNPhoneNumber>("Work", new CNPhoneNumber("408-555-1212"));
contact.PhoneNumbers = new CNLabeledValue<CNPhoneNumber>[] { cellPhone, workPhone };

// Add work address
var workAddress = new CNMutablePostalAddress()
{
    Street = "1 Infinite Loop",
    City = "Cupertino",
    State = "CA",
    PostalCode = "95014"
};
contact.PostalAddresses = new CNLabeledValue<CNPostalAddress>[] { new CNLabeledValue<CNPostalAddress>(CNLabelKey.Work, workAddress) };

// Add birthday
var birthday = new NSDateComponents()
{
    Day = 1,
    Month = 4,
    Year = 1984
};
contact.Birthday = birthday;

// Save new contact
var store = new CNContactStore();
var saveRequest = new CNSaveRequest();
saveRequest.AddContact(contact, store.DefaultContainerIdentifier);

// Attempt to save changes
NSError error;
if (store.ExecuteSaveRequest(saveRequest, out error))
{
    Console.WriteLine("New contact saved");
}
else
{
    Console.WriteLine("Save error: {0}", error);
}
```

Se questo codice viene eseguito in un dispositivo iOS 9, verrà aggiunto un nuovo contatto alla raccolta dell'utente. Esempio:

[![](contacts-images/add01.png "A new contact added to the user's collection")](contacts-images/add01.png#lightbox)

### <a name="contact-formatting-and-localization"></a>Formattazione e localizzazione di contatti

Il Framework Contacts contiene diversi oggetti e metodi che consentono di formattare e localizzare il contenuto per la visualizzazione all'utente. Il codice seguente, ad esempio, formatta correttamente un nome di contatto e un indirizzo postale per la visualizzazione:

```csharp
Console.WriteLine(CNContactFormatter.GetStringFrom(contact, CNContactFormatterStyle.FullName));
Console.WriteLine(CNPostalAddressFormatter.GetStringFrom(workAddress, CNPostalAddressFormatterStyle.MailingAddress));
```

Per le etichette delle proprietà che verranno visualizzate nell'interfaccia utente dell'app, il Framework di contatto dispone anche di metodi per localizzare tali stringhe. Anche in questo caso, si basa sulle impostazioni locali correnti del dispositivo iOS in cui viene eseguita l'app. Esempio:

```csharp
// Localized properties
Console.WriteLine(CNContact.LocalizeProperty(CNContactOptions.Nickname));
Console.WriteLine(CNLabeledValue<NSString>.LocalizeLabel(CNLabelKey.Home));
```

### <a name="fetching-existing-contacts"></a>Recupero di contatti esistenti

Utilizzando un'istanza della classe `CNContactStore`, è possibile recuperare le informazioni di contatto dal database dei contatti dell'utente. Il `CNContactStore` contiene tutti i metodi necessari per recuperare o aggiornare i contatti e i gruppi dal database. Poiché questi metodi sono sincroni, è consigliabile eseguirli in un thread in background per evitare di bloccare l'interfaccia utente.

Utilizzando i predicati (compilati dalla classe `CNContact`), è possibile filtrare i risultati restituiti durante il recupero dei contatti dal database. Per recuperare solo i contatti che contengono la stringa `Appleseed`, usare il codice seguente:

```csharp
// Create predicate to locate requested contact
var predicate = CNContact.GetPredicateForContacts("Appleseed");
```

> [!IMPORTANT]
> I predicati generici e composti non sono supportati dal framework dei contatti.

Ad esempio, per limitare il recupero solo alle proprietà **DATENAME** e **familyName** del contatto, usare il codice seguente:

```csharp
// Define fields to be searched
var fetchKeys = new NSString[] {CNContactKey.GivenName, CNContactKey.FamilyName};
```

Infine, per eseguire una ricerca nel database e restituire i risultati, usare il codice seguente:

```csharp
// Grab matching contacts
var store = new CNContactStore();
NSError error;
var contacts = store.GetUnifiedContacts(predicate, fetchKeys, out error);
```

Se questo codice è stato eseguito dopo l'esempio creato nella sezione **oggetto contacts** precedente, restituirebbe il contatto "John Appleseed" appena creato.

### <a name="contact-access-privacy"></a>Privacy accesso contatto

Poiché gli utenti finali possono concedere o negare l'accesso alle informazioni di contatto per ogni singola applicazione, la prima volta che si effettua una chiamata al `CNContactStore`, viene visualizzata una finestra di dialogo in cui viene chiesto di consentire l'accesso per l'app.

La richiesta di autorizzazione verrà presentata una sola volta, la prima volta che l'app viene eseguita e le esecuzioni successive o le chiamate all'`CNContactStore` utilizzeranno l'autorizzazione selezionata dall'utente in quel momento.

È consigliabile progettare l'applicazione in modo che gestisca normalmente l'utente che nega l'accesso al database di contatto.

#### <a name="fetching-partial-contacts"></a>Recupero di contatti parziali

Un _contatto parziale_ è un contatto che solo alcune delle proprietà disponibili sono state recuperate dall'archivio contatti per. Se si tenta di accedere a una proprietà che non è stata recuperata in precedenza, viene generata un'eccezione.

È possibile verificare facilmente se un determinato contatto dispone della proprietà desiderata usando il `IsKeyAvailable` o `AreKeysAvailable` metodi dell'istanza di `CNContact`. Esempio:

```csharp
// Does the contact contain the requested key?
if (!contact.IsKeyAvailable(CNContactOption.PostalAddresses)) {
    // No, re-request to pull required info
    var fetchKeys = new NSString[] {CNContactKey.GivenName, CNContactKey.FamilyName, CNContactKey.PostalAddresses};
    var store = new CNContactStore();
    NSError error;
    contact = store.GetUnifiedContact(contact.Identifier, fetchKeys, out error);
}
```

> [!IMPORTANT]
> I metodi `GetUnifiedContact` e `GetUnifiedContacts` della classe `CNContactStore` restituiscono _solo_ un contatto parziale limitato alle proprietà richieste dalle chiavi di recupero fornite.

### <a name="unified-contacts"></a>Contatti unificati

Un utente potrebbe avere diverse fonti di informazioni di contatto per una singola persona nel database di contatto, ad esempio iCloud, Facebook o Google mail. Nelle app iOS e OS X le informazioni di contatto verranno automaticamente collegate tra loro e visualizzate all'utente come singolo _contatto unificato_:

[![](contacts-images/unified01.png "Unified Contacts overview")](contacts-images/unified01.png#lightbox)

Questo contatto unificato è una visualizzazione temporanea, in memoria, delle informazioni di contatto dei collegamenti a cui viene assegnato il proprio identificatore univoco (che deve essere usato per recuperare il contatto, se necessario). Per impostazione predefinita, il Framework dei contatti restituirà un contatto unificato quando possibile.

### <a name="creating-and-updating-contacts"></a>Creazione e aggiornamento di contatti

Come è stato illustrato nella sezione [Contact Objects](#Contact_Objects) , è possibile usare un `CNContactStore` e un'istanza di un `CNMutableContact` per creare nuovi contatti che vengono quindi scritti nel database Contact dell'utente usando un `CNSaveRequest`:

```csharp
// Create a new Mutable Contact (read/write)
var contact = new CNMutableContact();

// Set standard properties
contact.GivenName = "John";
contact.FamilyName = "Appleseed";

// Save new contact
var store = new CNContactStore();
var saveRequest = new CNSaveRequest();
saveRequest.AddContact(contact, store.DefaultContainerIdentifier);

NSError error;
if (store.ExecuteSaveRequest(saveRequest, out error)) {
    Console.WriteLine("New contact saved");
} else {
    Console.WriteLine("Save error: {0}", error);
}
```

Un `CNSaveRequest` può essere usato anche per memorizzare nella cache più modifiche di contatto e di gruppo in un'unica operazione e per eseguire il batch di tali modifiche al `CNContactStore`.

Per aggiornare un contatto non modificabile ottenuto da un'operazione di recupero, è necessario innanzitutto richiedere una copia modificabile che viene quindi modificata e salvata nuovamente nell'archivio contatti. Esempio:

```csharp
// Get mutable copy of contact
var mutable = contact.MutableCopy() as CNMutableContact;
var newEmail = new CNLabeledValue<NSString>(CNLabelKey.Home, new NSString("john.appleseed@xamarin.com"));

// Append new email
var emails = new NSObject[mutable.EmailAddresses.Length+1];
mutable.EmailAddresses.CopyTo(emails,0);
emails[mutable.EmailAddresses.Length+1] = newEmail;
mutable.EmailAddresses = emails;

// Update contact
var store = new CNContactStore();
var saveRequest = new CNSaveRequest();
saveRequest.UpdateContact(mutable);

NSError error;
if (store.ExecuteSaveRequest(saveRequest, out error)) {
    Console.WriteLine("Contact updated.");
} else {
    Console.WriteLine("Update error: {0}", error);
}
```

### <a name="contact-change-notifications"></a>Notifiche di modifica contatto

Ogni volta che viene modificato un contatto, l'archivio contatti invia un `CNContactStoreDidChangeNotification` al centro notifiche predefinito. Se sono stati memorizzati nella cache o sono attualmente presenti contatti, è necessario aggiornare tali oggetti dall'archivio contatti (`CNContactStore`).

### <a name="containers-and-groups"></a>Contenitori e gruppi

I contatti di un utente possono esistere localmente sul dispositivo dell'utente o come contatti sincronizzati con il dispositivo da uno o più account del server (ad esempio Facebook o Google). Ogni pool di contatti dispone di un proprio _contenitore_ e un determinato contatto può esistere solo in un contenitore.

[![](contacts-images/containers01.png "Containers and Groups overview")](contacts-images/containers01.png#lightbox)

Alcuni contenitori consentono la disposizione dei contatti in uno o più _gruppi_ o _sottogruppi_. Questo comportamento dipende dall'archivio di backup per un contenitore specificato. Ad esempio, iCloud ha un solo contenitore ma può avere molti gruppi, ma non sottogruppi. Microsoft Exchange, invece, non supporta i gruppi, ma può avere più contenitori (uno per ogni cartella di Exchange).

[![](contacts-images/containers02.png "Overlap within Containers and Groups")](contacts-images/containers02.png#lightbox)

<a name="contactsui" />

## <a name="the-contactsui-framework"></a>Framework ContactsUI

Per le situazioni in cui l'applicazione non deve presentare un'interfaccia utente personalizzata, è possibile usare il Framework ContactsUI per presentare gli elementi dell'interfaccia utente per visualizzare, modificare, selezionare e creare contatti nell'app Novell. iOS.

Usando i controlli predefiniti di Apple, non solo si riduce la quantità di codice che è necessario creare per supportare i contatti nell'app Novell. iOS, ma si presenta un'interfaccia coerente agli utenti dell'app.

### <a name="the-contact-picker-view-controller"></a>Controller di visualizzazione selezione contatti

Il controller di visualizzazione selezione contatti (`CNContactPickerViewController`) gestisce la visualizzazione selezione contatti standard che consente all'utente di selezionare un contatto o una proprietà di contatto dal database di contatto dell'utente. L'utente può selezionare uno o più contatti (in base al relativo utilizzo) e il controller di visualizzazione selezione contatti non richiede l'autorizzazione prima di visualizzare la selezione.

Prima di chiamare la classe `CNContactPickerViewController`, è necessario definire le proprietà che l'utente può selezionare e definire predicati per controllare la visualizzazione e la selezione delle proprietà dei contatti.

Utilizzare un'istanza della classe che eredita da `CNContactPickerDelegate` per rispondere all'interazione dell'utente con la selezione. Esempio:

```csharp
using System;
using System.Linq;
using UIKit;
using Foundation;
using Contacts;
using ContactsUI;

namespace iOS9Contacts
{
    public class ContactPickerDelegate: CNContactPickerDelegate
    {
        #region Constructors
        public ContactPickerDelegate ()
        {
        }

        public ContactPickerDelegate (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ContactPickerDidCancel (CNContactPickerViewController picker)
        {
            Console.WriteLine ("User canceled picker");

        }

        public override void DidSelectContact (CNContactPickerViewController picker, CNContact contact)
        {
            Console.WriteLine ("Selected: {0}", contact);
        }

        public override void DidSelectContactProperty (CNContactPickerViewController picker, CNContactProperty contactProperty)
        {
            Console.WriteLine ("Selected Property: {0}", contactProperty);
        }
        #endregion
    }
}
```

Per consentire all'utente di selezionare un indirizzo di posta elettronica dai contatti nel database, è possibile usare il codice seguente:

```csharp
// Create a new picker
var picker = new CNContactPickerViewController();

// Select property to pick
picker.DisplayedPropertyKeys = new NSString[] {CNContactKey.EmailAddresses};
picker.PredicateForEnablingContact = NSPredicate.FromFormat("emailAddresses.@count > 0");
picker.PredicateForSelectionOfContact = NSPredicate.FromFormat("emailAddresses.@count == 1");

// Respond to selection
picker.Delegate = new ContactPickerDelegate();

// Display picker
PresentViewController(picker,true,null);
```

### <a name="the-contact-view-controller"></a>Controller di visualizzazione contatto

La classe Contact View Controller (`CNContactViewController`) fornisce un controller per presentare una visualizzazione contatto standard all'utente finale. La visualizzazione contatto può visualizzare nuovi contatti nuovi, sconosciuti o esistenti ed è necessario specificare il tipo prima che la visualizzazione venga visualizzata chiamando il costruttore statico corretto (`FromNewContact`, `FromUnknownContact`, `FromContact`). Di seguito è riportato un esempio:

```csharp
// Create a new contact view
var view = CNContactViewController.FromContact(contact);

// Display the view
PresentViewController(view, true, null);
```

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato in dettaglio come usare i Framework di contatto e contatto dell'interfaccia utente in un'applicazione Novell. iOS. In primo luogo, sono stati illustrati i diversi tipi di oggetti offerti dal framework del contatto e il modo in cui vengono usati per creare nuovi contatti esistenti o accedervi. È stato inoltre esaminato il Framework Contact UI per selezionare i contatti esistenti e visualizzare le informazioni di contatto.

## <a name="related-links"></a>Collegamenti correlati

- [Esempio di contatti](https://docs.microsoft.com/samples/xamarin/ios-samples/contacts/)
- [Novità di iOS 9](https://developer.apple.com/library/content/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Informazioni di riferimento sul Framework contatti](https://developer.apple.com/documentation/contacts?language=objc)
- [Guida di riferimento a ContactsUI Framework](https://developer.apple.com/documentation/contactsui?language=objc)
