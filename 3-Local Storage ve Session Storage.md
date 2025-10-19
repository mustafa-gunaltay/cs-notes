
# 1) Local Storage

Harika bir konudasın. `localStorage`, modern frontend geliştirmenin temel taşlarından biridir. Angular (ve React/Vue gibi diğer framework'ler) ile çalışırken sıkça kullanacaksın.

İşte `localStorage` hakkında bilmen gerekenler, basit ve net bir şekilde:

### 1\. Local Storage Nedir?

`localStorage`, tarayıcının (Chrome, Firefox vb.) sana sağladığı küçük bir "depolama alanıdır". Web sitelerinin, kullanıcının bilgisayarında küçük verileri kalıcı olarak saklamasına olanak tanır.

Temel özellikleri şunlardır:

  * **Kalıcıdır (Persistent):** Tarayıcıyı kapatsan, bilgisayarı yeniden başlatsan bile bu veriler silinmez. Veri, sadece sen JavaScript koduyla silersen veya kullanıcı tarayıcı ayarlarından manuel olarak temizlerse kaybolur.
  * **Anahtar-Değer (Key-Value) Yöntemi:** Verileri bir "anahtar" (key) ve ona karşılık gelen bir "değer" (value) olarak saklar. Bir sözlük (dictionary) veya JavaScript objesi gibi düşünebilirsin.
  * **Sadece String Saklar:** En önemli kural budur. `localStorage` teknik olarak *sadece metin (string)* depolar. Obje veya sayı kaydetmek istersen, onları string'e dönüştürmen gerekir (JSON kullanarak).
  * **Domain'e Özgüdür:** `ornek.com` sitesinin `localStorage`'ına, `baskasite.com` erişemez. Bu bir güvenlik önlemidir.

### 2\. Hangi Problemi Çözer?

`localStorage`'dan önce, bu iş için genellikle **Cookie (Çerez)** kullanılırdı. Ancak Cookie'lerin bir problemi vardı:

**Problem:** Cookie'ler, sunucuya gönderilen *her bir HTTP isteğine* (sayfa yenileme, resim çekme vb.) otomatik olarak eklenir.

**Çözüm:** Diyelim ki kullanıcının "Koyu Mod" (Dark Mode) tercihini hatırlamak istiyorsun. Bu bilgi sunucunun bilmesi gereken bir şey değil, sadece tarayıcının bilmesi yeterli.

  * **Eski Yöntem (Cookie):** "Koyu Mod" bilgisini Cookie'ye yazarsan, kullanıcı sitede her tıkladığında bu gereksiz `theme=dark` bilgisi sunucuya gidip gelir. Bu, ağ trafiğini (network) şişirir.
  * **Yeni Yöntem (localStorage):** "Koyu Mod" bilgisini `localStorage`'a yazarsın. Bu veri kullanıcının tarayıcısında kalır, sunucuya *asla* gönderilmez. Sayfa yüklendiğinde JavaScript ile `localStorage`'ı kontrol eder, temayı ona göre ayarlarsın.

Kısacası `localStorage`, **sunucuyu ilgilendirmeyen, sadece tarayıcıda kalması gereken** küçük kullanıcı tercihlerini veya verilerini saklamak için idealdir.

### 3\. Temel Kullanım (Basit Örnekler)

`localStorage`'ın 4 temel komutu vardır. Bu örnekler saf JavaScript'tir ve Angular'da da doğrudan çalışır:

#### a) Veri Kaydetme: `setItem`

Bir anahtara (key) bir değer (value) atar.

```javascript
// 'kullaniciTemasi' adında bir anahtar oluştur ve değerini 'koyu' yap
localStorage.setItem('kullaniciTemasi', 'koyu');

// 'welcomeMessage' adında bir anahtar oluştur
localStorage.setItem('welcomeMessage', 'Merhaba Dünya!');
```

#### b) Veri Okuma: `getItem`

Bir anahtarın değerini okur. Eğer anahtar yoksa `null` döner.

```javascript
const tema = localStorage.getItem('kullaniciTemasi');
// tema değişkeninin değeri şimdi 'koyu'

const olmayanVeri = localStorage.getItem('buAnahtarYok');
// olmayanVeri değişkeninin değeri şimdi null
```

#### c) Veri Silme: `removeItem`

Sadece belirli bir anahtarı siler.

```javascript
// 'welcomeMessage' anahtarını ve değerini depodan sil
localStorage.removeItem('welcomeMessage');
```

#### d) Tümünü Temizleme: `clear`

O siteye ait `localStorage`'daki *tüm* verileri siler.

```javascript
localStorage.clear();
```

### 4\. En Önemli Konu: Obje (Object) ve Dizileri (Array) Saklamak

Angular'da genellikle basit stringler değil, kullanıcı ayarları gibi objeleri saklamak istersin.

**Sorun:** `localStorage` sadece string saklar.
**Çözüm:** Objeyi **JSON** formatına (string) çeviririz.

  * `JSON.stringify(object)`: Objeyi -\> String'e çevirir. (Kaydetmek için)
  * `JSON.parse(string)`: String'i -\> Objeye çevirir. (Okumak için)

#### Örnek:

```javascript
// 1. Saklamak istediğimiz ayarlar objesi
const kullaniciAyarlari = {
  kullaniciAdi: 'mehmet',
  bildirimlerAcik: true,
  dil: 'tr'
};

// 2. Objeyi string'e çevir ve KAYDET
localStorage.setItem('ayarlar', JSON.stringify(kullaniciAyarlari));

// ... (Kullanıcı tarayıcıyı kapattı, açtı) ...

// 3. String olarak OKU
const kayitliAyarlarString = localStorage.getItem('ayarlar');

// 4. String'i tekrar objeye çevir ve KULLAN
const okunanAyarlar = JSON.parse(kayitliAyarlarString);

console.log(okunanAyarlar.kullaniciAdi); // 'mehmet'
console.log(okunanAyarlar.bildirimlerAcik); // true
```

### 5\. Best Practices (İyi Kullanım Yöntemleri)

Angular öğrenirken bu kurallara dikkat etmen çok önemlidir.

**1. ASLA HASSAS VERİ SAKLAMA\!**
`localStorage` güvenli, şifreli bir alan değildir. Tarayıcı konsolunu açan herkes (veya bir XSS saldırısı) buradaki verileri okuyabilir.

  * **YAPMA:** Şifre, Kredi Kartı Bilgisi, TC Kimlik Numarası, API Key (gizli anahtar).
  * **YAPABİLİRSİN:** Kullanıcı adı (e-posta değil), "Beni Hatırla" seçeneği (true/false), tema tercihi (dark/light), dil tercihi (tr/en).
  * *(Not: JWT (Giriş Token'ları) burada saklamak yaygın bir pratiktir ancak güvenlik tartışmalarına açıktır. Başlangıç için saklayabilirsin ama riskini bilmelisin.)*

**2. VERİ BOYUTUNU KÜÇÜK TUT.**
`localStorage`'ın bir limiti vardır (genellikle tarayıcı başına 5MB - 10MB). Burası bir veritabanı değildir. Binlerce satır veriyi burada saklamaya çalışma.

**3. ANGULAR İÇİN: BİR "STORAGE SERVICE" OLUŞTUR.**
Angular'da `localStorage.setItem()` komutunu doğrudan component'lerin (bileşenlerin) içine yazmak "best practice" değildir. Test etmesi zorlaşır ve kod tekrarına yol açar.

Bunun yerine, `localStorage` işlemlerini yapan bir **Servis (Service)** oluştur.

**Örnek (Basit bir Angular Servisi):**
Bu, `localStorage`'ı "Angular dilinde" kullanmanın en doğru yoludur.

```typescript
// src/app/services/storage.service.ts

import { Injectable } from '@angular/core';

@Injectable({
  providedIn: 'root'
})
export class StorageService {

  constructor() { }

  // Veri kaydetme (JSON'a çevirmeyi otomatik yapar)
  public saveData(key: string, value: any): void {
    localStorage.setItem(key, JSON.stringify(value));
  }

  // Veri okuma (JSON'dan çevirmeyi otomatik yapar)
  public getData(key: string): any {
    const data = localStorage.getItem(key);
    
    // Veri varsa parse et, yoksa null dön
    return data ? JSON.parse(data) : null;
  }

  // Veri silme
  public removeData(key: string): void {
    localStorage.removeItem(key);
  }

  // Tümünü temizleme
  public clearData(): void {
    localStorage.clear();
  }
}
```












# 2) Session Storage

`sessionStorage`, `localStorage`'ın aralarında çok kritik ve önemli bir fark vardır: **Yaşam Süresi**.


### 1\. Session Storage Nedir?

`sessionStorage`, tıpkı `localStorage` gibi, tarayıcının sağladığı bir "depolama alanıdır". Verileri anahtar-değer (key-value) olarak saklar ve sadece string (metin) kabul eder.

**`localStorage`'dan tek farkı:**

`sessionStorage`'a kaydettiğin veriler, **sadece o anki tarayıcı sekmesi açık kaldığı sürece** yaşar.

  * Kullanıcı tarayıcı sekmesini veya penceresini kapattığı an, o sekmeye ait `sessionStorage`'daki **tüm veriler otomatik olarak silinir.**
  * `localStorage` ise kalıcıydı; tarayıcı kapansa bile veriler silinmiyordu.

### 2\. Hangi Problemi Çözer?

**Problem:** Bazen verileri *kalıcı* olarak değil, sadece kullanıcının o anki "oturum (session)" süresince saklamak isteriz. Kullanıcı siteden ayrıldığında bu geçici verilerin de kaybolmasını isteriz.

**Örnek Senaryo:**
Çok adımlı bir form dolduruyorsun (Örn: Uçak bileti rezervasyonu).

1.  **Adım 1:** Kişisel bilgilerini girdin (Ad, Soyad).
2.  **Adım 2:** Koltuk seçimi yapıyorsun.
3.  **Adım 3:** Ödeme bilgileri.

Bu adımlar arasında geçiş yaparken, 1. Adım'daki "Ad, Soyad" bilgisini bir yerde geçici olarak tutman gerekir.

  * **`localStorage` Kullanırsan (Kötü Yöntem):** Kullanıcı Adım 2'deyken tarayıcıyı kapatırsa ve 3 gün sonra siteye geri dönerse, Ad/Soyad bilgisi hala orada kalır. Bu genellikle istenmeyen bir durumdur. Formun sıfırdan başlaması gerekir.
  * **`sessionStorage` Kullanırsan (İyi Yöntem):** Kullanıcı formu doldururken sekme içinde ileri-geri gidebilir, verileri `sessionStorage`'da saklanır. Ama kullanıcı rezervasyon form sekmesini kapattığı an, o geçici form verileri otomatik olarak temizlenir. Kullanıcı siteye tekrar geldiğinde forma temiz bir başlangıç yapar.

Kısacası `sessionStorage`, **"sadece bu sekme kapanana kadar geçerli olan geçici veriler"** için kullanılır.

### 3\. Temel Kullanım (Basit Örnekler)

Kullanımı `localStorage` ile **%100 aynıdır**. Sadece `localStorage` yerine `sessionStorage` yazarsın.

#### a) Veri Kaydetme: `setItem`

```javascript
// O anki sekmeye 'geciciBilgi' adında bir veri kaydet
sessionStorage.setItem('geciciBilgi', 'Bu veri sekme kapanınca silinecek');
```

#### b) Veri Okuma: `getItem`

```javascript
const veri = sessionStorage.getItem('geciciBilgi');
// veri değişkeninin değeri: 'Bu veri sekme kapanınca silinecek'

// ÖNEMLİ: Başka bir sekmeden buna erişemezsin!
```

#### c) Veri Silme: `removeItem`

```javascript
// (Sekme kapanmadan manuel silmek istersen)
sessionStorage.removeItem('geciciBilgi');
```

#### d) Tümünü Temizleme: `clear`

```javascript
// O sekmeye ait tüm session verilerini temizler
sessionStorage.clear();
```

### 4\. Obje (Object) ve Dizileri (Array) Saklamak

Bu konu da `localStorage` ile %100 aynıdır. `JSON.stringify` ve `JSON.parse` kullanman gerekir.

```javascript
// 1. Saklamak istediğimiz geçici form verisi
const formAdim1 = {
  ad: 'Ahmet',
  soyad: 'Yılmaz'
};

// 2. String'e çevir ve KAYDET
sessionStorage.setItem('adim1Verisi', JSON.stringify(formAdim1));

// ... (Kullanıcı 2. adıma geçti) ...

// 3. String olarak OKU
const kayitliVeriString = sessionStorage.getItem('adim1Verisi');

// 4. Objeye çevir ve KULLAN
const okunanVeri = JSON.parse(kayitliVeriString);

console.log(okunanVeri.ad); // 'Ahmet'
```

### 5\. Best Practices (İyi Kullanım Yöntemleri)

**1. Doğru Yeri Seç: `session` vs `local`**
Veriyi kaydetmeden önce kendine şu soruyu sor:

  * "Bu veri, kullanıcı tarayıcıyı kapatıp açtıktan sonra da gerekli mi?"
      * **Evet:** `localStorage` kullan (Örn: Koyu Mod tercihi, Dil tercihi).
      * **Hayır:** `sessionStorage` kullan (Örn: Geçici form verileri, o anki oturuma özel filtre ayarları).

**2. Güvenlik (`localStorage` ile Aynı Kural)**
`sessionStorage` da güvenli değildir, şifrelenmez. Tarayıcı konsolundan okunabilir. **Asla** şifre, kredi kartı gibi hassas verileri burada saklama.

**3. ANGULAR İÇİN: Aynı "Storage Service"i Kullanmak**
`localStorage` için yaptığımız `StorageService` örneğini, `sessionStorage`'ı da destekleyecek şekilde kolayca güncelleyebilirsin.

**Örnek (Güncellenmiş Angular Servisi):**

```typescript
// src/app/services/storage.service.ts

import { Injectable } from '@angular/core';

@Injectable({
  providedIn: 'root'
})
export class StorageService {

  constructor() { }

  // ----- Local Storage Fonksiyonları -----
  
  public saveLocalData(key: string, value: any): void {
    localStorage.setItem(key, JSON.stringify(value));
  }

  public getLocalData(key: string): any {
    const data = localStorage.getItem(key);
    return data ? JSON.parse(data) : null;
  }

  public removeLocalData(key: string): void {
    localStorage.removeItem(key);
  }

  // ----- Session Storage Fonksiyonları -----

  public saveSessionData(key: string, value: any): void {
    sessionStorage.setItem(key, JSON.stringify(value));
  }

  public getSessionData(key: string): any {
    const data = sessionStorage.getItem(key);
    return data ? JSON.parse(data) : null;
  }

  public removeSessionData(key: string): void {
    sessionStorage.removeItem(key);
  }

  // ----- Genel Temizleme Fonksiyonları -----

  public clearAllLocalData(): void {
    localStorage.clear();
  }

  public clearAllSessionData(): void {
    sessionStorage.clear();
  }
}
```

Bu servis sayesinde Angular component'lerin içinde `localStorage` veya `sessionStorage`'ı doğrudan düşünmek zorunda kalmazsın, sadece servisin `saveLocalData` veya `saveSessionData` gibi temiz fonksiyonlarını çağırırsın.











# 3) Local Storage ve Session Storage Temel Farkliliklari



### Local Storage vs. Session Storage: Karşılaştırma Tablosu

| Özellik | `localStorage` | `sessionStorage` |
| :--- | :--- | :--- |
| **Yaşam Süresi** | **Kalıcıdır.** | **Geçicidir (Oturum Süresince).** |
| (Ne zaman silinir?) | Tarayıcı kapatılsa, bilgisayar yeniden başlasa bile veriler **silinmez**. | Tarayıcı **sekmesi** veya penceresi kapatıldığı an **tüm veriler silinir**. |
| | Veri ancak JavaScript kodu (`localStorage.clear()` vb.) ile veya kullanıcı tarayıcı verilerini manuel temizlerse silinir. | Sayfa yenilemek (`F5`) verileri **silmez**, ancak sekme kapanırsa silinir. |
| **Kapsam (Scope)** | **Domain Genelidir.** | **Sekme Özelidir.** |
| (Kimler erişebilir?) | Aynı tarayıcıda, aynı domain (`ornek.com`) altındaki **tüm sekmeler ve pencereler** aynı `localStorage` verisini paylaşır. | Sadece o an açık olan **tek bir tarayıcı sekmesi** erişebilir. |
| | Bir sekmede `localStorage.setItem('a', 1)` yaparsan, aynı sitede açtığın *başka bir sekme* bu veriyi okuyabilir. | Bir sekmede `sessionStorage.setItem('b', 2)` yaparsan, aynı sitede açtığın *başka bir sekme* bu veriyi **göremez**. |
| **Tipik Kullanım** | Kullanıcının tercihlerini **kalıcı** olarak hatırlamak. | **Geçici**, "kullan-at" verileri saklamak. |
| (Örnekler) | • Tema tercihi (Koyu/Açık Mod)<br> • Dil seçimi (tr/en)<br> • "Beni Hatırla" seçeneği<br> • Kullanıcının kimlik token'ı (JWT) | • Çok adımlı bir formun ara verileri<br> • O anki oturuma ait geçici filtre ayarları<br> • Sepete eklenen ama satın alınmayan ürünler (eğer sekme kapanınca sepetin boşalması isteniyorsa) |

-----

### Özet: Ne Zaman Hangisini Kullanmalı?

Bu iki soruyu cevaplayarak doğru olana karar verebilirsin:

**1. `localStorage` ne zaman kullanılır?**

  * **Soru:** "Bu veri, kullanıcı tarayıcıyı kapatıp 3 gün sonra geri geldiğinde bile orada olmalı mı?"
  * **Cevap EVET ise:** `localStorage` kullan.
  * **Anahtar Kelime:** **Kalıcılık** (Persistence).

**2. `sessionStorage` ne zaman kullanılır?**

  * **Soru:** "Bu veri, sadece kullanıcının bu sekmede yaptığı işlem için mi gerekli? Sekmeyi kapattığında bu verinin kaybolması sorun olur mu (hatta istenir mi)?"
  * **Cevap EVET ise:** `sessionStorage` kullan.
  * **Anahtar Kelime:** **Geçicilik** (Temporary / Session-based).

###  Benzer Yönleri

Bu iki depolama türü, temel farkları dışında tamamen aynı şekilde çalışır:

  * İkisi de **güvenli değildir** (Hassas veri, şifre, kredi kartı saklanmaz).
  * İkisi de **sadece string (metin)** saklar (Obje saklamak için `JSON.stringify` / `JSON.parse` gerekir).
  * İkisi de aynı komutları kullanır (`setItem`, `getItem`, `removeItem`, `clear`).