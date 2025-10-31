

# 1) Loglama

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