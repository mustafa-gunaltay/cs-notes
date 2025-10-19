
# 1) Interceptor

## 🎯 **HTTP Interceptor Nedir?**

### ✅ **Sizin Tanımınız:**
> "Gönderilecek her HTTP isteğine otomatik şekilde eklenecek header vb bilgileri ekleyen yapılar"

**Tamamen doğru!** Ama sadece header eklemekle sınırlı değil, çok daha fazlasını yapabilir.

---

## 🔄 **Interceptor Nasıl Çalışır? (Basit Anlatım)**

### **1. Pipeline Mantığı**

Evet, **PipelineBehaviour** benzeri çalışır! Angular'da buna **Interceptor Chain** (Zincirleme) denir.

```
Component Request
       ↓
[Interceptor 1] → Token ekle
       ↓
[Interceptor 2] → Log yaz
       ↓
[Interceptor 3] → Loading göster
       ↓
    Backend
       ↓
[Interceptor 3] ← Loading gizle
       ↓
[Interceptor 2] ← Log yaz
       ↓
[Interceptor 1] ← Error handle
       ↓
Component Response
```

---

## 📝 **Sizin Interceptor'ınız - Adım Adım:**

```typescript
export const authInterceptor: HttpInterceptorFn = (req, next) => {
  // 1️⃣ ADIM: AuthService'i inject et
  const authService = inject(AuthService);
  
  // 2️⃣ ADIM: Token'ı al
  const token = authService.getToken();
  
  // 3️⃣ ADIM: Token varsa isteği klonla ve header ekle
  if (token) {
    const clonedRequest = req.clone({
      setHeaders: { Authorization: `Bearer ${token}` }
    });
    return next(clonedRequest);  // ← Değiştirilmiş isteği gönder
  }
  
  // 4️⃣ ADIM: Token yoksa orijinal isteği gönder
  return next(req);
};
```

---

## 🔍 **Nasıl Otomatik Çalışır?**

### **Kayıt: [`app.config.ts`](src/app/app.config.ts )**

```typescript
export const appConfig: ApplicationConfig = {
  providers: [
    provideHttpClient(
      withInterceptors([authInterceptor])  // ← Buradan kayıt ediliyor!
    )
  ]
};
```

**Bu kayıt sayesinde:**
- ✅ HttpClient kullanılan **HER YERDE** devreye girer
- ✅ Manuel olarak çağırmana gerek yok
- ✅ Tüm GET, POST, PUT, DELETE istekleri otomatik geçer

---

## 🎬 **Gerçek Hayat Örneği:**

### **Component'te:**
```typescript
// category-list.component.ts
this.categoryService.getCategories().subscribe(...);
```

### **Service'te:**
```typescript
// category.service.ts
return this.http.get<any>(this.apiUrl);  // ← Sadece GET isteği
```

### **Arka Planda Olan:**
```
1. http.get() çağrılır
2. Angular HTTP Client devreye girer
3. Kayıtlı interceptor'ları kontrol eder
4. authInterceptor bulunur
5. authInterceptor çalışır:
   ✓ Token alır
   ✓ İsteği klonlar
   ✓ Authorization header ekler
6. Değiştirilmiş istek backend'e gider
```

---

## 🛠️ **Interceptor'ler Ne İşe Yarar?**

### **1. Authentication (Sizin Kullanımınız)**
```typescript
// Token ekle
req.clone({ setHeaders: { Authorization: `Bearer ${token}` } })
```

### **2. Error Handling**
```typescript
return next(req).pipe(
  catchError(error => {
    if (error.status === 401) {
      // Token geçersiz, login'e yönlendir
      router.navigate(['/login']);
    }
    return throwError(() => error);
  })
);
```

### **3. Loading Spinner**
```typescript
const loadingService = inject(LoadingService);
loadingService.show();

return next(req).pipe(
  finalize(() => loadingService.hide())
);
```

### **4. Logging**
```typescript
console.log('📤 Request:', req.method, req.url);

return next(req).pipe(
  tap(response => console.log('📥 Response:', response))
);
```

### **5. API Base URL Ekleme**
```typescript
const apiUrl = environment.apiUrl;
const modifiedReq = req.clone({
  url: `${apiUrl}${req.url}`
});
return next(modifiedReq);
```

---

## 🎯 **Sizin Interceptor'ınızın Yaptığı:**

```typescript
🔐 Auth Interceptor:
   ↓
❓ Token var mı?
   ├─ ✅ Evet → Authorization header ekle → Backend'e gönder
   └─ ❌ Hayır → Olduğu gibi gönder
```

---

## 💡 **PipelineBehaviour ile Karşılaştırma:**

| Özellik | .NET PipelineBehaviour | Angular Interceptor |
|---------|------------------------|---------------------|
| **Amaç** | MediatR request/response pipeline | HTTP request/response pipeline |
| **Zincir** | ✅ Var (Handle method) | ✅ Var (next function) |
| **Kayıt** | `AddBehavior<T>()` | `withInterceptors([])` |
| **Otomatik** | ✅ Evet | ✅ Evet |
| **Sıralama** | ✅ Kayıt sırasına göre | ✅ Kayıt sırasına göre |

**Evet, mantık tamamen aynı!** 🎯

---

## 🧪 **Test Etmek İçin:**

Console'da görmek için:

```typescript
console.log('🔐 Auth Interceptor:', {
  url: req.url,           // → "https://localhost:7159/api/category"
  hasToken: !!token,      // → true
  method: req.method      // → "GET"
});
```

Network sekmesinde:
```
Request Headers:
  Authorization: Bearer eyJhbGciOiJSUzI1NiIs...
  ↑ Bu header'ı interceptor ekledi!
```

---

## 📊 **Özet:**

1. ✅ **Tanımınız doğru:** Header eklemek için kullanılır
2. ✅ **Pipeline mantığı doğru:** PipelineBehaviour benzeri çalışır
3. ✅ **Otomatik çalışır:** HttpClient her kullanıldığında devreye girer
4. ✅ **`next()` ile zincir:** Bir sonraki adıma geçer
5. ✅ **Tek kayıt:** [`app.config.ts`](src/app/app.config.ts )'de `withInterceptors([...])`

---

## 🎓 **Kısa Cevap:**

> **Interceptor = HTTP isteklerinin araya giren otomatik middleware'i**
> 
> Pipeline'da çalışır, `next()` ile zincir yapar, tüm HttpClient isteklerini otomatik yakalar.


# 2) next() Fonksiyonu

## 🎯 **`next` Fonksiyonu Ne İşe Yarıyor?**

`next` fonksiyonu **pipeline'daki bir sonraki adıma geçmek** için kullanılır.

---

## 🔄 **Basit Açıklama:**

```typescript
export const authInterceptor: HttpInterceptorFn = (req, next) => {
  // 1. Token ekle
  const modifiedReq = req.clone({ ... });
  
  // 2. next() ile "Tamam, işim bitti, devam et" de
  return next(modifiedReq);
  //     ↑
  //     "Bu isteği bir sonraki adıma gönder"
};
```

**`next(request)`** = **"Bu HTTP isteğini işlemeye devam et"**

---

## 📊 **Pipeline Akışı:**

### **Tek Interceptor Varsa:**

```
Component
    ↓
http.get()
    ↓
[authInterceptor]
    ↓
  next(req)  ← "Backend'e gönder"
    ↓
Backend
    ↓
Response
    ↓
Component
```

### **Birden Fazla Interceptor Varsa:**

```typescript
// app.config.ts
withInterceptors([
  authInterceptor,      // 1. önce
  loggingInterceptor,   // 2. sonra
  errorInterceptor      // 3. en son
])
```

**Akış:**

```
Component
    ↓
http.get()
    ↓
[authInterceptor]
  next(req) ← "Sonrakine geç"
    ↓
[loggingInterceptor]
  next(req) ← "Sonrakine geç"
    ↓
[errorInterceptor]
  next(req) ← "Backend'e gönder"
    ↓
Backend
    ↓
Response (ters sırayla)
    ↓
[errorInterceptor]
    ↓
[loggingInterceptor]
    ↓
[authInterceptor]
    ↓
Component
```

---

## 🎓 **`next` Fonksiyonunun Tipi:**

```typescript
type HttpHandlerFn = (req: HttpRequest<unknown>) => Observable<HttpEvent<unknown>>;
//   ↑
//   next parametresinin tipi
```

**Ne döndürür?**
- `Observable<HttpEvent>` → RxJS Observable
- İçinde HTTP response'u var

**Nasıl kullanılır?**
```typescript
return next(req);  // ← Observable döner
```

---

## 🔄 **Middleware/Pipeline Pattern:**

Bu pattern birçok frameworkte var:

### **Express.js (Node.js):**
```javascript
app.use((req, res, next) => {
  console.log('Middleware');
  next();  // ← Aynı konsept!
});
```

### **ASP.NET Core:**
```csharp
app.Use(async (context, next) => {
    // Ön işlem
    await next();  // ← Aynı konsept!
    // Son işlem
});
```

### **Angular:**
```typescript
export const interceptor: HttpInterceptorFn = (req, next) => {
  // Ön işlem
  return next(req);  // ← Aynı konsept!
};
```

---

## 💡 **Gerçek Hayat Benzetmesi:**

**Güvenlik Kontrolü Gibi Düşünün:**

```
Havaalanında:
1. Pasaport kontrolü → next() → Çanta kontrolü
2. Çanta kontrolü → next() → Kapıya git
3. Kapı → next() → Uçağa bin

Eğer bir adımda next() çağrılmazsa:
❌ Kişi takılır, ilerleyemez
```

**Aynı şekilde:**

```
HTTP İsteği:
1. Auth Interceptor → next() → Logging Interceptor
2. Logging Interceptor → next() → Error Interceptor
3. Error Interceptor → next() → Backend

Eğer bir adımda next() çağrılmazsa:
❌ İstek takılır, backend'e ulaşamaz
```

---

## 🔑 **Özet:**

| Özellik | Açıklama |
|---------|----------|
| **`next` ne yapar?** | Pipeline'da bir sonraki adıma geçer |
| **Ne döndürür?** | `Observable<HttpEvent>` |
| **Zorunlu mu?** | ✅ Evet, mutlaka `return next(req)` yapmalısınız |
| **Birden fazla interceptor?** | Sırayla hepsini çağırır |
| **Response işleme** | `next(req).pipe(...)` ile yapılır |

---

## 🎯 **Kısa Cevap:**

> **`next(request)` = "Bu isteği işlemeye devam et"**
> 
> - Tek interceptor varsa → Backend'e gönderir
> - Birden fazla varsa → Sonraki interceptor'a geçer
> - `return next(req)` yapmazsanız → İstek asla gönderilmez! ❌


