> [docs/01-getting-started.md](../../docs/01-getting-started.md) çevirisidir — İngilizce sürüm esastır. / Translation of the English README, which is the source of truth.

# 1. Başlarken

> **Son doğrulama:** Claude Code v2.1.205 · Temmuz 2026. Davranış değişti mi? [Bildir](https://github.com/omerbberk/claude-code-field-guide/issues) — bir bölümü yeniden doğrulamak harika bir ilk PR'dır.

## Kurulum

**Gereksinimler:** macOS, Linux, veya Windows (native ya da WSL). Native yükleyici için Node.js gerekmez; npm yolu için Node 22+ gerekir.

```bash
# Native installer — önerilen (macOS / Linux / WSL)
curl -fsSL https://claude.ai/install.sh | bash

# Windows PowerShell
irm https://claude.ai/install.ps1 | iex

# npm (Node 22+ gerektirir)
npm install -g @anthropic-ai/claude-code

# Homebrew (güncellemeler `brew upgrade` ile yapılır, otomatik değil)
brew install --cask claude-code
```

Doğrulama:

```bash
claude --version
```

Native install kendini otomatik olarak günceller; Homebrew/npm kurulumları ise manuel olarak güncellenir (`brew upgrade claude-code` / `npm install -g @anthropic-ai/claude-code@latest`). `claude update` manuel olarak bir güncelleme tetikler.

## Kimlik doğrulama

Herhangi bir klasörde `claude` komutunu çalıştırın. İlk açılışta şunlardan birini seçersiniz:

1. **Claude aboneliği (Pro, Max, Team, ya da Enterprise)** — claude.ai hesabınızla giriş yapın. Kullanım planınıza dahildir. Bireysel kullanım için en basit seçenek budur.
2. **Claude Console / API anahtarı** — kullandıkça öde API faturalandırması. Ekipler ve otomasyon için uygundur.
3. **Enterprise sağlayıcılar** — Amazon Bedrock veya Google Vertex AI, ortam değişkenleriyle yapılandırılır.

> Hangi planı seçmeli? Hafif günlük kullanım için Pro yeterlidir. Max (5x / 20x) yoğun günlük kullanım içindir — büyük refaktörler, uzun oturumlar, birden fazla paralel ajan. API faturalandırma, token başına ücretlendirme istediğiniz CI ve script tabanlı kullanımlar için mantıklıdır.

## İlk oturumunuz

```bash
cd your-project
claude
```

İlk beş dakikada deneyebilecekleriniz:

```
> give me an overview of this codebase
> what does the deploy script actually do?
> /init
```

`/init` projeyi tarar ve bir `CLAUDE.md` dosyası oluşturur — projenin hafızası. Bunu commit'leyin. (Daha fazlası [bölüm 4](04-context-claude-md-memory.md)'te.)

## İzin modeli

Claude Code yan etkileri olan herhangi bir işlem yapmadan önce size sorar. Bir dosyayı değiştirmek istediğinde veya bir komut çalıştırmak istediğinde **Yes**, **Yes, and don't ask again for this command**, ve **No, tell Claude what to do differently** gibi seçeneklerle bir prompt görürsünüz.

Oturum sırasında **Shift+Tab** ile değiştirilebilen çeşitli izin *modları* vardır:

| Mod | Davranış | Ne zaman kullanılır |
|------|----------|-------------|
| **Normal** | Her yazma/komut için sorar | Varsayılan; tanıdık olmayan kod tabanları |
| **Auto-accept edits** | Dosya düzenlemeleri otomatik ilerler, komutlar yine sorar | Güvenilen repo'lar, akış halinde çalışırken |
| **Plan mode** | Salt okunur: Claude araştırır ve bir plan önerir, hiçbir şeye dokunmaz | Büyük değişiklikler, mimari kararları |
| **Bypass permissions** | Hiç prompt olmaz (`--dangerously-skip-permissions`) | Yalnızca sandbox/container içinde — bkz. [Güvenlik](11-security-best-practices.md) |

**Plan mode hafife alınmamalı.** Basit bir bug fix'ten büyük her değişiklik için Shift+Tab ile plan mode'a geçin, ne istediğinizi anlatın, planı gözden geçirin, *sonra* çalıştırmasına izin verin. Yanlış anlamaları 40 farklı dosya değiştirilmeden önce yakalarsınız.

## Temel tuş ve komutlar

| Tuş / komut | Ne işe yarar |
|---|---|
| `Esc` | Claude'u işlem sırasında kesintiye uğratır (güvenli — durur ve bekler) |
| `Esc Esc` / `/rewind` | Konuşmayı *ve/veya kodu* önceki bir kontrol noktasına geri alır |
| `Shift+Tab` | İzin modları arasında geçiş yapar |
| `Ctrl+C` | input'u iptal eder / çıkar |
| `!command` | Kendi shell komutunuzu çalıştırır, çıktı konuşmaya yansır |
| `#some note` | CLAUDE.md'ye hızlıca bir hafıza notu ekler |
| `@path/to/file` | Prompt içinde doğrudan bir dosyaya referans verir |
| `/help` | Tüm komutları listeler |
| `/clear` | Konuşmayı temizler, sıfırdan başlar (CLAUDE.md context'ini korur) |
| `/compact` | Context alanı açmak için konuşmayı özetler |
| `/model` | Model değiştirir (Örn. Opus vs Sonnet vs Haiku) |
| `/usage` | Plan limitlerini ve token kullanımını gösterir (`/cost` bir alias'tır) |

## Bilinmesi gereken CLI flag'leri

```bash
claude                        # interaktif oturum
claude "fix the failing test" # başlangıç promptu ile başlar
claude -p "explain db.py"     # print modu: cevapla ve çık (script'ler için ideal)
claude -c                     # en son konuşmaya devam eder
claude -r                     # resume: eski bir konuşma seçer
claude --model claude-sonnet-5   # bir modeli sabitler
```

## Neler nerede bulunur

| Yol | Amaç |
|---|---|
| `~/.claude/settings.json` | Global ayarlarınız (tüm projeler) |
| `.claude/settings.json` | Proje ayarları, git'e commitlenir — ekibinizle paylaşılır |
| `.claude/settings.local.json` | Proje ayarları, git-ignored — kişisel |
| `CLAUDE.md` | Proje hafızası (bölüm 4) |
| `.claude/skills/` | Özel slash komutları / skill'ler (bölüm 5) |
| `.claude/agents/` | Özel subagent'lar (bölüm 5) |
| `~/.claude/projects/` | Proje başına konuşma geçmişi |

## Sırada

→ [Bölüm 2: Temel Kavramlar](02-core-concepts.md) — *nasıl* çalıştığını anlayın ki ne yapacağını öngörebilin.
