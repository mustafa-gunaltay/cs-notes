
# 1) Arrow Function ile degisken icine tanimlanmis fonksiyon ve onun cagirimi

## 🧩 1️⃣ Değişken içine atanmış fonksiyon (arrow function)

Bu en yaygın formdur:

```typescript
const topla = (a: number, b: number): number => {
  return a + b;
};
```

### 🧠 Kullanımı:

```typescript
console.log(topla(3, 5)); // 8
```

Bu yapı genellikle:

* Bir bileşen (`component`) içinde
* Ya da bir service class’ında callback, event, subscription gibi durumlarda
  kullanılır çünkü `this` bağlamını **korur**.

---

## 🧩 2️⃣ Sınıf içinde tanımlanan metot (Angular component örneği)

```typescript
export class AppComponent {
  title = 'Merhaba Dünya';

  // klasik metot biçimi
  selamla(): void {
    console.log(`Selam ${this.title}`);
  }

  // arrow function biçimi
  selamla2 = (): void => {
    console.log(`Selam ${this.title}`);
  };
}
```

### 🧠 Kullanımı (HTML tarafında):

```html
<button (click)="selamla()">Selamla</button>
```

veya

```html
<button (click)="selamla2()">Selamla</button>
```

İkisi de çağrılır, ama fark şudur 👇

* `selamla()` klasik method → `this` bağlamı kaybolabilir (örneğin event callback içinde).
* `selamla2 = () => {}` arrow function → `this` her zaman sınıfın kendisini (component instance’ı) gösterir.

Bu yüzden Angular bileşenlerinde sık sık:

```typescript
metotAdi = (): void => { ... }
```

biçimi tercih edilir.

---

## 🧩 3️⃣ Arrow function + parametreli kullanım (örnek)

```typescript
sayHello = (name: string): void => {
  console.log(`Hello ${name}!`);
};
```

HTML tarafında:

```html
<button (click)="sayHello('Mustafa')">Selamla</button>
```

---







# 2) Angular'da sonu "Fn" ile biten tipler

- Fn son eki Angular'ın kendi isimlendirme standardıdır. Angular ekibi fonksiyon tiplerini genelde ...Fn ile adlandırmayı tercih ediyor.

```ts
export const authInterceptor: HttpInterceptorFn = (req, next) => {
    //...
}
```

# 3) Constructor ile Dependency Injection Kisaltilmis Hali

- private readonly route = inject(ActivatedRoute);

  - inject() ifadesi, klasik constructor(private route: ActivatedRoute) {} sözdiziminin yeni, kısaltılmış halidir (Angular 14+).
