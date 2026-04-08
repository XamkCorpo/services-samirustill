# Vaihe 3: Service-kerros, Repository, Result Pattern ja API-dokumentaatio — Teoriakysymykset

Olin väsynyt kun vastasin kysymyksiin ja kun etsin netistä tietoa niin ei ollut oikein motivaatiota tarkistaa kuinka tosi jotkut asiat olivat.


Vastaa alla oleviin kysymyksiin omin sanoin. Kirjoita vastauksesi kysymysten alle.

> **Vinkki:** Jos jokin kysymys tuntuu vaikealta, palaa lukemaan teoriamateriaalit:
> - [Service-kerros ja DI](https://github.com/xamk-mire/Xamk-wiki/blob/main/C%23/fin/04-Advanced/WebAPI/Services-and-DI.md)
> - [Repository Pattern](https://github.com/xamk-mire/Xamk-wiki/blob/main/C%23/fin/04-Advanced/Patterns/Repository-Pattern.md)
> - [Result Pattern](https://github.com/xamk-mire/Xamk-wiki/blob/main/C%23/fin/04-Advanced/Patterns/Result-Pattern.md)

---

## Osa 1: Service-kerros

### Kysymys 1: Fat Controller -ongelma

Miksi on ongelma jos controller sisältää kaiken logiikan (tietokantakyselyt, muunnokset, validoinnin)? Anna vähintään kaksi konkreettista haittaa.

**Vastaus:**
Koodi toistuu usein, controlleristä tulee vaikeampi lukee koska sinne saattaa pääytä satoja riviä koodia, testaaminen vaikeutuu koska logiikka sitoutuu HTTP kerrokseen.

---

### Kysymys 2: Vastuunjako

Miten vastuut jakautuvat controller:n, service:n ja repository:n välillä tässä harjoituksessa? Kirjoita lyhyt kuvaus kunkin kerroksen tehtävästä.

**Controller vastaa:**
Mitä HTTP-pyynnöllä haluttiin

**Service vastaa:**
Mitä sovellus tekee

**Repository vastaa:**
Miten data haetaan


---

### Kysymys 3: DTO-muunnokset servicessä

Miksi DTO ↔ Entity -muunnokset kuuluvat serviceen eikä controlleriin? Mitä hyötyä siitä on, että controller ei tunne `Product`-entiteettiä lainkaan?

**Vastaus:**
Jos muistan niin tunnilla selitettiin että käytetään serviceä käsittelemään dataa että controlleri ei "tiedä liikaa".
Hyöty on että controlleri ei ole sidottuna entiteettiin jos vaikka tietokanta, relaatio tai validointi muuttuu. 

---

## Osa 2: Interface ja Dependency Injection

### Kysymys 4: Interface vs. konkreettinen luokka

Miksi controller injektoi `IProductService`-interfacen eikä suoraan `ProductService`-luokkaa? Mitä hyötyä tästä on?

**Vastaus:**
Että ei olisi sidottuna konkreettiseen luokkaan. Pystyt määrittämään instanssin elinkaaren. 

---

### Kysymys 5: DI-elinkaaret

Selitä ero näiden kolmen elinkaaren välillä ja anna esimerkki milloin kutakin käytetään:

- **AddScoped
- Kestää yhden HTTP-pynnön
  
- **AddSingleton:**
- Kestää sovelluksen koko elinkaaren
  
- **AddTransient:**
- Luodaan uusi joka kerta

Miksi `AddScoped` on oikea valinta `ProductService`:lle?
Luulen, että se on resurrsien säästämistä varten koska scoped kestää yhden HTTP pyynnön.

---

### Kysymys 6: DI-kontti

Selitä omin sanoin mitä DI-kontti tekee kun HTTP-pyyntö saapuu ja `ProductsController` tarvitsee `IProductService`:ä. Mitä tapahtuu vaihe vaiheelta?

**Vastaus:**
DI kontti tarkistaa riippuvuudet konstruktorista, etsii rekisteröinnin esim IProductService -> ProductService ja rakentaa riippuvuuspuun. 
DI-kontti katsoo konstruktorit -> instansoi olioita -> hallitsee elinkaaren

---

### Kysymys 7: Rekisteröinnin unohtaminen

Mitä tapahtuu jos unohdat rekisteröidä `IProductService`:n `Program.cs`:ssä? Milloin virhe ilmenee ja miltä se näyttää?

**Vastaus:**
ASP.NET core ei osaisi automaattisesti:
Luoda AppDbContext-instanssin per pyyntö
Injektoida sen ProductService-konstruktoriin
Injektoida ProductService:n ProductsController-konstruktoriin.

Virhe mielestäni ilmeneisi kun serviceä kutsuttaisiin, mutta en tiedä miltä se näyttäisi mutta varmaan jonkunlainen error numero.
---

## Osa 3: Repository-kerros

### Kysymys 8: Miksi repository?

`ProductService` käytti aluksi `AppDbContext`:ia suoraan. Miksi se refaktoroitiin käyttämään `IProductRepository`:a? Anna vähintään kaksi syytä.

**Vastaus:**
Jotta tehtäisiin siitä abstraktisempi interfacen taakse mikä tekee koodista testattavaa, ei riipu suoraan ef coresta eikä koodi toistu.

---

### Kysymys 9: Service vs. Repository

Mikä on `IProductService`:n ja `IProductRepository`:n välinen ero? Mitä tietotyyppejä kumpikin käsittelee (DTO vai Entity)?

**IProductService:**
Toimii controllerin kanssa, hoitaa validoinnit, säännöt jne. Käsittelee molempia.

**IProductRepository:**
Toimii tietokannan kanssa, ei sisällä liiketoimintalogiikkaa ja vastaa datan tallennuksesta/hausta. Käsittelee entiteettejä.

---

### Kysymys 10: Controllerin muuttumattomuus

Kun Vaihe 7:ssä lisättiin repository-kerros, `ProductsController` ei muuttunut lainkaan. Miksi? Mitä tämä kertoo rajapintojen (interface) hyödystä?

**Vastaus:**


---

## Osa 4: Exception-käsittely ja lokitus

### Kysymys 11: ILogger

Mikä on `ILogger` ja miksi sitä tarvitaan? Mistä lokit näkee kehitysympäristössä?

**Vastaus:**


---

### Kysymys 12: Odotetut vs. odottamattomat virheet

Selitä ero "odotetun" ja "odottamattoman" virheen välillä. Anna esimerkki kummastakin ja kerro miten ne käsitellään eri tavalla servicessä.

**Odotettu virhe (esimerkki + käsittely):**

**Odottamaton virhe (esimerkki + käsittely):**


---

## Osa 5: Result Pattern

### Kysymys 13: Miksi null ja bool eivät riitä?

Alla on kaksi esimerkkiä. Selitä miksi ensimmäinen tapa on ongelmallinen ja miten toinen ratkaisee ongelman:

```csharp
// Tapa 1: null
ProductResponse? product = await _service.GetByIdAsync(id);
if (product == null)
    return NotFound();

// Tapa 2: Result
Result<ProductResponse> result = await _service.GetByIdAsync(id);
if (result.IsFailure)
    return NotFound(new { error = result.Error });
```

**Vastaus:**


---

### Kysymys 14: Result.Success vs. Result.Failure

Miten `Result Pattern` muutti virheiden käsittelyä servicessä? Vertaa Vaihe 8:n `throw;`-tapaa Vaihe 9:n `Result.Failure`-tapaan: mitä eroa niillä on asiakkaan (API:n kutsuja) näkökulmasta?

**Vastaus:**


---

## Osa 6: API-dokumentaatio

### Kysymys 15: IActionResult vs. ActionResult\<T\>

Miksi `ActionResult<ProductResponse>` on parempi kuin `IActionResult`? Anna vähintään kaksi syytä.

**Vastaus:**


---

### Kysymys 16: ProducesResponseType

Mitä `[ProducesResponseType]`-attribuutti tekee? Miten se näkyy Swagger UI:ssa?

**Vastaus:**


---

### Kysymys 18: Refaktorointi

Sovelluksen toiminnallisuus pysyi täysin samana koko harjoituksen ajan — samat endpointit, samat vastaukset. Mitä refaktorointi tarkoittaa ja miksi se kannattaa, vaikka käyttäjä ei huomaa eroa?

**Vastaus:**


---
