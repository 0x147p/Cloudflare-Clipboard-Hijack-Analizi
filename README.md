# Sahte CAPTCHA / Cloudflare Doğrulaması – Clipboard Hijack Analizi

> **Amaç:** “Cloudflare / reCAPTCHA doğrulaması” gibi görünen ama kullanıcıyı **Win+R → Ctrl+V → Enter** akışıyla komut çalıştırmaya ikna eden saldırıların teknik analizini ve farkındalık materyallerini paylaşmak.  
> **Not:** Bu repo **zararlı davranış içermez**; IOC’ler redakte edilmiştir.

## Özet

Bu saldırı sınıfı genelde şu şekilde çalışır:
- Sayfa, gerçek bir doğrulama (Cloudflare/reCAPTCHA) gibi görünür.
- Kullanıcıdan “doğrulama” adı altında **Çalıştır (Win+R)** penceresini açması ve panodakini yapıştırıp çalıştırması istenir.
- Tıklama anında sayfa, panoya **komut** basarak kullanıcıyı fark etmeden “yapıştır + çalıştır” yaptırır.

Bu yöntem; **clipboard hijack + sosyal mühendislik** birleşimidir.

## Teknik Davranışlar (yüksek seviye)

Aşağıdaki davranışlar sahte doğrulama sayfalarında sık görülür:

- **Platform hedefleme:** `navigator.userAgent` ile Windows kullanıcılarını seçme / diğerlerini farklı sayfaya yönlendirme
- **Clipboard manipülasyonu:** görünmez textarea + `execCommand('copy')` veya `copy` event’ini yakalayıp panoya içerik basma
- **Sahte UI akışı:** “Win+R → Ctrl+V → Enter” adımlarını “doğrulama” gibi sunma
- **Taklit (impersonation):** URL parametreleriyle sahte domain/ikon gösterimi; favicon/ikon servislerinden logo çekme
- **Güven hissi üretme:** “Verification ID”, “Ray ID” gibi değerleri rastgele üretip meşruiyet algısı yaratma

## Neden Tehlikeli?

- Win+R ile açılan pencereye yapıştırılan komut; kullanıcı fark etmeden **indir/kurdur/çalıştır** zincirini tetikleyebilir.
- Saldırganlar çoğu zaman **sessiz kurulum** parametreleri kullanır (kullanıcı “hiçbir şey olmadı” sanabilir).

## IOC’ler (Redakte)

> Paylaşırken zararlı alan adlarını **hxxp** ve **[.]** formatında redakte edin.

- Yönlendirme: `t[.]me/<REDACTED>`
- İndirme kaynağı: `hxxp://<REDACTED-DOMAIN>/<REDACTED-PATH>`
- Çalıştırılan komut: `<REDACTED-COMMAND>`
 
## Savunma / Önlem Önerileri

### Kullanıcı Farkındalığı
- “Doğrulama” adı altında **Win+R** isteyen hiçbir sayfaya güvenmeyin.
- Tarayıcıda “clipboard” ile ilgili beklenmedik davranış görürseniz sekmeyi kapatın.

### SOC / EDR İçin
- Windows süreçlerinde `msiexec`, `powershell`, `cmd`, `rundll32` gibi ikililerin **şüpheli komut satırı** ile çalıştırılmasını alarmlayın.
- Tarayıcı kaynaklı zincirlerde **process tree** korelasyonu yapın (browser → msiexec vb.)

## Güvenli Paylaşım (Önemli)

Bu repo **savunma ve farkındalık** içindir.  
- Zararlı komut/URL/payload **paylaşmayın**  
- İçerikleri eğitim amaçlı ve redakte şekilde yayınlayın  
- Gerçek kurum/marka taklidi yapacak şekilde kullanmayın
 
## Sorumlu Kullanım / Disclaimer

Bu içerik yalnızca **güvenlik farkındalığı, analiz ve savunma** amaçlıdır.  
Her türlü kötüye kullanım sorumluluğu kullanıcıya aittir.
