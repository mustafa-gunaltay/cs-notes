
# 1)

```ts
private readonly route = inject(ActivatedRoute);

...

ngOnInit(): void {
    const categoryId = this.route.snapshot.params['id'];

    ...
}
```

---

### 🧩 1) `private readonly route = inject(ActivatedRoute);`

* Bu satır, Angular’ın **Dependency Injection (DI)** sistemini kullanarak **ActivatedRoute** servisini **enjekte eder**.
* `ActivatedRoute`, o an aktif olan route (yani sayfanın URL bilgisi, parametreleri, query’leri, fragment’leri) hakkında bilgi verir.
* Kısaca:

  > “Şu an hangi route’tayım, URL’de hangi parametreler var?” bilgisini buradan alırız.

> `inject()` ifadesi, klasik `constructor(private route: ActivatedRoute) {}` sözdiziminin yeni, kısaltılmış halidir (Angular 14+).

---

### 🧭 2) `this.route.snapshot`

* `snapshot`, **ActivatedRoute’un o anki (tek seferlik) durumunun “fotoğrafıdır”**.
* Yani bu, component oluşturulduğu **an** hangi URL’de hangi parametre varsa onu içerir.
* “Canlı dinleme” yapmaz → route değişirse snapshot otomatik güncellenmez.
  Eğer route parametreleri değişirse (örneğin `/categories/1` → `/categories/2`), yeni değeri almak için ya yeniden route’a gitmek gerekir ya da `.paramMap.subscribe()` kullanılmalıdır.
  > Canli dinleme yapilmak isteniyorsa subscribe etmek gerekir

---

### 🧾 3) `this.route.snapshot.params`

* `params` → **path parametrelerini** (yani `/categories/:id` gibi kısımları) tutan bir **nesnedir** (`{ [key: string]: string }` tipiyle).
* URL örneği: `/categories/5`

  ```ts
  this.route.snapshot.params['id']  // { id: "5" }
  ```
* Yani bu satırdaki `params['id']`, URL’deki `:id` değerini (string olarak) alır.

---

### ✅ 4) Sonuç olarak şu yapılıyor:

```ts
private readonly route = inject(ActivatedRoute);
const categoryId = this.route.snapshot.params['id'];
```

Bu iki satırın anlamı:

> “Şu anda bulunduğum route’un URL’inden `id` parametresini al ve `categoryId` değişkenine ata.”

Örnek:

```
URL → /categories/42
categoryId → "42"
```

---
