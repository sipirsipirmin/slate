---
search: true

toc_footers:
 - <a href='/en/'>Click for English version </a>
---
# <a href='#paytrek' style='color: white; text-decoration: none; color:#7ccfaf' > Giriş</a>

Paytrek PCI DSS Seviye 1, en yüksek seviye kart sahibi bilgi güvenliği,
sertifikası ile 100'den fazla ülkede yasal kurulum gereksinimleri ücretsiz,
kullanımı ve entegrasyonu kolay bir ödeme sistemi hizmeti sunmaktadır.

# Ödeme Akışı

Hızlı ve kolay entegrasyon için aşağıdaki ödeme akışları önerilmektedir.

Paytrek ödeme sistemi kullanabilmeniz için öncelikle onaylanmış bir üye
işyeri hesabına sahip olmanız gerekir.
API isteğinde bulunabilmek için kullanıcı adı ve parolanız bulunmuyor
ise lütfen [https://sandbox.paytrek.com/sandbox/signup](https://sandbox.paytrek.com/sandbox/signup) adresine
giderek kullanıcınızı oluşturun. Paytrek entegrasyonunuzu
tamamladıktan sonra takımımız sizinle canlı ortam
bilgileri paylaşılacaktır.

Paytrek aşağıda belirtilen ödeme yöntemlerine sahiptir.

+ Paytrek Ortak Ödeme Formu
+ Direkt Ödeme
+ Kart Saklama ile Ödeme
+ Ön Otorizasyon ile Ödeme

## Paytrek Ortak Ödeme Formu

1. Müşteri üye iş yerinin web sitesinde bir sipariş verir.
2. Üye işyeri Paytrek'e [Sale](#satis-sale) Kaynağını kullanarak token isteğinde bulunur.
3. Tarafımızdan üye işyerine `sale_token` bilgisi cevap olarak verilir.
4. Üye işyeri müşterisini yukarıdaki token aracılığıyla ödeme sayfasına yönlendirir. [1]
5. Müşteri ortak ödeme sayfasında ödeme için gereken bilgileri doldurur.
6. Paytrek ödeme işlemini gerçekleştirir.
7. Paytrek müşterinin girdiği bilgiler doğrultusunda ödemeyi gerçekleştirdiği istemciden
    bilgileri alır.
8. İşlem gerçekleştikten sonra muşteri ilgili üye işyerinin sayfasına `sale_token` ile yönlendirilir. [2]


<sub> * [1, 2] Sandbox ortak ödeme formu
    [https://sandbox.paytrek.com](https://sandbox.paytrek.com), Production ortak ödeme sayfası ise
    [https://secure.paytrek.com](https://secure.paytrek.com) da yer alır.
    Üye işyeri yönlendirme yaparken bu URL'lerin sonuna `sale_token` bilgisi
    ekleyerek ilgili ortamlarda bu sayfalara yönlendirme yapması gerekmektedir.
    (ie. [https://sandbox.paytrek.com/?token=TOKEN_VALUE](https://sandbox.paytrek.com/?token=TOKEN_VALUE))</sub>

<sub> * [1, 2] Ortak ödeme sayfasında müşteri
    işlemi gerçekleştirdikten sonra Paytrek tarafından üye iş yeri sayfasına yönlendirilir
    Bu esnada üye işyeri Sale Kaynağını kullanarak satışın durumunu sorgulamalıdır.
    Satışın durumu  **Paid**, **Ready to Send**,
    **Authorized** veya **PreAuthorized** ise bu satışın başarılı bir şekilde gerçekleştiğini
    gösterir.</sub>

## Direkt Ödeme

1. Müşteri üye işyerine ait ödeme sayfasına yönlenir.
2. Müşteri kart bilgilerini üye işyerinin ödeme sayfasında paylaşır.
3. Üye işyeri [Direct Charge](#direkt-odeme-2) Kaynağını kullanarak
   müşteriden aldığı bilgilerle ödeme işlemi için Paytrek'e istekte bulunur.
4. Paytrek üye işyerinden gelen isteği karşılar.
5. Paytrek ödemeyi gerçekleştirmek üzere ilgili istemciye istekte bulunur.
6. Ödeme işleminin sonucu üye işyerine cevap olarak dönülür.

## Kart Saklama ile Ödeme

1. Müşteri üye işyerine ait ödeme sayfasına yönlenir.
2. Müşteri kart bilgilerini üye işyerinin ödeme sayfasında paylaşır
   ve üye iş yeri PaytrekJS aracılığıyla `Create Card Token` kullanarak,
   kart bilgilerini tokenize eder.
3. Üye iş yeri bu kart token'ı kaydettikten sonra [Sale](#satis) Kaynağını kullanarak
   satış oluşturur.
4. Üye işyeri ilgili `card_token` ve `sale_token` kullanarak [Charge with Card Token](#kart-saklama-ile-odeme-2) Kaynağına
   istekte bulunur.
5. Paytrek üye işyerinden gelen isteği karşılar.
6. Paytrek ödemeyi gerçekleştirmek üzere ilgili istemciye istekte bulunur.
7. Ödeme işleminin sonucu üye işyerine cevap olarak dönülür.

## Ön Otorizasyon ile Ödeme

1. Müşteri üye işyerine ait ödeme sayfasına yönlenir.
2. Müşteri kart bilgilerini üye işyerinin ödeme sayfasında paylaşır
   ve üye işyeri [Direct Charge](#direkt-odeme-2) Kaynağına
   `pre_auth` parametresini `true` olacak şekilde gönderir (`pre_auth:true`).
3. Üye iş yeri ```sale_token``` kullanarak [Capture](#capture) Kaynağına
   istekte bulunur.
4. Paytrek üye işyerinden gelen isteği karşılar.
5. Paytrek ödemeyi gerçekleştirmek üzere ilgili istemciye istekte bulunur.
6. Ödeme işleminin sonucu üye işyerine cevap olarak dönülür.



# 3D Transactions
Üye işyeri ve/veya müşteri ödeme işleminin MasterCard veya Visa
tarafından sağlanan 3D güvenli ödeme işlemi olarak yapılmasını talep
edebilir. Bu bağlamda Paytrek 3D ödeme işlemini tüm ödeme akışlarına
desteklemektedir.

Üye işyeri 3D güvenli ödeme işlemi gerçekleştirmek için bulunduğu isteklerin içerisine ```secure_option``` parametresini ```true``` olarak eklemelidir (```secure_option:true```).

## Paytrek Ortak Ödeme ile 3D Güvenli Ödeme İşlemi

1. Müşteri üye işyerine ait ödeme sayfasına yönlenir.
2. Üye işyeri [Sale](#satis) Kaynağını kullanarak satış oluşturur.
3. Paytrek tarafından üye işyerine `sale_token` bilgili cevap olarak dönülür.
4. Üye işyeri `sale_token` bilgisi kullanarak müşterisini ortak ödeme sayfasına
   yönlendirir. [1]
5. Müşteri ortak ödeme sayfasında kart bilgilerini paylaşır.
6. Paytrek tarafında 3D güvenli ödeme işlemi başlatılır ve müşteri karta ait
   istemcinin 3D güvenli ödeme sayfasına yönlendirilir.
7. Müşteri 3D ödeme işlemini gerçekleştirmek üzere PIN girişi yapar.
8. Müşteri 3D ödeme sayfasından Paytrek sayfasına yönlendirilir.
9. Paytrek ödeme işlemini 3D güvenli ödeme işlemi sonucuna göre işler. Bu bağlamda
   Müşteri 3D işlemini başarılı bir şekilde tamamlayabilir ya da satışı 3D aşamasında
   bırakıp satışı tamamlamamayı tercih edebilir. İki durumda da paytrek tarafından üye
   işyerine işlem bilgili cevap olarak dönülür.
10. Müşteri üye işyerinin sayfasına `sale_token` bilgisi ile yönlendirilir. [2]

<sub> * [1] Sandbox ortak ödeme formu
    [https://sandbox.paytrek.com](https://sandbox.paytrek.com), Production ortak ödeme sayfası ise
    [https://secure.paytrek.com](https://secure.paytrek.com) da yer alır.
    Üye işyeri yönlendirme yaparken bu URL'lerin sonuna `sale_token` bilgisi
    ekleyerek ilgili ortamlarda bu sayfalara yönlendirme yapması gerekmektedir.
    (ie. [https://sandbox.paytrek.com/?token=TOKEN_VALUE](https://sandbox.paytrek.com/?token=TOKEN_VALUE))</sub>

<sub> * [2] Ortak ödeme sayfasında müşteri
    işlemi gerçekleştirdikten sonra Paytrek tarafından üye iş yeri sayfasına yönlendirilir
    Bu esnada üye işyeri Sale Kaynağını kullanarak satışın durumunu sorgulamalıdır.
    Satışın durumu  **Paid**, **Ready to Send**,
    **Authorized** veya **PreAuthorized** ise bu satışın başarılı bir şekilde gerçekleştiğini
    gösterir.</sub>

## Direkt Ödeme ile 3D Güvenli Ödeme İşlemi

1. Müşteri üye işyerine ait ödeme sayfasına yönlenir.
2. Müşteri kart bilgilerini üye işyerinin ödeme sayfasında paylaşır.
3. Üye işyeri [Direct Charge](#direkt-odeme-2) Kaynağını kullanarak
   müşteriden aldığı bilgilerle ödeme işlemi için Paytrek'e istekte bulunur.
4. Paytrek bu isteğe cevap olarak müşterinin 3D güvenli bilgilerini gireceği
   `forward_url` bilgisini üye işyeri ile paylaşır.
5. Üye işyeri müşterisini ilgi `forward_url` e yönlendirir.
6. Müşteri bu forward url aracılığıyla 3D güvenli ödeme sayfasına yönlenir.
7. Müşteri 3D güvenli ödemeyi gerçekleştirmek için PIN bilgilerini girer.
8. 3D güvenli ödeme sayfasının başarılı bir şekilde tamamlanması sonucunda
    müşteri Paytrek'e yönlendirilir.
9. Paytrek ödeme işlemini 3D güvenli ödeme işlemi sonucuna göre işler. Bu bağlamda
   Müşteri 3D işlemini başarılı bir şekilde tamamlayabilir ya da satışı 3D aşamasında
   bırakıp satışı tamamlamamayı tercih edebilir. İki durumda da paytrek tarafından üye
   işyerine işlem bilgili cevap olarak dönülür.
10. Müşteri üye işyerinin sayfasına yönlendirilir. [1]


<sub> * [1] Paytrek üye işyeri tarafından Sale Kaynağına istekte bulunurken,
    paylaşmış olduğu `return_url` adresine müşteriyi yönlendirir. İlgili istek
    içerisinde `return_url` bulunmuyor ise müşteri, üye işyerinin kullanıcı bilgileri
    oluşturulurken vermiş olduğu url bilgisine yönlendirilir.</sub>


## Kart Saklama ile 3D Güvenli Ödeme İşlemi

1. Müşteri üye işyerine ait ödeme sayfasına yönlenir.
2. Müşteri kart bilgilerini üye işyerinin ödeme sayfasında paylaşır
   ve üye iş yeri PaytrekJS aracılığıyla `Create Card Token` kullanarak,
   kart bilgilerini tokenize eder.
3. Üye iş yeri bu kart token'ı kaydettikten sonra [Sale](#satis) Kaynağını kullanarak
   satış oluşturur.
4. Üye işyeri ilgili `card_token` ve `sale_token` kullanarak [Charge with Card Token](#kart-saklama-ile-odeme-2) Kaynağına
   istekte bulunur.
5. Paytrek bu isteğe cevap olarak müşterinin 3D güvenli bilgilerini gireceği
   `forward_url` bilgisini üye işyeri ile paylaşır.
6. Üye işyeri müşterisini ilgi `forward_url` e yönlendirir.
7. Müşteri bu forward url aracılığıyla 3D güvenli ödeme sayfasına yönlenir.
8. Müşteri 3D güvenli ödemeyi gerçekleştirmek için PIN bilgilerini girer.
9. 3D güvenli ödeme sayfasının başarılı bir şekilde tamamlanması sonucunda
    müşteri Paytrek'e yönlendirilir.
10. Paytrek ödeme işlemini 3D güvenli ödeme işlemi sonucuna göre işler. Bu bağlamda
    Müşteri 3D işlemini başarılı bir şekilde tamamlayabilir ya da satışı 3D aşamasında
    bırakıp satışı tamamlamamayı tercih edebilir. İki durumda da paytrek tarafından üye
    işyerine işlem bilgili cevap olarak dönülür.
11. Müşteri üye işyerinin sayfasına yönlendirilir*.

<sub> * Paytrek üye işyeri tarafından Sale Kaynağına istekte bulunurken,
    paylaşmış olduğu `return_url` adresine müşteriyi yönlendirir. İlgili istek
    içerisinde `return_url` bulunmuyor ise müşteri, üye işyerinin kullanıcı bilgileri
    oluşturulurken vermiş olduğu url bilgisine yönlendirilir.</sub>

# Paytrek API

Paytrek gateway API RESTful mimarisi esas alınarak en iyi endütriyel standartlara
göre dizayn edilmiştir.

## Kimlik Doğrulama

Paytrek tarafına iletilen tüm istekler basit kimlik doğrulama yöntemi
kullanılarak gönderilmelidir [Basic Auth](https://en.wikipedia.org/wiki/Basic_access_authentication).

API isteği yapabilmek için bir kullanıcı adı(api_key) ve parola(secret) bilgisine sahip
değilseniz lütfen [https://sandbox.paytrek.com/sandbox/signup](https://sandbox.paytrek.com/sandbox/signup) adresinden kayıt olunuz.

Kullanıcı adı(api_key) ve parola(secret) bilgileriniz [Hesap Bilgileri](https://sandbox.paytrek.com/dashboard/account_info/)
sayfasında yer almaktadır.

## Headers

Tüm isteklerin headerları ```Content-Type: application/json``` içermelidir.

## Endpoints


| Resource      | Endpoint      | Allowed HTTP Methods |
| ------------- |:-------------| :-----:|
| [`Sale`](#satis)      | ```/api/v2/sale/``` | ```POST``` ```GET```|
| [`Charge`](#odeme)      | ```/api/v2/charge/``` | ```POST``` |
| [`Direct Charge`](#direkt-odeme-2)| ```/api/v2/direct_charge/``` | ```POST``` |
| [`Vault`](#vault) | ```/api/v2/vault/``` | ```POST``` ```GET``` |
| [`Charge With Token`](#kart-saklama-ile-odeme-2) | ```/api/v2/charge_with_token/``` | ```POST``` |
| [`Capture`](#capture) | ```/api/v2/capture/``` | ```POST``` |
| [`Cancel`](#iptal) | ```/api/v2/cancel/``` | ```POST``` |
| [`Refund`](#iade) | ```/api/v2/refund/``` | ```POST``` |
| [`Installments`](#taksitler) | ```/api/v2/installments/``` | ```GET``` |


## Paytrek Ortamlarının URL'leri

|||
|:-|:-|
|Sandbox (Test Ortamı) | [https://sandbox.paytrek.com](https://sandbox.paytrek.com)|
|Canlı Ortam | [https://secure.paytrek.com.tr](https://secure.paytrek.com.tr)|


# PaytrekJS Kütüphanesi

PaytrekJS, Paytrek’e ait tokenization servisini müşteri tarafında bulunan
uygulamanıza entegre etmenize yardımcı olacak küçük bir kütüphanedir.
Bu bölümde bu kütüphaneyi inceleyeceğiz.


## PaytrekJS Entegrasyonu

PaytrekJS, form gönderimlerinizi veya etkinlik işleyicilerinizi etkilemez.
Formunuzun onsubmit işleyicisinde gerçekleştirmek istediğiniz her işlemi bir
kere çağırmanız yeterlidir.

## PaytrekJS'yi Sitenize Dahil Etmek
```html
<script type="text/javascript" src="https://secure.paytrek.com.tr/static/v2/js/paytrek.js"></script>
```

> Paytrek Sandbox (Test Ortamı için), lütfen aşağıdaki şekilde sitenize dahil edin:

```html
<script type="text/javascript" src="https://sandbox.paytrek.com/static/v2/js/paytrek.js"></script>
```

PaytrekJS'i kullanabilmek için web sayfanıza eklemeniz gerekmektedir.
Header veya footera ekleyebilirsiniz.

## PaytrekJS'yi Konfigüre Etme

PaytrekJS, ayarlamanız gereken bazı yapılandırma seçeneklerine sahiptir.
Seçenekler aşağıdaki gibi açıklanmıştır:

### PaytrekJS Ayarları

> Aşağıda, PaytrekJS yi nasıl yerleştirebileceğinizi gösteren bir kod örneği verilmiştir.

```javascript
Paytrek.Options = {
    "publishableKey"          : "myPaytrekPublishableKey",
    "host"                    : "https://secure.paytrek.com",
    "orderId"                 : "1420199350",
    "currency"                : "TRY",
    "amount"                  : "1299.00",
    "removeCardElements"      : false,
    "resetCardElementValues"  : false,
    "cardTokenField"       : "card_token"
}

var form = document.getElementById("payment_form");
form.addEventListener("submit", function(event) {
    // handle your form submission.
    // everything seems good so far let's get a card token
    var paytrekForm = new Paytrek.Form(event.target);
    paytrekForm.createToken(function(response) {
        if (response.hasOwnProperty("errors")) {
            // there's a validation error or there's a server side error
            // from Paytrek that you must handle.
            return false;
        }
        // we have the token, let's use it.
        console.log(response.card_token);
    });
});
```
| Değişken Adı| Açıklama |
|:-|:-|
|publishableKey| string Paytrek Dashboard üzerinden aldığınız publishable key bilgisi|
|host| string İstekte bulunacağınız paytrek istemci bilgileri. Canlı ortam için host bilgisi secure.paytrek.com.tr test ortamı içinse sandbox.paytrek.com host bilgisi olarak kullanılabilir.
|orderId| string Satış oluştururken kullanacağınız sipariş id bilgisi.Sipariş idsi her bir satış için tekil olmalıdır.|
|currency| string Para biriminin ISO kodu formatı (ie: TRY, USD, EUR)|
|amount| string Satışa ait tutar bilgisi. (ie: 23.99)|
|removeCardElements| true ya da false. Paytrek Form'un bir token aldıktan sonra kredi kartıyla ilgili tüm öğeleri formunuzdan kaldırması gerekip gerekmediği kararı.|
|resetCardElementValues| true or false. Paytrek Form'un bir kart belirteci aldıktan sonra kredi kartıyla ilgili tüm öğelerin değerini boş bir dizeye sıfırlayıp sıfırlamaması kararı.|
|cardTokenField| string Paytrekten alınacak payment tokenın yazılabileceği hidden input alanının idsi.|
|useVaultField| string Ödeme işlemi yapıldıktan sonra kartın kaydedilip kaydedilmeyeceğine karar verilen checkbox alanının idsi|

PaytrekJS, iki temel bileşenden oluşur:

## Paytrek.Form

Bu sınıf dosyası, ödeme formunuza bir JavaScript nesnesi eşlemesidir. Sınıfın özelliklerine göre
Kredi kartı numaranızı, cvv, son kullanma tarihi ve kart sahibinin adı alanlarını eşler.

### Yapı

```javascript
// creating a Paytrek.Form object with an id
var paytrekForm = new Paytrek.Form("myFormId");
```

```javascript
// creating a Paytrek.Form object with a form element
var paytrekForm = new Paytrek.Form(document.getElementById("myFormId"));
var paytrekForm = new Paytrek.Form(document.forms[0]);
```

Formunuzun id niteliğini veya form öğesinin kendisini sağlayarak Paytrek.Form sınıfını başlatabilirsiniz.
İsteğinize göre form objesini ve objenin ilişkilendiği satış bilgilerini konfigüre edebilirsiniz.


Form bulunamaz ise ilgili öğe tarafından hata fırlatılır.

### Sale Data Objesi

Sale Data objesi bir card token oluşturulurken satışa ait asgari
verinin tutulduğu objedir.
Bu verilerden bazıları card token oluşturulurken halihazırda bildiğiniz satışa
ait sipariş numarası, sipariş tutarı, para birimi gibi bilgilerdir.

Satış verileriyle gönderilecek tüm veriler string formatına dönüştürülmelidir.

### Kart Token Oluşturma

```javascript
paytrekForm.createToken(function(response){
    // response is an object returned by the xhr call.
    console.log(response.card_token);
});
```

Bir Paytrek.Form örneğiniz hazır olduğunda
Kart belirteci almak için yapmanız gereken
tek şey, `createToken ()` yöntemini çağırmaktır.

`createToken ()` yöntemi tek bir argüman alır:
Bu argüman Paytrek API tarafından callback fonksiyonu olarak
dönülen ilk cevaptır.



### Kart Token Cevabı

```javascript
paytrekForm.createToken(function(response){
    if (response.card_token === undefined) {
        // handle failure case
    } else {
        // handle success case
    }
});
```

Paytrek Tokenization API'nin döndürdüğü HTTP yanıtı her zaman
JSON formatında olur.
`Card_token` veya `error` anahtarlarına  sahip olacaktır.

### Form Elementleri
> Form Örneği;

```html
<form id="checkout">
   <input id="payment_token" name="payment_token" value="" />
   <div>
     <label for="number">Kredi Kartı Numarası:</label>
     <input id="number" data-paytrek-field="number" />
   </div>
   <div>
     <label for="chname">Kart Sahibi İsmi:</label>
     <input id="chname" data-paytrek-field="card-holder-name" />
   </div>
   <div>
     <label for="expmonth">Son Kullanım Ayı:</label>
     <select id="expmonth" data-paytrek-field="expiration-month">
       <option>1..12</option>
     </select>
   </div>
   <div>
     <label for="expyear">Son Kullanım Yılı:</label>
     <select id="expyear" data-paytrek-field="expiration-year">
       <option>2014..2020</option>
     </select>
   </div>
</form>
```

Yeni bir Paytrek.Form nesnesi oluşturduktan sonra,
form öğenizin altındaki bir özelliğe sahip tüm düğümlere
“data-paytrek-field” denir.

Yan panelde data-paytrek-field niteliğine sahip alanlara sahip form örneği yer almaktadır.

## Paytrek.CreditCard

```javascript
var creditCard = new Paytrek.CreditCard(
    number,
    cvc,
    expMonth,
    expYear,
    cardHolderName
);
```

Bu sınıf, kredi kartı ile ilgilenen bir JavaScript sınıfıdır.
API isteği yapmadan önce kart verilerinin doğrulaması bu sınıf aracılığıyla yapılır.

### Yapı

Paytrek.CreditCard’ın ilgili öğesi, aşağıdaki sırayla 5 argüman alır:
Kredi kartı numarası, cvc, son kullanma tarihi, son kullanma tarihi, kart sahibinin adı.


### Validasyonlar

```javascript
if (creditCard.isValid()) {
    // credit card is valid
} else {
   // credit card is invalid
   console.log(creditCard.errors);
}
```

Sahip olduğunuz kredi kartı örneğini doğrulayabilirsiniz.
Paytrek.CreditCard sınıfı Kredi kartı numarası, cvc, son
kullanma tarihi ve kart sahibinin adı bilgilerinin geçerliliğini
sorgular.

Aşağıdakiler Paytrek.CreditCard'ın sağladığı yöntemlerin listesidir.

| Yöntem Adı| Açıklama |
|:-|:-|
| isCvcValid() | CVC nin geçerliliğini sorgular.|
| isCardNumberValid() | Kredi Kartı numarasının nin geçerliliğini sorgular.|
| isdExpirationDateValid() | Son Kullanım tarihinin geçerliliğini sorgular.|
| isCardHolderNameValid() | Kart sahibinin adının geçerliliğini sorgular.|

Yukarıdaki tüm yöntemleri çağırmak zorunda değilsiniz.
Bir kredi kartı nesnesini doğrulamak için sadece isValid () yöntemini
çağırmanız yeterlidir.

## Ödeme Seçenekleri

PaytrekJS, her satış için kart tipi ücretlendirmelerini görebileceğiniz
bir başka özelliğe daha sahiptir. Bu özelliğin kullanımı için
Paytrek hesap yöneticinizle irtibata geçiniz.

### Kart Tipi Ücretlendirmeleri

> Kart tipi ücretlendirmeleri için örnek kullanım;

```javascript
paytrekForm.getPaymentOptions(function(response) {
    console.log(response);
    /*
    {"options":
      {"TRY": {
        "tx_fee": "0.96",
        "grand_total": "101.96",
        "total": "101.00",
        "tx_fee_rate": "0.95"}
      }
    }*/
});
```

Paytrek, PaytrekJs aracılığıyla müşterilerinizin kart türü başına ücretlerini
ayarlamanıza ayrıca ödeme şeklinizdeki masrafları anında görmenize ve güncellemenize
izin verir.

## Paytrek.Checkout

Paytrek.Checkout widget ile Web sitenize gömülü ödeme
formunu inanılmaz derecede kolay şekilde entegre edebilirsiniz.

Bu widget müşteriyi verilen `return_url` e yönlendirecektir.
Bu `return_url` e müşteri ödeme işlemi gerçekleştirdikten sonra `sale_token` ile
yönlendirilecektir,
Bu adımdan sonra `sale_token` kullanılarak [Sale Resource](#satis) üzerinden
üye işyeri satışın son durumunu sorgulayabilir.

Satışın durumu  **Paid**, **Ready to Send**,
    **Authorized** veya **PreAuthorized** ise bu satışın başarılı bir şekilde gerçekleştiğini
    gösterir.</sub>

### Yapı

```javascript
function Checkout(saleData, options) {
    // Paytrek.Checkout implementation
}
```

Paytrek.Checkout öğesi `saleData` ve `options` olmak üzere
iki parametre ile çalışmaktadır.Bu iki parametre de JavaScript Hash olarak
tutulur. Sale Data içerisinde yer alan keyler Sale Kaynağına gönderilecek
argümanları oluşturur.

### Constructor Options

> Modal Form

```javascript
// your order form
var form = document.forms['order-form'];
form.addEventListener('click', function(event){
    // your form handling logic
    // ...
    // everything is fine, display the checkout widget
    new Paytrek.Checkout({
        amount: "10.20",
        currency: "TRY",
        customer_first_name: "John",
        customer_last_name: "Doe",
        customer_email: "johndoe@gmail.com",
        billing_state: "CA",
        billing_city: "San Francisco",
        billing_country: "US",
    }).displayForm();
});
```

> Gömülü Form

```javascript
var saleData = {
  amount: "10.20",
  currency: "TRY",
  customer_first_name: "John",
  customer_last_name: "Doe",
  customer_email: "johndoe@gmail.com",
  billing_state: "CA",
  billing_city: "San Francisco",
  billing_country: "US",
};

var options = {
  openModal: false,
  widgetParent: document.getElementById('widget-parent'),
  errorCb: function(errorMessage) {
      console.log(errorMessage);
  },
  beforeCb: function(event) {
      // form validation is handled here
      // if you want to stop paytrek widget to charge
      // you should do event.preventDefault()
  },
  afterCb: function(event) {
      // post-charge callback
  }
};

new Paytrek.Checkout(saleData, options).displayForm();
```

Paytrek.Checkout ikinci parametre olarak options parametresini alır. Bu parametre
aşağıdaki tabloda gösterilen değişken isimlerini içerebilir.

| Özellik Adı | Açıklama |
|:-|:-|
|openModal | Sayfanıza yerleştirilmiş formu görüntülemek veya kalıcı bir kutu olarak açmak isteyip istemediğinizi belirler. Varsayılan değer `true`.|
|widgetParent | Modal kutusunu görüntülemek istemiyorsanız, gömülü formu görüntülemek istediğiniz üst DOM Öğesi.|
|beforeCb | Ödeme işleminden önce çalıştırmak istediğiniz `callback` metodu.|
|afterCb | Ödeme işleminden önce sonra istediğiniz `callback` metodu.|
|errorCb | Ödeme işlemi sırasında herhangi bir hata oluştuğunda çalıştırılacak `callback` metodu.|

# Test Kartları

Kullanabileceğiniz test kartları listesi verilmiştir.

| Card Number        | Bank           | Expiration  | Cvc| 3D Pass
| ------------- |:-------------| :-----:|:---:|:---:|
| 4508034508034509     | İşbank | 12/20 |000| |
| 4506347043358536      | YKB      |  08/19 |000| |
| 4508034508034509 | ING      |   12/18 |000| |
| 4355084355084358 | Akbank | 12/18 |000| a |
| 5456165456165454 | Finansbank | 12/18 |000| a |

# Hata Kodları

Paytrek özelinde tüm bankalara ait hata kodlarının eşleştirildiği bir hata kodu
tablosu bulunmaktadır. Ödeme işlemi esnasında banka tarafından alınacak bir hata
Paytrek tablosunda karşılığı bulunarak `error_code` ve `error_message` olarak
cevap içerisinde üye işyerine dönülür.

Tablo Aşağıda Verilmiştir

|Kod| Üye İşyeri Mesajı  |  Müşteri Mesajı  |
|:-:|:-|:-|
|30001|Invalid Business Member|An unexpected error has occurred, please contact the seller.|
|10004|Transaction has been denied / General Rejection|Transaction has been denied, please contact your bank|
|10006|ID Control / Honor With Id|Transaction has been denied, please contact your bank.|
|30003|Invalid Amount|An unexpected error has occurred, please contact the seller.|
|10009|Invalid Account / Card Number|Your transaction has been terminated, please check your card information.|
|10020|Insufficient Limit|Transaction has been denied, please check your card limit.|
|10023|Card has Expired|Transaction has been denied, please check your card information.|
|10026|The transaction has not been cleared to the cardholder|Transaction has been denied, please contact your bank.|
|10029|Security Breach|Transaction has been denied, please contact your bank.|
|30009|Incompatible data received|An unexpected error has occurred, please contact the seller.|
|30011|Encryption Error|An unexpected error has occurred, please contact the seller.|
|10033|Wrong / Invalid Cvv|Transaction has been denied, please check your card information.|
|30013|Authentication Error|An unexpected error has occurred, please contact the seller.|
|10038|Rejection - Invalid Card|The transaction has been denied, please proceed with another card.|
|30015|Double Transaction Transfer|-|
|30016|Daily Count Error/ Cancellation has been rejected|-|
|20015|System Error|An unexpected error has occurred, please contact the seller.|
|20017|Connection Error|An unexpected error has occurred, please contact the seller.|
|30033|Incorrect Extra Point Parameter|An unexpected error has occurred, please contact the seller.|
|30037|Invalid Installment|An unexpected error has occurred, please contact the seller.|
|30041|Invalid Pos|An unexpected error has occurred, please contact the seller.|
|30050|End of the Day Transactions must be Completed|An unexpected error has occurred, please contact the seller.|
|30053|Invalid Currency|An unexpected error has occurred, please contact the seller.|
|30090|Invalid Repeating Payment Duration|-|
|10046|The Request can be Fraud, Ip of cardholder did not clear the check|Transaction has been denied, please contact your bank.|
|30119|Invalid Order Number.|We cannot complete your transaction at the moment, please contact the the seller.|
|30120|Order Number is in Use|We cannot complete your transaction at the moment, please contact the the seller.|
|10049|Maximum Retry Count Exceeded|Maximum retry count exceeded, Please try again later.|
|99999|Card Declined|Card Declined|
|10050|FR-Rejected|An unexpected error has occurred, please contact the seller.|
|40000|Unable to Authenticate|We cannot proceed with your transaction, unable to verify cardholder.|
|40004|Cardholder not Participating|We cannot proceed with your transaction, unable to perform 3D Secure Authentication.|
|40005|Directory Server not Responding|We cannot proceed with your transaction, unable to perform 3D Secure Authentication.|
|40006|Authentication Failure|We cannot proceed with your transaction, unable to perform 3D Secure Authentication.|
|40007|3D Secure System Error|We cannot proceed with your transaction, unable to perform 3D Secure Authentication.|
|10001|Please call your bank for authorization|Transaction has been denied, please contact your bank.|
|10003|Block The Card|Transaction has been denied, please contact your bank.|
|30002|Invalid Transaction|An unexpected error has occurred, please contact the seller.|
|10010|Unidentified Issuer Bank|Your transaction has been terminated, please check your card information.|
|30005|Cancellation could not be completed / The original could not be found|-|
|10016|Too Many Pin Entry Attempts, Block the Card|Transaction has been denied, please contact your bank.|
|10032|Invalid Date|Transaction has been denied, please check your card information.|
|30118|The Request is not included in the Group's Allowed Day/Hour Setting|We cannot complete your transaction at the moment, please contact the the seller.|
|30128|Vpos Lock Control Failure|We cannot complete your transaction at the moment, please contact the the seller.|
|UNKNOWN_ERROR|Unknown Error|An unexpected error has occurred, please try again later|
|9000|Preauth Transaction Cannot Void|Preauth Transaction Cannot Void|
|9001|3D Fields Do Not Match|3D Fields Do Not Match|
|40002|Cardholder or Issuer Bank not Enrolled in 3D Secure System|We cannot proceed with your transaction, unable to perform 3D Secure Authentication.|

# Paytrek Koleksiyon Cüzdanı

Paytrek Cüzdan, maaşlarınız için kazanç, komisyon veya bağlılık vb. Ödemeler yapmak için kullanılan ödeme platformudur.
Platform, ödemeleri tamamlamak için iki ana bileşenden oluşmaktadır
ve ödeme akışı. Bunlar [Wallet API](https://paytrekwallet.docs.apiary.io/#introduction/paytrek-wallet-api) and [Payee Dashboard](https://paytrekwallet.docs.apiary.io/#introduction/payee-dashboard).

# <a href='#reference' style='color: white; text-decoration: none; color:#7ccfaf' > Reference </a>

# Satış

Satış Kaynağı, satışla ilgili bilgileri içeren bir satış nesnesi oluşturmak için kullanılır.
Ödeme işlemi gerçekleşmeden önce ödemesi yapılacak ilgili nesne bu kaynak aracılığıyla oluşturulur.
Bu kaynak ödeme yapılmadan önce işlemin tanımlanmasını sağlar.
Bir ödeme Ön Otorizasyonlu olarak gerçekleştirilecek ise satış parametrelerinden (`pre_auth`  ```true```)
olarak gönderilmelidir, ardından satış `capture` kaynağı kullanılarak `capture` edilir.

Oluşturulan bir satış nesnesine ait bilgileri almak için, ```sale_token```
Sale Kaynağına ` GET` HTTP metodu ile gönderilmelidir.

Bu isteğin cevabında işlem bilgileri ve durumu ile ilgili satış detaylarını döndürür.

(eg. `/api/v2/sale/f25356bab177416ba7b518cc1bd8a596/`)

İstekte bulunmak için aşağıdaki linkten gerekli parametreleri yeni bir sekme açarak bulabilir
e aynı zamanda anahtar konsolunu kullanarak sekmeler arasında da istekte bulunabilirsiniz.

## Satış Oluşturma

> Request Headers

```json
"Content-Type": "application/json"
"Authorization": "Basic aEtHWE53SFExRk5IOld2OXI5QmdxSnFocQ=="
```

> Request Body

```json
{
  "amount": 24.0,
  "order_id": "1467034250",
  "secure_option": false,
  "pre_auth": false,
  "billing_address": "123 Market St. San Francisco",
  "billing_city": "San Francisco",
  "billing_country": "US",
  "billing_state": "CA",
  "currency": "TRY",
  "customer_email": "johndoe@gmail.com",
  "customer_first_name": "John",
  "customer_ip_address": "212.57.9.204",
  "customer_last_name": "Doe",
  "installment": 1,
  "items": [{
    "unit_price": 12.0,
    "quantity": 2,
    "name": "product_name",
    "photo": "https://sandbox.paytrek.com/statics/images/testing.jpg"
  }],
  "sale_data": {"merchant_name": "Ted"}
}
```

> Response Code

```string
201
```
> Response headers

```json
"Content-Type": "application/json"
```

> Response Body

```json
{
    "request_id": null,
    "sys_time": 1521014319,
    "language_code": "en-us",
    "transactions": [],
    "currency": "TRY",
    "order_id": "1467034250",
    "amount": "24.00",
    "installment": 1,
    "sale_token": "c626f49193114e24ad0980752b749318",
    "expires_at": "2018-03-14T08:58:39.519205Z",
    "secure_option": true,
    "half_secure": null,
    "pre_auth": false,
    "return_url": null,
    "customer_first_name": "John",
    "customer_last_name": "Doe",
    "customer_email": "johndoe@gmail.com",
    "customer_ip_address": "212.57.9.204",
    "billing_city": "San Francisco",
    "billing_country": "US",
    "billing_zipcode": null,
    "billing_state": "CA",
    "billing_phone": null,
    "sale_data": {
        "merchant_name": "Ted"
    },
    "items": [
        {
            "name": "product_name",
            "photo": "https://sandbox.paytrek.com/statics/images/testing.jpg",
            "quantity": 2,
            "unit_price": "12.00"
        }
    ],
    "created_at": "2018-03-14T07:58:39.519707Z",
    "callback_url": null,
    "refunded_amount": "0.00",
    "status": "Created",
    "financial_status": null,
    "description": null,
    "is_locked": false,
    "hosted_payment": true,
    "billing_address": "123 Market St. San Francisco",
    "billing_company": null
}
```

| İstek Türü | Kaynak(Endpoint) |
|:-:|:-:|
| POST | https://sandbox.paytrek.com/api/v2/sale/ |

Yan panelde örnek bir satış oluşturma isteği ve aşağıdaki tabloda bu istek
içerisinde gönderebileceğiniz yada göndermeniz gereken parametrelerin
listesi verilmiştir.

|Üye İşyeri Mesajı  | Tip |  Müşteri Mesajı  |
|:-|:--:|:-|
|currency | string, zorunlu | 3 harfli para birimi ISO kodu.|
|order_id | string, zorunlu | Siparişin Idsi.|
|amount  | number, zorunlu | Sipariş Tutarı.|
|customer_first_name | string, zorunlu | Müşteri adı.|
|customer_last_name | string, zorunlu | Müşteri soyadı.|
|customer_email | string, zorunlu | Müşteri email adresi.|
|customer_ip_address | string, zorunlu | Müşteri ip adresi.  |
|billing_address | string, zorunlu | Müşteri fatura adresi.|
|billing_city | string, zorunlu | Müşteri fatura şehri.|
|billing_state | string | Müşterinin faturalandırma durumu, ABD ve CA için posta kısaltmaları kullanın.|
|billing_country | string, zorunlu | Müşterinin fatura ülkesi, 2 harfli ISO kodu.|
|billing_zipcode | string, opsiyonel | Müşterinin fatura zip kodu.|
|billing_phone | string, opsiyonel | Müşterinin fatura telfon numarası.|
|items | object, zorunlu | Siparişe ait ürünlerin listesi.|
|return_url | string | Ödeme tamamlandığında müşterinin yönlendirileceği sayfanın URL'i.|
|hosted_payment | boolean | Ortak ödeme sayfası kullanım tercihi.|
|hosted_payment_url | string | Ortak ödeme sayfası URL'i.|
|installment | number, zorunlu | Taksit bilgisi.|
|secure_option | boolean | Ödemenin 3D li ya da 3Dsiz geçme kararı.(Varsayılan: false) |
|half_secure | boolean | 3D kayıt kontrolünün (mdstatus) 2, 3, 4 ile sonuçlanması durumunda satışın devam etme kararı.|
|pre_auth | boolean | Satışın Ön otorizasyonlu olup olmayacağı kararı. (Default: false)|
|sale_data | object | Satışa ait ekstra bilgi alanı.|

## Satışı İnceleme

> Request Headers

```json
"Content-Type": "application/json"
"Authorization": "Basic aEtHWE53SFExRk5IOld2OXI5QmdxSnFocQ=="
```

> Response Code

```string
200
```

> Response headers

```json
"Content-Type": "application/json"
```

> Response Body

```json
{
    "request_id": null,
    "sys_time": 1521014392,
    "language_code": "en-us",
    "transactions": [],
    "currency": "TRY",
    "order_id": "1467034250",
    "amount": "24.00",
    "installment": 1,
    "sale_token": "c626f49193114e24ad0980752b749318",
    "expires_at": "2018-03-14T08:58:39.519205Z",
    "secure_option": true,
    "half_secure": null,
    "pre_auth": false,
    "return_url": null,
    "customer_first_name": "John",
    "customer_last_name": "Doe",
    "customer_email": "johndoe@gmail.com",
    "customer_ip_address": "212.57.9.204",
    "billing_city": "San Francisco",
    "billing_country": "US",
    "billing_zipcode": null,
    "billing_state": "CA",
    "billing_phone": null,
    "sale_data": {
        "merchant_name": "Ted"
    },
    "items": [
        {
            "name": "product_name",
            "photo": "https://sandbox.paytrek.com/statics/images/testing.jpg",
            "quantity": 2,
            "unit_price": "12.00"
        }
    ],
    "created_at": "2018-03-14T07:58:39.519707Z",
    "callback_url": null,
    "refunded_amount": "0.00",
    "status": "Created",
    "financial_status": null,
    "description": null,
    "is_locked": false,
    "hosted_payment": true,
    "billing_address": "123 Market St. San Francisco",
    "billing_company": null
}
```

| İstek Türü | Kaynak(Endpoint) |
|:-:|:-:|
| GET | https://sandbox.paytrek.com/api/v2/sale/**sale_token**/ |

# Ödeme

> Request Headers

```json
"Content-Type": "application/json"
"Authorization": "Basic aEtHWE53SFExRk5IOld2OXI5QmdxSnFocQ=="
```

> Request Body

```json
{
  "number": "4508034508034509",
  "expiration": "12/2020",
  "cvc":"000",
  "card_holder_name":"John Doe",
  "sale_token":"c626f49193114e24ad0980752b749318"
}
```

> Response Code

```string
200
```

> Response headers

```json
"Content-Type": "application/json"
```

> Response Body

```json
{
    "exp_month": "12",
    "number": "450803******4509",
    "sale_token": "c626f49193114e24ad0980752b749318",
    "exp_year": "2020",
    "card_holder_name": "J********",
    "succeeded": true,
    "error_message": "",
    "issuer_bank_id": "isbank",
    "secure_charge": false,
    "cvc": "***",
    "return_url": "?token=c626f49193114e24ad0980752b749318"
}
```

Charge Kaynağı kredi kartı ile ödeme yapabilmenize imkan sağlayan kaynaktır.
Ödeme işlemi yapmadan önce [satış oluşturma](#satis-olusturma) kaynağını kullanarak bir satış oluşturmanız gerekir.

Eğer ödeme işleminin ön otorizasyonlu olarak gerçekleşmesi için
`pre_auth` parametresi `true` olarak gönderilmelidir. Ardından ilgili satış 'capture'
edilir.

| İstek Türü | Kaynak(Endpoint) |
|:-:|:-:|
| POST | https://sandbox.paytrek.com/api/v2/charge/ |

Bir ödeme işlemi yapılırken kullanılan kartın tekrardan kullanımı için saklanmasını
istiyorsanız `save_card` parametresini `true` olarak belirlemeniz gerekir.
Bu parametre `true` olacak şekilde istek yapıldığında kart 'vault' edilerek saklanır
ve cevap içerisinde `card_token` parametresi dönülür.

Aşağıdaki tabloda bir ödeme işlemi için gönderilmesi gereken parametrelerin listesi verilmiştir.

| Parametre | Tip | Açıklama |
|:-|:-|:-|
|sale_token  | string, required | Sale kaynağı tarafından oluşturulan satışa ait token.|
|number  | number, required | Kredi kartı numarası.|
|expiration  | string, required | Kredi kartı son kullanım tarihi MM/YY formatında.|
|cvc  | number, required | Karta ait CVC numarası.|
|card_holder_name | Kart üzerindeki isim.|
|save_card  | boolean(Varsayılan: true) | Kart saklama parametresi.|

# Direkt Ödeme

> Request Headers

```json
"Content-Type": "application/json"
"Authorization": "Basic aEtHWE53SFExRk5IOld2OXI5QmdxSnFocQ=="
```

> Request Body

```json
{
  "amount": 24.0,
  "order_id": "1467034250",
  "secure_option": false,
  "pre_auth": false,
  "number": "4508034508034509",
  "expiration": "12/2020",
  "cvc":"000",
  "card_holder_name":"John Doe",
  "billing_address": "123 Market St. San Francisco",
  "billing_city": "San Francisco",
  "billing_country": "US",
  "billing_state": "CA",
  "currency": "TRY",
  "customer_email": "johndoe@gmail.com",
  "customer_first_name": "John",
  "customer_ip_address": "212.57.9.204",
  "customer_last_name": "Doe",
  "installment": 1,
  "items": [{
    "unit_price": 12.0,
    "quantity": 2,
    "name": "product_name",
    "photo": "https://sandbox.paytrek.com/statics/images/testing.jpg"
    }],
  "sale_data": {"merchant_name": "Ted"}
}
```

> Response Code

```string
200
```

> Response headers

```json
"Content-Type": "application/json"
```

> Response Body

```json
{
    "secure_option": true,
    "number": "450803******4509",
    "half_secure": null,
    "currency": "TRY",
    "customer_email": "johndoe@gmail.com",
    "language_code": "en-us",
    "billing_country": "US",
    "customer_ip_address": "212.57.9.204",
    "card_holder_name": "J********",
    "customer_last_name": "Doe",
    "billing_zipcode": null,
    "financial_status": "Ready to Send",
    "billing_phone": null,
    "issuer_bank_id": "isbank",
    "sale_data": {
        "merchant_name": "Ted"
    },
    "sys_time": 1521016061,
    "pre_auth": false,
    "billing_address": "123 Market St. San Francisco",
    "description": null,
    "transactions": [
        {
            "type": "Sale",
            "succeeded": true,
            "error_message": "",
            "ref_id": "",
            "amount": "24.00",
            "currency": "TRY",
            "is_secure": false,
            "secure_status": null,
            "created_at": "2018-03-14T08:27:40.467500Z",
            "bin_number": "450803",
            "card_number_last4": "4509",
            "card_type": "credit",
            "exchange_rate": null,
            "card_holder_name": "J********",
            "card_fee": "0.00",
            "card_fee_rate": "0.00",
            "fraud_risk": {
                "id": 46196,
                "request_id": null,
                "sys_time": 1521016061,
                "language_code": "en-us",
                "created_at": "2018-03-14T08:27:40.411031Z",
                "succeeded": true,
                "risk_score": "16.00",
                "decision": "ACCEPT",
                "reviewer_decision": null,
                "reviewer_comments": null,
                "bin_country": "TR",
                "bin_name": "visa",
                "ip_region": null,
                "ip_country": null,
                "ip_city": null,
                "ref_id": null,
                "error": null,
                "info_codes": null,
                "sale": 90810,
                "transaction": null
            },
            "paytrek_ref_id": "1mlHu6ljNGxC5CZY0nVs",
            "paytrek_error": null
        }
    ],
    "order_id": "1467034250",
    "succeeded": true,
    "expires_at": "2018-03-14T09:27:40.301574Z",
    "hosted_payment": false,
    "exp_month": "12",
    "sale_token": "92489820e4914f23b2db8039205cb64b",
    "exp_year": "2020",
    "secure_charge": false,
    "billing_city": "San Francisco",
    "installment": 1,
    "refunded_amount": "0.00",
    "is_locked": false,
    "items": [
        {
            "name": "product_name",
            "photo": "https://sandbox.paytrek.com/statics/images/testing.jpg",
            "quantity": 2,
            "unit_price": "12.00"
        }
    ],
    "created_at": "2018-03-14T08:27:40.302118Z",
    "error_message": "",
    "amount": "24.00",
    "status": "Paid",
    "return_url": "?token=92489820e4914f23b2db8039205cb64b",
    "billing_company": null,
    "request_id": null,
    "customer_first_name": "John",
    "callback_url": null,
    "cvc": "***",
    "billing_state": "CA"
}
```

Bu endpoint size herhangi bir sale isteğinde bulunmadan doğrudan kredi kartı
kullanarak ödeme gerçekleştirme imkanı sağlar.

Eğer ödeme işleminin ön otorizasyonlu olarak gerçekleşmesini istiyorsanız
pre_auth parametresi true olarak gönderilmelidir. Ardından ilgili satış capture
edilir.

Bir ödeme işlemi yapılırken kullanılan kartın tekrardan kullanımı için saklanmasını
istiyorsanız `save_card` parametresini `true` olarak belirlemeniz gerekir.
Bu parametre `true` olacak şekilde istek yapıldığında kart vault edilerek saklanır
ve cevap içerisinde `card_token` parametresi dönülür. [Charge with token](#satis).

| İstek Türü | Kaynak(Endpoint) |
|:-:|:-:|
| POST | https://sandbox.paytrek.com/api/v2/direct_charge/ |


| Parametre | Tip | Açıklama |
|:-|:-|:-|
| currency | string, required | Para biriminin 3 harfli ISO kodu.|
| order_id | string, required | Sipariş numarası.|
| amount |  number, required | Siparişe ait tutar bilgisi.|
| number | number, required | Kredi kartı numarası.|
| expiration | string, required | Kredi kartı son kullanma tarihi, MM/YY formatında.|
| cvc | number, required | Karta ait CVC numarası.|
| card_holder_name | string | Kart hamilinin adı.|
| installment | number, required | Taksit bilgisi.|
| secure_option | boolean | Ödemenin 3D güvenli ödeme işlemi olup olmayacağı kararı.(Varsayılan: false)|
| half_secure | boolean | 3D kayıt kontrolünün mdstatus) 2, 3, 4 ile sonuçlanması durumunda satışın devam etme kararı.|
| return_url | string, required | Ödeme işlemi gerçekleştikten sonra müşterinin yönlendirileceği URL|
| pre_auth | boolean | Ödemenin ön otorizasyonlu geçip geçmediği kontrolü(Varsayılan: false)|
| items | object, required | Siparişe ait öğelerin listesi.|
| customer_first_name | string, required | Müşterinin adı.|
| customer_last_name | string, required | Müşterinin soyadı.|
| customer_email | string, required | Müşteri emaili.|
| customer_ip_address | string, required | Müşteri Ip adresi.  |
| billing_address | string, required | Müşteri fatura adresi.|
| billing_city | string, required | Müşteri fatura şehri.|
| billing_state | string | Müşterinin faturalandırma durumu, ABD ve CA için posta kısaltmaları kullanın.|
| billing_country | string, zorunlu | Müşterinin fatura ülkesi, 2 harfli ISO kodu.|
| billing_zipcode | string, zorunlu | Müşterinin fatura zip kodu.|
| billing_phone | string, zorunlu | Müşterinin fatura telfon numarası.|
| sale_data | object | Satışa ait ekstra bilgi alanı|

# Kart Saklama

Paytrek, müşterinizin PAN verilerini Paytrek'in güvenli kasasında saklamanıza olanak tanır.
Bu endpointi kullanarak, Kredi kartı verilerini sadece müşterilerinizden yalnızca
bir kez almanız gerekecek ve istediğiniz zaman ödeme işlemi için aynı verileri kullanabileceksiniz.

+ Müşterilerin formunuza girdiği kredi kartı verilerini doğrulayın.
+ Kredi kartı verileriyle Vault endpointine POST isteği gönderin.
+ Paytrek size bir kart token cevap olarak döner.
+ Bu token ile "charge_with_token" endpointi ile ödemenizi gerçekleştirin.

İstekte bulunmak için aşağıdaki linkten gerekli parametreleri yeni bir sekme açarak bulabilir
e aynı zamanda anahtar konsolunu kullanarak sekmeler arasında da istekte bulunabilirsiniz.

## Kart Saklama İşlemi

> Request Headers

```json
"Content-Type": "application/json"
"Authorization": "Basic aEtHWE53SFExRk5IOld2OXI5QmdxSnFocQ=="
```

> Request Body

```json
{
    "card_holder_name": "Joe Doe",
    "number": "4508034508034509",
    "expiration": "12/2020",
    "cvc": "000",
    "card_label": "Ted"
}
```

> Response Code

```
200
```

> Response headers

```json
"Content-Type": "application/json"
```

> Response Body

```json
{
    "request_id": null,
    "sys_time": 1521015364,
    "language_code": "tr",
    "card_token": "2b54e2f7-58ad-40e3-9a71-f3bc9333c406",
    "created_at": "2018-03-14T08:16:04.001969Z",
    "bin_number": "450803",
    "last4": "4509",
    "card_brand": "visa",
    "card_type": "credit",
    "card_user_id": "b561169d7a2d467f885a7aedf7b10cf3",
    "card_label": "Ted",
    "card_issuer": "isbank",
    "card_country": "TR"
}
```

| İstek Türü | Kaynak(Endpoint)|
|:-:|:-:|
| POST | https://sandbox.paytrek.com/api/v2/vault/ |

| Parametre | Tip | Açıklama |
|:-|:-|:-|
| number | number, zorunlu | Kart numarası.|
| expiration | string, zorunlu | Kartın son kullanma tarihi, MM/YY formatında.|
| cvc | number, zorunlu | Kartın CVC numarası.|
| card_holder_name | string, zorunlu | Kart üzerindeki isim.|
| card_label | string |  Kart alan adı.|

## Saklanan Kartı Görüntüleme

> Request Headers

```json
"Content-Type": "application/json"
"Authorization": "Basic aEtHWE53SFExRk5IOld2OXI5QmdxSnFocQ=="
```

> Response Code

```
200
```

> Response Headers

```json
"Content-Type": "application/json"
```

> Response Body

```json
        {
            "request_id": null,
            "sys_time": 1521015364,
            "language_code": "tr",
            "card_token": "2b54e2f7-58ad-40e3-9a71-f3bc9333c406",
            "created_at": "2018-03-14T08:16:04.001969Z",
            "bin_number": "450803",
            "last4": "4509",
            "card_brand": "visa",
            "card_type": "credit",
            "card_user_id": "b561169d7a2d467f885a7aedf7b10cf3",
            "card_label": "Ted",
            "card_issuer": "isbank",
            "card_country": "TR"
        }
```

| İstek Türü | Kaynak(Endpoint)|
|:-:|:-:|
| GET | https://sandbox.paytrek.com/api/v2/vault/**card_token**/ |

| Parametre | Açıklama |
|:-|:-|
| card_token | Vault edilen karta ait kart token'ı |

## Saklanan Kartları Listeleme

> Request Headers

```json
"Content-Type": "application/json"
"Authorization": "Basic aEtHWE53SFExRk5IOld2OXI5QmdxSnFocQ=="
```

> Response Code

```
200
```

> Response Headers

```json
"Content-Type": "application/json"
```

> Response Body

```json
    {
        "count": 2,
        "next": null,
        "previous": null,
        "results": [
        {
            "request_id": null,
            "sys_time": 1540562575,
            "language_code": "en-us",
            "card_token": "aac3a45b-8ced-4e4c-8ebb-c88ceff272f6",
            "created_at": "2018-10-26T13:59:45.207410Z",
            "bin_number": "450803",
            "last4": "4509",
            "card_brand": "visa",
            "card_type": "credit",
            "card_user_id": "10001",
            "card_label": "",
            "card_issuer": "isbank",
            "card_country": "TR"
        },
        {
            "request_id": null,
            "sys_time": 1540562575,
            "language_code": "en-us",
            "card_token": "0f46ba98-d9d2-4fd3-93e4-6d23b4b48839",
            "created_at": "2018-10-26T13:59:46.445397Z",
            "bin_number": "450803",
            "last4": "4509",
            "card_brand": "visa",
            "card_type": "credit",
            "card_user_id": "10001",
            "card_label": "",
            "card_issuer": "isbank",
            "card_country": "TR"
        }
    ]
}
```

| İstek Türü | Kaynak(Endpoint)|
|:-:|:-:|
| GET | https://sandbox.paytrek.com/api/v2/vault/ |

|Parametre|Tip|Açıklama|
|:-:|:-|:-|
|card_user_id | opsiyonel | Kart kullanıcısın idsi (Vault oluştururken bu değer belirlenebilir)|
|card_label | opsiyonel | Kart etiketi|

## Saklanan Kartı Silme

> Request Headers

```json
"Content-Type": "application/json"
"Authorization": "Basic aEtHWE53SFExRk5IOld2OXI5QmdxSnFocQ=="
```

> Response Code

```
204
```
> Response Headers

```json
"Content-Type": "application/json"
```

| İstek Türü | Kaynak(Endpoint)|
|:-:|:-:|
| DELETE | https://sandbox.paytrek.com/api/v2/vault/**card_token**/ |

|Parametre|Tip|Açıklama|
|:-:|:-|:-|
|card_token | string | Vault işleminden sonra tarafınıza iletilen token |

# Kart Saklama ile Ödeme

> Request Headers

```json
"Content-Type": "application/json"
"Authorization": "Basic aEtHWE53SFExRk5IOld2OXI5QmdxSnFocQ=="
```

> Request Body

```json
{
  "sale_token":"5f20ca43d47244509949eb32c80e522a",
  "card_token":"914dcf0b-236b-44e9-9b31-3925af88cdc5"
}
```

> Response Code

```
200
```

> Response Headers

```json
"Content-Type": "application/json"
```

> Response Body

```json
{
    "exp_month": "12",
    "number": "450803******4509",
    "payment_product": null,
    "sale_token": "5f20ca43d47244509949eb32c80e522a",
    "exp_year": "2020",
    "sale_risk": null,
    "card_holder_name": "J********",
    "dcc_currency": null,
    "succeeded": true,
    "error_message": null,
    "issuer_bank_id": "isbank",
    "secure_charge": false,
    "cvc": "***",
    "return_url": "?token=5f20ca43d47244509949eb32c80e522a"
}
```

Bu endpoint müşterinin kart tokenı kullanarak ödeme gerçekleştirmesini sağlar.
Burada herhangi bir tutar ya da nicelik parametresi kullanılmaz. Yalnızca sale
endpointi ile oluşturulan sale e ait `sale_token` bilgisi ve vault edilen karta
ait `card_token` bilgisi ile ödeme gerçekleştirilir.

İstekte bulunmak için aşağıdaki linkten gerekli parametreleri yeni bir sekme açarak bulabilir
e aynı zamanda anahtar konsolunu kullanarak sekmeler arasında da istekte bulunabilirsiniz.

| İstek Türü | Kaynak(Endpoint)|
|:-:|:-:|
| POST | https://sandbox.paytrek.com/api/v2/charge_with_token/ |

|Parametre|Tip|Açıklama|
|:-:|:-|:-|
|sale_token | string, zorunlu | Sale endpointi ile oluşturulan salee ait token.|
|card_token | string, zorunlu | Vault endpointi ile vault edilen karta ait token.|

# Capture

> Request Headers

```json
"Content-Type": "application/json"
"Authorization": "Basic aEtHWE53SFExRk5IOld2OXI5QmdxSnFocQ=="
```

> Request Body

```json
{
  "sale_token": "b39887704acb480e81ab8611ae256719",
  "comments": "testing"
}
```

> Response Code

```
200
```

> Response Headers

```json
"Content-Type": "application/json"
```

> Response Body

```json
{
    "sale_token": "a16a810b503c4a91bb5c3d883617530a",
    "amount": "24.72",
    "sale_amount": "24.72",
    "sale_id": 97510,
    "succeeded": true
}
```

Bu endpoint ön otorizasyonda kalmış bir satışın capture edilmesini sağlar.
Bir satışı capture etmek için capture endpointine ilgili satışın tokenı ile POST isteği atmanız yeterlidir.
Ön otorizasyon durumunda olmayan bir satış capture edilemez.
Ön otorizasyonda bir satış oluşturmak için ilgili satışı oluştururken `pre_auth` parametresini `true` göndermeniz
gerekmektedir.
Bir satış fraud kontrolü sonucunda `REVIEW` durumunda ise bu satış ön otorizasyonda oluşturulur ve tarafınızdan
capture edilmesi gerekir.

Ön otorizasyonlu satışın akışı:

+ `pre_auth` parametresi `true` olacak şekilde direct_charge endpointine istekte bulunulur.
+ Bu işlemden sonra capture endpointine `sale_token` ile istekte bulunulur.

İstekte bulunmak için aşağıdaki linkten gerekli parametreleri yeni bir sekme açarak bulabilir
e aynı zamanda anahtar konsolunu kullanarak sekmeler arasında da istekte bulunabilirsiniz.

| İstek Türü | Kaynak(Endpoint)|
|:-:|:-:|
| POST | https://sandbox.paytrek.com/api/v2/capture/ |

|Parametre|Tip|Açıklama|
|:-:|:-|:-|
|sale_token | string, zorunlu | Sale endpointi ile oluşturulan sale'e ait token|
|comments | string, opsiyonel | Fraud kontrol sonucunun kabul edilmesine dair yorum.|

# İptal

> Request Headers

```json
"Content-Type": "application/json"
"Authorization": "Basic aEtHWE53SFExRk5IOld2OXI5QmdxSnFocQ=="
```

> Request Body

```json
{
  "sale_token": "b39887704acb480e81ab8611ae256719",
  "comments": "testing"
}

```

> Response Code

```
200
```

> Response Headers

```json
"Content-Type": "application/json"
```

> Response Body

```json
{
    "sale_token": "a16a810b503c4a91bb5c3d883617530a",
    "amount": 24,
    "sale_amount": 24.72,
    "succeeded": true
}
```

Cancel endpointi bir satışı iptal ya da iade etmek için kullanılır.
Cancel endpointine bir istekte bulunulduğunda satışın iptali ya da tam iadesi gerçekleşir.
Bir satışla ilgili parçalı iade yapmak için ```Refund``` endpointi kullanılmalıdır.
İki durumda da cevapta tutar bilgileri yer alacaktır.
Eğer bir satış fraud kararı sonucu `REVIEW` da ise ve bu satış iptal edilirse satışa ait `REJECTED` a çevrilir.

İstekte bulunmak için aşağıdaki linkten gerekli parametreleri yeni bir sekme açarak bulabilir
e aynı zamanda anahtar konsolunu kullanarak sekmeler arasında da istekte bulunabilirsiniz.

| İstek Türü | Kaynak(Endpoint)|
|:-:|:-:|
| POST | https://sandbox.paytrek.com/api/v2/cancel/ |

|Parametre|Tip|Açıklama|
|:-:|:-|:-|
|sale_token | string, zorunlu | Sale endpointi ile oluşturulan sale'e ait token.|
|comments | string, opsiyonel | Fraud kontrol sonucunun kabul edilmesine dair yorum.|

# İade

> Request Headers

```json
"Content-Type": "application/json"
"Authorization": "Basic aEtHWE53SFExRk5IOld2OXI5QmdxSnFocQ=="
```

> Request Body

```json
{
  "sale_token": "b39887704acb480e81ab8611ae256719",
  "amount": 9.70,
  "comments": "testing"
}

```

> Response Code

```
200
```

> Response Headers

```json
{
    "total_refunded_amount": 9.7,
    "succeeded": true,
    "sale_token": "b39887704acb480e81ab8611ae256719",
    "sale_amount": 50,
    "sale_id": 89890,
    "amount": 9.7
}
```

> Response Body

```json
{
    "sale_token": "a16a810b503c4a91bb5c3d883617530a",
    "amount": 24,
    "sale_amount": 24.72,
    "succeeded": true
}
```

İade endpointi bir satışın iadesini gerçekleştirmek için kullanılır.
Bir satışa ait tam iade isteğinde bulunduğunda; Paytrek tarafından öncelikle iptal işlemi gerçekleştirilir.
Cancel işlemi başarılı sonuçlanmaz ise, satışın iadesi gerçekleştirilmeye çalışılır.
Bir satışa ait parçalı iade yapılmak istendiğinde tutar parametresi gönderilmelidir.

İki durumda da cevapta tutar bilgileri yer alacaktır.
Eğer bir satış fraud kararı sonucu `REVIEW` da ise ve bu satış iptal edilirse satışa ait `REJECTED` a çevrilir.

İstekte bulunmak için aşağıdaki linkten gerekli parametreleri yeni bir sekme açarak bulabilir
e aynı zamanda anahtar konsolunu kullanarak sekmeler arasında da istekte bulunabilirsiniz.

| İstek Türü | Kaynak(Endpoint)|
|:-:|:-:|
| POST | https://sandbox.paytrek.com/api/v2/refund/ |

|Parametre|Tip|Açıklama|
|:-:|:-|:-|
|sale_token | string, zorunlu | Ödemesi yapılan sale'e ait `sale_token`|
|amount | number, opsiyonel | İade edilen tutar.|
|comments | string, opsiyonel | Fraud kontrol sonucunun kabul edilmesine dair yorum.|

# Ödeme Planı

Paytrek size tekrarlayan ödemeleriniz için ödeme planı oluşturma olanağı sağlar.
Bu endpointe istekte bulunmak için kullanılacak alanlar [`Direct Charge`](#direkt-odeme-2)
endpointi ile `recurring` harici aynıdır.

Yan paneldeki JSON bloğu yeni bir plan oluşturmak için gereken parametreleri göstermektedir.

```json
"recurring": {
    "name": "Test Store Recurring Payments",
    "recurring_period": "Monthly",
    "start_date": "2018-08-30",
    "recurring_frequency": 4
}
```

Eğer `start_date` parametresi belirlenmediyse **ilk ödeme** bugün gerçekleştirilir.

Ayrıca, abonelik planı oluşturulurken `save_card: true` ayarlanması zorunludur.

### Tekrarlayan Ödeme Periotları

 - Günlük
 - Haftalık
 - Aylık
 - Yıllık


İstekte bulunmak için aşağıdaki linkten gerekli parametreleri yeni bir sekme açarak bulabilir
e aynı zamanda anahtar konsolunu kullanarak sekmeler arasında da istekte bulunabilirsiniz.


## Ödeme Planı Oluşturma

> Request Headers

```json
"Content-Type": "application/json"
"Authorization": "Basic aEtHWE53SFExRk5IOld2OXI5QmdxSnFocQ=="
```

> Request Body

```json
{
    "secure_option": "false",
    "card_holder_name": "John Doe",
    "pre_auth": false,
    "billing_address": "123 Market St. San Francisco",
    "sale_data": {},
    "recurring" : {
        "name": "Test Store Recurring Payments",
        "recurring_period": "Monthly",
        "start_date": "2018-06-30",
        "recurring_frequency": 4
    },
    "save_card": true,
    "number": "4508034508034509",
    "cvc": "000",
    "customer_last_name": "Doe",
    "billing_state": "CA",
    "return_url": "https://www.google.com.tr/",
    "currency": "TRY",
    "amount": "100.00",
    "customer_first_name": "John",
    "billing_country": "US",
    "billing_zipcode": "34410",
    "billing_city": "San Francisco",
    "customer_ip_address": "212.57.9.204",
    "customer_email": "accept@paytrek.com",
    "installment": 1,
    "fraud_check_enabled": false,
    "expiration": "12/2020",
    "items": [],
    "order_id": "20180424141314",
    "half_secure": false
}

```

> Response Code

```
200
```

> Response Headers

```json
{
    "total_refunded_amount": 9.7,
    "succeeded": true,
    "sale_token": "b39887704acb480e81ab8611ae256719",
    "sale_amount": 50,
    "sale_id": 89890,
    "amount": 9.7
}
```

> Response Body

```json
{
    "secure_option": true,
    "half_secure": false,
    "currency": "TRY",
    "customer_email": "accept@paytrek.com",
    "language_code": "tr-tr",
    "card_token": "ab5b2706-75c6-4929-8269-9f410fe54178",
    "billing_country": "US",
    "customer_ip_address": "212.57.9.204",
    "description": null,
    "customer_last_name": "Doe",
    "billing_zipcode": "34410",
    "financial_status": null,
    "billing_phone": null,
    "sale_data": {},
    "card_brand": "visa",
    "sys_time": 1529389543,
    "pre_auth": false,
    "billing_address": "123 Market St. San Francisco",
    "card_country": "TR",
    "transactions": [],
    "order_id": "20180424141314",
    "expires_at": "2018-06-19T07:25:42.720412Z",
    "card_issuer": "isbank",
    "hosted_payment": true,
    "sale_token": "8639b66510424eca9b104f5ad0be634e",
    "subscription_plan": {
        "cancelled_at": null,
        "vaults": [
            {
                "card_type": "credit",
                "last4": "4509",
                "token": "ab5b2706-75c6-4929-8269-9f410fe54178",
                "card_brand": "visa"
            }
        ],
        "name": "Test Store Recurring Payments",
        "plan_token": "d0351fe3-4c05-4611-8c38-c07ff33679e0",
        "is_active": true,
        "fixed_currency_amount": null,
        "subscriptions": [
            {
                "status": "Active",
                "payment_date": "2018-10-18T21:00:00Z",
                "subscription_token": "6ac1726c-b8bd-4241-8976-601bcf4e4b75"
            },
            {
                "status": "Active",
                "payment_date": "2018-09-18T21:00:00Z",
                "subscription_token": "a1ad588a-9291-4d14-a4ee-ec94d07389ab"
            },
            {
                "status": "Active",
                "payment_date": "2018-08-18T21:00:00Z",
                "subscription_token": "9b5ee804-5399-423a-a0b7-794f1145ea4a"
            },
            {
                "status": "Active",
                "payment_date": "2018-07-18T21:00:00Z",
                "subscription_token": "73a3eea0-06f8-4113-9412-7c32f976e2e4"
            }
        ],
        "active_card_token": "ab5b2706-75c6-4929-8269-9f410fe54178",
        "created_at": "2018-06-19T06:25:43.520000Z",
        "description": null
    },
    "billing_city": "San Francisco",
    "installment": 1,
    "last4": "4509",
    "is_locked": false,
    "items": [],
    "created_at": "2018-06-19T06:25:43.421093Z",
    "refunded_amount": "0.00",
    "card_type": "credit",
    "amount": "100.00",
    "card_label": "",
    "status": "Created",
    "return_url": "https://www.google.com.tr/",
    "billing_company": null,
    "request_id": null,
    "customer_first_name": "John",
    "card_user_id": "69daf8cb6fe741a885cad8219bdb9dac",
    "callback_url": null,
    "billing_state": "CA",
    "bin_number": "450803"
}
```

| İstek Türü | Kaynak(Endpoint)|
|:-:|:-:|
| POST | https://sandbox.paytrek.com/api/v2/subscription_plan/ |

|Parametre|Tip|Açıklama|
|:-|:-|:-|
|currency |  string, required | Para biriminin 3 harfli ISO kodu.|
|order_id |  string, required | Sipariş numarası.|
|amount |   number, required | Siparişe ait tutar bilgisi.|
|number |  number, required | Kredi kartı numarası.|
|expiration |  string, required | Kredi kartı son kullanma tarihi, MM/YY formatında.|
|cvc |  number, required | Karta ait CVC numarası.|
|card_holder_name |   Kart hamilinin adı.|
|recurring |  object, zorunlu | Ödeme planı özellikleri [1]|
|installment |  number, required | Taksit bilgisi.|
|secure_option |  boolean | Ödemenin 3D güvenli ödeme işlemi olup olmayacağı kararı.(Varsayılan: false)|
|half_secure |  boolean | 3D kayıt kontrolünün (mdstatus) 2, 3, 4 ile sonuçlanması durumunda satışın devam etme kararı.|
|return_url |  string, required | Ödeme işlemi gerçekleştikten sonra müşterinin yönlendirileceği URL|
|pre_auth |  boolean | Ödemenin ön otorizasyonlu geçip geçmediği kontrolü(Varsayılan: false)|
|items |  object, required | Siparişe ait öğelerin listesi.|
|customer_first_name |  string, required | Müşterinin adı.|
|customer_last_name |  string, required | Müşterinin soyadı.|
|customer_email |  string, required | Müşteri emaili.|
|customer_ip_address |  string, required | Müşteri Ip adresi.  |
|billing_address |  string, required | Müşteri fatura adresi.|
|billing_city |  string, required | Müşteri fatura şehri.|
|billing_state |  string | Müşterinin faturalandırma durumu, ABD ve CA için posta kısaltmaları kullanın.|
|billing_country |  string, zorunlu | Müşterinin fatura ülkesi, 2 harfli ISO kodu.|
|billing_zipcode |  string, zorunlu | Müşterinin fatura zip kodu.|
|billing_phone |  string, zorunlu | Müşterinin fatura telfon numarası.|
|sale_data |  object | Satışa ait ekstra bilgi alanı|

[1] Recurring parametresinin içeriği(JSON);

|Parametre|Tip|Açıklama|
|:-|:-|:-|
|name | string, zorunlu | Ödeme planı adı.|
|start_date | string | Ödeme planının başlangıç tarihi.|
|recurring_period | string, zorunlu | Ödeme periodu|
|recurring_frequency | number, zorunlu | Her periotta ödeme tekrarlama sıklığı.|

## Ödeme Planı Görüntüleme

> Request Headers

```json
"Content-Type": "application/json"
"Authorization": "Basic aEtHWE53SFExRk5IOld2OXI5QmdxSnFocQ=="
```

> Response Code

```
200
```

> Response Headers

```json
"Content-Type": "application/json"
```

> Response Body

```json
{
    "secure_option": true,
    "half_secure": false,
    "currency": "TRY",
    "customer_email": "accept@paytrek.com",
    "language_code": "tr-tr",
    "card_token": "ab5b2706-75c6-4929-8269-9f410fe54178",
    "billing_country": "US",
    "customer_ip_address": "212.57.9.204",
    "description": null,
    "customer_last_name": "Doe",
    "billing_zipcode": "34410",
    "financial_status": null,
    "billing_phone": null,
    "sale_data": {},
    "card_brand": "visa",
    "sys_time": 1529389543,
    "pre_auth": false,
    "billing_address": "123 Market St. San Francisco",
    "card_country": "TR",
    "transactions": [],
    "order_id": "20180424141314",
    "expires_at": "2018-06-19T07:25:42.720412Z",
    "card_issuer": "isbank",
    "hosted_payment": true,
    "sale_token": "8639b66510424eca9b104f5ad0be634e",
    "subscription_plan": {
        "cancelled_at": null,
        "vaults": [
            {
                "card_type": "credit",
                "last4": "4509",
                "token": "ab5b2706-75c6-4929-8269-9f410fe54178",
                "card_brand": "visa"
            }
        ],
        "name": "Test Store Recurring Payments",
        "plan_token": "d0351fe3-4c05-4611-8c38-c07ff33679e0",
        "is_active": true,
        "fixed_currency_amount": null,
        "subscriptions": [
            {
                "status": "Active",
                "payment_date": "2018-10-18T21:00:00Z",
                "subscription_token": "6ac1726c-b8bd-4241-8976-601bcf4e4b75"
            },
            {
                "status": "Active",
                "payment_date": "2018-09-18T21:00:00Z",
                "subscription_token": "a1ad588a-9291-4d14-a4ee-ec94d07389ab"
            },
            {
                "status": "Active",
                "payment_date": "2018-08-18T21:00:00Z",
                "subscription_token": "9b5ee804-5399-423a-a0b7-794f1145ea4a"
            },
            {
                "status": "Active",
                "payment_date": "2018-07-18T21:00:00Z",
                "subscription_token": "73a3eea0-06f8-4113-9412-7c32f976e2e4"
            }
        ],
        "active_card_token": "ab5b2706-75c6-4929-8269-9f410fe54178",
        "created_at": "2018-06-19T06:25:43.520000Z",
        "description": null
    },
    "billing_city": "San Francisco",
    "installment": 1,
    "last4": "4509",
    "is_locked": false,
    "items": [],
    "created_at": "2018-06-19T06:25:43.421093Z",
    "refunded_amount": "0.00",
    "card_type": "credit",
    "amount": "100.00",
    "card_label": "",
    "status": "Created",
    "return_url": "https://www.google.com.tr/",
    "billing_company": null,
    "request_id": null,
    "customer_first_name": "John",
    "card_user_id": "69daf8cb6fe741a885cad8219bdb9dac",
    "callback_url": null,
    "billing_state": "CA",
    "bin_number": "450803"
}
```

| İstek Türü | Kaynak(Endpoint)|
|:-:|:-:|
| GET | https://sandbox.paytrek.com/api/v2/subscription_plan/ |

|Parametre|Tip|Açıklama|
|:-|:-|:-|
|plan_token|Ödeme planı tokenı|

## Ödeme Planı Güncelleme

> Request Headers

```json
"Content-Type": "application/json"
"Authorization": "Basic aEtHWE53SFExRk5IOld2OXI5QmdxSnFocQ=="
```

> Request Body

```json
{
    "subscriptions": [{
            "subscription_token": "6ac1726c-b8bd-4241-8976-601bcf4e4b75",
            "status": "Cancelled",
    }],
    "name": "New Test Store Recurring Payments"
}
```

> Response Code

```
200
```

> Response Headers

```json
"Content-Type": "application/json"
```

> Response Body

```json
{
    "cancelled_at": null,
    "vaults": [
        {
            "card_type": "credit",
            "last4": "4509",
            "token": "ab5b2706-75c6-4929-8269-9f410fe54178",
            "card_brand": "visa"
        }
    ],
    "name": "New Test Store Recurring Payments",
    "plan_token": "d0351fe3-4c05-4611-8c38-c07ff33679e0",
    "is_active": true,
    "fixed_currency_amount": null,
    "subscriptions": [
        {
            "status": "Cancelled",
            "payment_date": "2018-10-18T21:00:00Z",
            "subscription_token": "6ac1726c-b8bd-4241-8976-601bcf4e4b75"
        },
        {
            "status": "Active",
            "payment_date": "2018-09-18T21:00:00Z",
            "subscription_token": "a1ad588a-9291-4d14-a4ee-ec94d07389ab"
        },
        {
            "status": "Active",
            "payment_date": "2018-08-18T21:00:00Z",
            "subscription_token": "9b5ee804-5399-423a-a0b7-794f1145ea4a"
        },
        {
            "status": "Active",
            "payment_date": "2018-07-18T21:00:00Z",
            "subscription_token": "73a3eea0-06f8-4113-9412-7c32f976e2e4"
        }
    ],
    "active_card_token": "ab5b2706-75c6-4929-8269-9f410fe54178",
    "created_at": "2018-06-19T06:25:43.520000Z",
    "description": null
}
```

| İstek Türü | Kaynak(Endpoint)|
|:-:|:-:|
| PUT | https://sandbox.paytrek.com/api/v2/subscription_plan/ |

|Parametre|Tip|Açıklama|
|:-|:-|:-|
|plan_token|Ödeme planı tokenı|

# Taksitler

> Request Headers

```json
"Content-Type": "application/json"
"Authorization": "Basic aEtHWE53SFExRk5IOld2OXI5QmdxSnFocQ=="
```

> Response Code

```
200
```

> Response Headers

```json
"Content-Type": "application/json"
```

> Response Body

```json
{
     "sys_time": 1520321002,
     "language_code": "en-us",
     "bin_number": "510118",
     "amount": "123.00",
     "banks": [
         {
             "card_brand": "mastercard",
             "card_type": "credit",
             "card_issuer": "denizbank",
             "card_country": "TR",
             "loyalty": "bonus",
             "is_commercial": false,
             "installments": [
                 {
                     "number": 1,
                     "commission": "9.92",
                     "instalment_amounts": "132.92",
                     "total_amount": "132.92",
                     "total_amount_wo_commission": "113.08"
                 },
                 {
                     "number": 2,
                     "commission": "9.92",
                     "instalment_amounts": "66.46",
                     "total_amount": "132.92",
                     "total_amount_wo_commission": "113.08"
                 },
                 {
                     "number": 3,
                     "commission": "9.92",
                     "instalment_amounts": "44.31",
                     "total_amount": "132.92",
                     "total_amount_wo_commission": "113.08"
                 },
                 {
                     "number": 4,
                     "commission": "5.46",
                     "instalment_amounts": "32.12",
                     "total_amount": "128.46",
                     "total_amount_wo_commission": "117.54"
                 },
                 {
                     "number": 5,
                     "commission": "5.46",
                     "instalment_amounts": "25.69",
                     "total_amount": "128.46",
                     "total_amount_wo_commission": "117.54"
                 },
                 {
                     "number": 6,
                     "commission": "5.46",
                     "instalment_amounts": "21.41",
                     "total_amount": "128.46",
                     "total_amount_wo_commission": "117.54"
                 }
             ]
         }
     ]
 }
```

Verilen ```bin_number``` ve ```amount``` bilgilerine bakılarak. Alternatif
taksit listesini ve bu taksit bilgilerine ait seçeneklerin gösterildiği endpointtir.

İstekte bulunmak için aşağıdaki linkten gerekli parametreleri yeni bir sekme açarak bulabilir
e aynı zamanda anahtar konsolunu kullanarak sekmeler arasında da istekte bulunabilirsiniz.

| İstek Türü | Kaynak(Endpoint)|
|:-:|:-:|
| GET | https://sandbox.paytrek.com/api/v2/installments/?bin_number=bin_number&amount=amount |

|Parametre|Tip|Açıklama|
|:-|:-|:-|
|bin_number | optional | Kartın ilk 6 hanesi|
|amount | - | Taksitlendirme planına ait tutar.|
