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
├── en/
│   ├── privacy.html    → Privacy Policy (EN)
│   ├── kvkk.html       → Personal Data Notice (EN)
│   └── terms.html      → Terms of Service (EN)
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

## Bağımlılık

- Node.js 20+
- `marked@13` — `npx --yes` ile otomatik indirilir, kalıcı kurulum gerekmez

## Versiyon Geçmişi

- **v2 (2026-05-26):** local-first mimari geçişi — Cloud Sync iptal, yedekleme kullanıcı bulutuna, KVKK ve sorumluluk metinleri yeniden yazıldı, EN versiyonları eklendi
- **v1 (2026-05-05):** İlk yayın — Cloud Sync / Türkiye sunucusu varsayımıyla yazılmış metinler

## Mimari Pattern

Bu repo yapısı [`mashop-legal`](https://github.com/VictorPain/mashop-legal)'dan port edilmiştir. Single source of truth + build.mjs + dil-aware nav + inline CSS + dark mode aynı şablon.

## İçerik Sahibi

**Fatih Acı** — `fatihaci79@gmail.com`

Bu içerik telif korumalıdır.

## Lawyer Review

EN versiyonları AI çevirisidir; **yayın öncesi İngilizce dilinde lisanslı bir privacy avukatı tarafından review edilmelidir.** TR versiyonları Türkçe hukuki bağlayıcılığa sahiptir; TR metinleri de KVKK uzmanı bir avukat tarafından gözden geçirilmelidir (özellikle Terms m.5 sorumluluk reddi cümleleri, TBK m.115 uyumu).
