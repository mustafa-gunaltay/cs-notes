
# 1) combineLatest

```ts
import { combineLatest, throwError } from 'rxjs';

...

combineLatest([
      this.categoryService.getCategories(),
      this.authService.getCurrentUser() 
    ])
    .
    pipe(...)
    .subscribe(...)
```

- Reactive Programlama ile ilgili bir seymis, once bu programlama tipi arastirilmali.

- combineLatest fonksiyonu `Observable<[T1, T2, T3, ...]>` donduren bir container'dir
    - Bu ornekte combineLatest `[categories, currentUser]` formatinda bir array dondurur.
    - combineLatest'dan sonra gelecek olan metotlar bi array'i isleyebilecek kabiliyettedir

- combineLatest() metodu → icine kac tane metot yazildiysa hepsini ayni anda dinler. Her biri en az bir kez veri gönderince [val1, val2, ...] olarak tek dizi döner.