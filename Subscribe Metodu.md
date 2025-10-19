
# subscribe() Metodu

- Hatirlatma: subscribe metodu icinde kullanilan yapi Object Literal
```js
{
    property1: string = "mustafa"
    function1: (p: string) => {
        console.writeLine(p);
    }
}
// icinde 1 property 1 fonksiyon olan obje
```

## 📚 **1. Subscribe Metodunun API Reference**

### **TypeScript Signature:**

```typescript
subscribe(observer?: Partial<Observer<T>>): Subscription

// Detaylı hali:
subscribe(observerOrNext?: Partial<Observer<T>> | ((value: T) => void)): Subscription
```

### **Observer Interface:**

```typescript
interface Observer<T> {
  next: (value: T) => void;      // ✅ Başarılı veri geldiğinde
  error: (err: any) => void;     // ✅ Hata olduğunda
  complete: () => void;          // ✅ Stream tamamlandığında (opsiyonel)
}
```

### **Sizin Kullanımınız:**

```typescript
.subscribe({
  next: (data: CategoryViewModel[]) => void,
  error: (error: any) => void
})
// complete yazmadınız, çünkü HTTP istekleri otomatik complete olur
```

### **Dönüş Tipi:**

```typescript
Subscription
// Aboneliği iptal etmek için kullanılır
// subscription.unsubscribe() ile iptal edilebilir
```

---

## 🎯 **2. Subscribe Metodu Genel Olarak Ne Yapıyor?**

### **Temel Kavram: Observer Pattern**

`subscribe()` metodu **Observer (Gözlemci) Pattern**'inin uygulamasıdır.

```
┌─────────────────┐
│   Observable    │  ← Veri kaynağı (HTTP isteği)
│  (Subject)      │
└────────┬────────┘
         │
         │ subscribe()
         │
┌────────▼────────┐
│    Observer     │  ← Sizin verdiğiniz obje
│  (Subscriber)   │     { next, error }
└─────────────────┘
```

### **Adım Adım Ne Oluyor:**

```typescript
// 1. Observable oluşturuluyor (henüz istek YOK!)
const observable = this.categoryService.getCategories();
//    ↑
//    Bu bir "veri kaynağı tanımı" - pasif duruyor

// 2. subscribe() çağrılıyor
observable.subscribe({
  next: (data) => { ... },
  error: (error) => { ... }
});
//         ↑
//         ŞİMDİ istek başladı! Observable aktif oldu!

// 3. Observable iş yapıyor (HTTP isteği)
// 4. Sonuç gelince Observer'ı bilgilendiriyor:
//    - Başarılıysa → next() çağrılır
//    - Hata varsa → error() çağrılır
//    - İş bittiyse → complete() çağrılır
```



### **Detaylı Akış:**

```
┌──────────────────────────────────────────────────┐
│ 1. subscribe() çağrıldı                          │
│    Observer objesi RxJS'e verildi:               │
│    {                                             │
│      next: (data) => { ... },                    │
│      error: (error) => { ... }                   │
│    }                                             │
└──────────────┬───────────────────────────────────┘
               │
               ▼
┌──────────────────────────────────────────────────┐
│ 2. RxJS Observable aktif hale geldi              │
│    HTTP isteği başlatıldı:                       │
│    GET https://localhost:7159/api/category       │
└──────────────┬───────────────────────────────────┘
               │
               ▼
┌──────────────────────────────────────────────────┐
│ 3. Backend işliyor...                            │
│    [Auth Interceptor token ekledi]               │
│    [Backend SQL sorgusu çalıştırdı]              │
└──────────────┬───────────────────────────────────┘
               │
         ┌─────┴─────┐
         │           │
    ✅ Başarılı   ❌ Hata
         │           │
         ▼           ▼
┌─────────────┐ ┌──────────────┐
│ 4a. RxJS:   │ │ 4b. RxJS:    │
│ next()      │ │ error()      │
│ çağırıyor   │ │ çağırıyor    │
└──────┬──────┘ └──────┬───────┘
       │               │
       ▼               ▼
┌─────────────┐ ┌──────────────┐
│ SİZİN       │ │ SİZİN        │
│ next        │ │ error        │
│ fonksiyonu  │ │ fonksiyonu   │
│ çalıştı!    │ │ çalıştı!     │
└──────┬──────┘ └──────┬───────┘
       │               │
       ▼               ▼
┌─────────────┐ ┌──────────────┐
│ categories  │ │ error        │
│ = data      │ │ mesajı set   │
│             │ │ edildi       │
│ loading     │ │              │
│ = false     │ │ loading      │
│             │ │ = false      │
└─────────────┘ └──────────────┘
```

---

## 💡 **Subscribe Metodunun 3 Ana Görevi:**

### **1. Observer'ı Kaydet**
```typescript
// Sizin verdiğiniz objeyi içeride sakla
this.observers.push({
  next: (data) => { ... },
  error: (error) => { ... }
});
```

### **2. Observable'ı Aktif Et**
```typescript
// HTTP isteğini başlat
// Eğer subscribe() çağrılmasaydı, istek asla atılmazdı!
httpClient.get(url).then(...)
```

### **3. Sonuçları Bildir**
```typescript
// Veri geldiğinde veya hata olduğunda Observer'ı çağır
if (success) {
  observer.next(data);  // ← Sizin fonksiyonunuz
} else {
  observer.error(err);  // ← Sizin fonksiyonunuz
}
```

---

## 🔑 **Önemli Özellikler:**

### **1. Cold Observable (Soğuk Observable)**
```typescript
const obs = this.http.get(url);  // ← Henüz istek YOK!

obs.subscribe(...);  // ← 1. istek atılır
obs.subscribe(...);  // ← 2. istek atılır (yeni istek!)
```

HTTP Observable'ları **cold**'dur - her subscribe ayrı istek atar.

### **2. Auto-Completion**
```typescript
// HTTP istekleri otomatik complete olur
// complete: () => { ... } yazmanıza gerek yok
// Çünkü HTTP tek veri döner ve biter
```

### **3. Unsubscribe**
```typescript
const subscription = this.http.get(url).subscribe(...);

// İptal etmek için:
subscription.unsubscribe();
// Ama HTTP'de genelde gerek yok, otomatik temizlenir
```

---

## 📊 **Callback vs Promise vs Observable:**

### **1. Callback (Eski Yöntem)**
```typescript
categoryService.getCategories(
  (data) => { /* success */ },
  (error) => { /* error */ }
);
```

### **2. Promise**
```typescript
categoryService.getCategories()
  .then(data => { /* success */ })
  .catch(error => { /* error */ });
```

### **3. Observable (RxJS - Angular'ın Tercihi)**
```typescript
categoryService.getCategories()
  .subscribe({
    next: data => { /* success */ },
    error: error => { /* error */ }
  });
```

**Neden Observable?**
- ✅ İptal edilebilir (unsubscribe)
- ✅ Operatörler zengin (map, filter, retry, vb.)
- ✅ Çoklu değer desteği (stream)
- ✅ Lazy evaluation (subscribe edilene kadar pasif)

---

## 🎯 **Özet:**

### **subscribe() Metodu:**

| Özellik | Açıklama |
|---------|----------|
| **Ne alır?** | Observer objesi `{ next, error, complete }` |
| **Ne yapar?** | Observable'ı aktif eder, HTTP isteğini başlatır |
| **Ne döner?** | Subscription objesi (iptal için) |
| **Observer ile ne yapar?** | Veri gelince `next()`, hata olunca `error()` çağırır |
| **Ne zaman çalışır?** | Asenkron - veri geldiğinde veya hata olduğunda |

### **Sizin Kodunuzda:**

```typescript
.subscribe({
  next: (data) => { 
    // RxJS bunu çağırır: "İşte verin, başarılı!"
    this.categories = data; 
  },
  error: (error) => { 
    // RxJS bunu çağırır: "Hata var, al!"
    this.error = this.getErrorMessage(error); 
  }
});
```

**Basit ifade:**
> **subscribe() = "Bu veri kaynağına abone ol, veri gelince beni bilgilendir"**

Observer pattern'in mükemmel bir uygulaması! 🚀