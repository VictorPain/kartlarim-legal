# kartlarim-legal

Kartlarım mobil uygulamasının gizlilik politikası, KVKK aydınlatma metni ve kullanıcı sözleşmesinin yayınlandığı statik GitHub Pages reposu.

**Canlı yayın:** https://victorpain.github.io/kartlarim-legal/

## Yapı

```
kartlarim-legal/
├── index.html          → TR + EN ana sayfa (link listesi)
├── privacy.html        → Gizlilik Politikası (TR)
├── kvkk.html           → KVKK Aydınlatma Metni (TR)
├── terms.html          → Kullanıcı Sözleşmesi (TR)
├── hesap-silme.html    → Hesap ve Veri Silme (TR) — Play zorunluluğu
├── en/
│   ├── privacy.html    → Privacy Policy (EN)
│   ├── kvkk.html       → Personal Data Notice (EN)
│   ├── terms.html      → Terms of Service (EN)
│   └── hesap-silme.html → Account and Data Deletion (EN)
├── build.mjs           → Wiki markdown → HTML build script
└── README.md
```

## Kaynak (Single Source of Truth)

Tüm yasal metinlerin **tek doğruluk kaynağı** sibling `sanalFatih` wiki'sidir:

```
../sanalFatih/wiki/projeler/kartlarim/yasal/
├── privacy-policy-tr.md
├── privacy-policy-en.md
├── kvkk-aydinlatma-metni.md
├── kvkk-aydinlatma-metni-en.md
├── kullanici-sozlesmesi.md
└── kullanici-sozlesmesi-en.md
```

HTML'ler bu markdown'lardan **otomatik üretilir**; **HTML'i elle düzenleme.**

## Yeniden Yayın

Wiki metinlerinde değişiklik yapıldıktan sonra:

```bash
cd kartlarim-legal
node build.mjs               # 7 HTML üret (index + 3 TR + 3 EN)
git add -A
git commit -m "legal: rebuild — <kısa açıklama>"
git push                     # GitHub Pages otomatik build (~1 dk, aktifse)
```

## GitHub Pages — nasıl çalışıyor, ne zaman ne yaparsın

Yayın **2026-08-16'da açıldı.** Ayar: `main` dalı, `/ (root)` klasörü, HTTPS zorunlu.
Repo Public olmak zorunda — Pages private repo'da ücretsiz planda yayınlanmaz.

### Durum kontrolü

```bash
gh api repos/VictorPain/kartlarim-legal/pages --jq '.html_url, .source.branch, .https_enforced'
gh api repos/VictorPain/kartlarim-legal/pages/builds/latest --jq '.status, .error.message'
```

`status: built` beklenen sonuç. `errored` görürsen `.error.message` nedeni söyler.

### Yayın akışı

`push` → GitHub otomatik build → **~1 dakika** içinde canlı. Ayrı bir deploy adımı yok.
HTML'ler `build.mjs` ile üretilir; **elle düzenlenmez** (bir sonraki build ezer).

```bash
# 1. Metni wiki'de değiştir (tek doğruluk kaynağı)
#    ../sanalFatih/wiki/projeler/kartlarim/yasal/*.md
# 2. HTML üret
cd kartlarim-legal && node build.mjs
# 3. Yayınla
git add -A && git commit -m "legal: rebuild — <kısa açıklama>" && git push
# 4. ~1 dk sonra doğrula
curl -s -o /dev/null -w "%{http_code}\n" https://victorpain.github.io/kartlarim-legal/kvkk.html
```

### Yeni sayfa eklerken

`build.mjs` içinde üç yer güncellenir: `PAGES` dizisi (kaynak md + çıktı adı),
`nav()` içindeki `tips` etiket haritası, `indexHtml()` içindeki link listesi.
`tip` değeri çıktı dosya adıyla aynı olmalı (`hesap-silme` → `hesap-silme.html`),
çünkü nav linkleri `${tip}.html` olarak üretiliyor.

### Bu URL'lerin bağlı olduğu yerler

| Nerede | Ne için |
|---|---|
| Play Console → Store listing | Privacy Policy URL (**zorunlu**) |
| Play Console → App content | Veri silme talebi URL'i → `hesap-silme.html` (**zorunlu**) |
| App Store Connect → App Information | Privacy Policy URL (**zorunlu**) |
| Uygulama → `src/app/legal-urls.ts` | `BASE` sabiti; onboarding onay modalı ve Profil → Yasal buradan açılır |
| Paywall (premium modal) | Apple 3.1.2 — EULA + Gizlilik linkleri |

Site adresi değişirse **tek düzeltme noktası** `legal-urls.ts` içindeki `BASE`
sabitidir; ayrıca iki konsoldaki URL alanları elle güncellenir.

### Sık karşılaşılan iki sorun

- **404 alıyorum ama dosya repo'da var.** Build henüz bitmemiş olabilir (~1 dk),
  ya da Pages hiç açılmamıştır: `gh api .../pages` 404 dönüyorsa yayın kapalıdır.
  Açmak için: `gh api -X POST repos/VictorPain/kartlarim-legal/pages -f 'source[branch]=main' -f 'source[path]=/'`
- **Metni değiştirdim ama sitede eski hâli duruyor.** Wiki'yi değiştirip
  `node build.mjs` çalıştırmayı atlamışsındır — commit'lenen HTML eski kalır.

## Bağımlılık

- Node.js 20+
- `marked@13` — `npx --yes` ile otomatik indirilir, kalıcı kurulum gerekmez

## Versiyon Geçmişi

- **v3 (2026-08-16):** yayın öncesi kod denetimi hizalaması — Crashlytics/Analytics beyandan çıkarıldı (kullanılmıyor), e-posta/parola ve misafir giriş yolları eklendi, yerel saklama teknolojisi düzeltildi (SQLite/Keychain iddiası gerçek değildi), yurt dışı aktarım dayanağı KVKK m.9/2-a standart sözleşme oldu, ui-avatars + favicon aktarımları beyan edildi, abonelik otomatik yenileme cümlesi eklendi, `hesap-silme.html` (TR+EN) eklendi

- **v2 (2026-05-26):** local-first mimari geçişi — Cloud Sync iptal, yedekleme kullanıcı bulutuna, KVKK ve sorumluluk metinleri yeniden yazıldı, EN versiyonları eklendi
- **v1 (2026-05-05):** İlk yayın — Cloud Sync / Türkiye sunucusu varsayımıyla yazılmış metinler

## Mimari Pattern

Bu repo yapısı [`mashop-legal`](https://github.com/VictorPain/mashop-legal)'dan port edilmiştir. Single source of truth + build.mjs + dil-aware nav + inline CSS + dark mode aynı şablon.

## İçerik Sahibi

**Fatih Acı** — `fatihaci79@gmail.com`

Bu içerik telif korumalıdır.

## Lawyer Review

EN versiyonları AI çevirisidir; **yayın öncesi İngilizce dilinde lisanslı bir privacy avukatı tarafından review edilmelidir.** TR versiyonları Türkçe hukuki bağlayıcılığa sahiptir; TR metinleri de KVKK uzmanı bir avukat tarafından gözden geçirilmelidir (özellikle Terms m.5 sorumluluk reddi cümleleri, TBK m.115 uyumu).
