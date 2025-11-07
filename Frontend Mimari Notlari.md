
# 1) Dosya Isimlendirmeleri


1.  **Dosya Tipi (Uzantı):** `[...].ts`, `[...].html`, `[...].css`
    * Bu, dosyanın **teknolojik tipini** belirtir.
    * Bilgisayara, derleyiciye (compiler) veya tarayıcıya "Bu dosyanın içinde TypeScript kodu var", "Bu bir HTML şablonu" veya "Bu bir stil dosyası" der.
    * Bu, dosyanın *nasıl* işleneceğini belirleyen teknik bir kuraldır.

2.  **Mimari Rol (Sonek):** `[...].component`, `[...].service`, `[...].model`, `[...].interceptor`
    * Bu, dosyanın **mimari rolünü** veya **amacını** belirtir.
    * Dosyanın teknolojik tipine (`.ts` olmasına) *etki etmez*. `auth.service.ts` de `auth.interceptor.ts` de birer TypeScript dosyasıdır.
    * Bu, **size, ekibinize ve Angular CLI'a** "Bu dosya bir Component'in mantığını içeriyor", "Bu dosya bir API servisi" veya "Bu dosya bir HTTP isteği yakalayıcı" der.
    * Bu, dosyanın *ne iş yaptığını* belirleyen bir adlandırma kuralıdır (convention).

> Ozetleyecek olursak. Dosyanin tipini belirleyen sey son "." dan sonra gelen kisim ondan once gelen .intercepter, .model gibi yazilar dosyanin tipine etki etmiyor sadece o dosyanin neyle alakali oldugunun bilgisini veriyor (servis mi component mi vs) 
> - "app.ts" ile "app.component.ts" arasinda isimlendirmeden baska bir fark yoktur









# 2) Loglama

- Backendde oldugu gibi merkezi bir loglama servisi implement edildi (**logger.service.ts**) (ana loglama servisi)
  - Sorumlulugu
    - `Debug()`, `Info()`, `Warn()`, `Error()` gibi bir api sunuyor
    - `Logger.config`’e gore yukaridan hangi metotlarin loglama yapip loglama yapmayacaginin kararini veriyor. Ornegin loglama seviyesi warning ise debug ve info loglari calismayacak (loglama yapmayacak)
      - Frontend developer sadece `logger.config` icindeki ayari degistirerek proje geneli loglama seviyesini belirleyebilir
        > **NOT**: `logger.config` cok buyuk oranda environment dosyasi ile baglantilidir. Environment production ise loglama seviyesi warn, degil ise (gelistirme yapilmaya devam ediliyorsa) debug seviyesinde loglama yapilir
    - Frontend developer’in arka planda ne oldugunu bilmeden loglama islemini yapmasi
- Ana loglama servisine Yardimci Loglama servisleri implement edildi (bu yardimci servisler **logger.service.ts** icinde kullanarak arka planda farkli baglamlarda loglama islemini yapabiliriz ve loglama islemini soyutlamis oluruz)
  - **ConsoleLogger**
    - Konsola basilacak loglamanin formatlamasini yapar, emoji vs ekliyor
  - **FileLogger** (ileride eklenebilir)
    - Eklendikten sonra ana loglama servisinde kullanilir
  - **Api’a loglari gonderecek bir logger** (ileride eklenebilir)
    - Eklendikten sonra ana loglama servisinde kullanilir


- Merkezi loglama servisine ek olarak HTTP trafiğini merkezi olarak izlemek icin logging interceptor da  eklenebilir (ileride)