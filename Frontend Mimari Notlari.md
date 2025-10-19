
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