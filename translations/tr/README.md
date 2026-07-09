# How It Works: Claude Code 🛠️

> [README.md](../../README.md) çevirisidir — İngilizce sürüm esastır. / Translation of the English README, which is the source of truth.

**Claude Code için pratik, lafı dolandırmayan bir saha rehberi — terminalinizde yaşayan yapay zekâ kodlama ajanı.**

Bu repo, "bir yapay zekâyla sohbet etmenin" ötesine geçip onunla gerçekten *çalışmak* isteyenler için yazıldı: gerçek kod tabanlarını refactor etmek, sıkıcı işleri otomatikleştirmek, onu araçlarınıza bağlamak ve kalıcı iş akışları kurmak.

> Buradaki her şey placeholder değerler kullanır (`YOUR_API_KEY`, `your-project` vb.). Gerçek secret'ları asla commit'lemeyin — bkz. [Güvenlik](../../docs/11-security-best-practices.md).

---

## Claude Code nedir?

Claude Code, Anthropic'in ajan tabanlı kodlama aracıdır. Kod parçalarını bir sohbet penceresine kopyala-yapıştır yapmak yerine, projenizin içinde `claude` komutunu çalıştırırsınız ve o:

- **Kod tabanınızı okur** — arar, dosyaları açar, kendi anlayışını kurar.
- **Dosyaları düzenler ve komut çalıştırır** — izninizle kod yazar, testleri koşturur, bozulanı düzeltir, yineler.
- **Git'i bir takım arkadaşı gibi kullanır** — commit'ler, branch'ler, PR açıklamaları, kod incelemesi.
- **Kendini genişletir** — hook'lar, skill'ler, subagent'lar ve MCP sunucularıyla neredeyse her şeye bağlanır.

Terminal CLI'ı, masaüstü uygulaması, web uygulaması ve IDE eklentileri (VS Code, JetBrains) olarak kullanılabilir.

## Hızlı başlangıç (60 saniye)

```bash
# Kurulum (macOS / Linux / WSL — Windows PowerShell: irm https://claude.ai/install.ps1 | iex)
curl -fsSL https://claude.ai/install.sh | bash

# Herhangi bir projeye gidin ve başlatın
cd your-project
claude
```

İlk çalıştırma sizi giriş adımlarından geçirir (Claude Pro/Max aboneliği veya API anahtarı). Sonra onunla konuşmanız yeterli:

```
> bu proje ne yapıyor, anlat
> kullanıcı doğrulaması nerede yapılıyor? rate limiting ekle
> testleri çalıştır, patlayanları düzelt
```

## İçindekiler

Bölümlerin tamamı şimdilik İngilizce — [`docs/`](../../docs/) klasöründe. Türkçe bölüm çevirileri için PR'lar açığız: [çeviri rehberi](../README.md).

| # | Bölüm | Ne öğreneceksiniz |
|---|---------|-------------------|
| 1 | [Getting Started](../../docs/01-getting-started.md) | Kurulum, giriş, ilk oturum, izin modları |
| 2 | [Core Concepts](../../docs/02-core-concepts.md) | Ajan döngüsü, araçlar, context window — *gerçekte* nasıl çalıştığı |
| 3 | [Everyday Workflows](../../docs/03-everyday-workflows.md) | Debug, refactor, test, git, kod incelemesi |
| 4 | [CLAUDE.md & Memory](../../docs/04-context-claude-md-memory.md) | Projenizi ve tercihlerinizi bir kez öğretmek |
| 5 | [Slash Commands & Skills](../../docs/05-slash-commands-and-skills.md) | Hazır komutlar ve kendi komutlarınızı yazmak |
| 6 | [Hooks](../../docs/06-hooks.md) | Ajanın eylemleri etrafında garantili otomasyon |
| 7 | [MCP & Integrations](../../docs/07-mcp-integrations.md) | Hangi sunucuları bağlamalı, neden |
| 8 | [Obsidian Workflows](../../docs/08-obsidian-workflows.md) | Vault'unuzu ajanın okuyup yazdığı bilgi tabanına çevirmek |
| 9 | [Automation & Headless](../../docs/09-automation-and-headless.md) | CI, GitHub Actions, cron, script'ler |
| 10 | [Tips, Tricks & Cost](../../docs/10-tips-tricks-cost.md) | Zaman ve para kazandıran alışkanlıklar |
| 11 | [Security Best Practices](../../docs/11-security-best-practices.md) | Secret'lar, izinler, güvende kalmak |
| 12 | [Agents & Delegation](../../docs/12-agents-and-delegation.md) | Subagent'lar, roller, teknik lider gibi çalışmak |
| 13 | [Token Efficiency](../../docs/13-token-efficiency.md) | Token başına daha çok iş |

## Hazır örnekler

[`examples/`](../../examples/) klasöründeki dosyaları doğrudan kendi projelerinize kopyalayabilirsiniz: örnek `CLAUDE.md`, izin + hook ayarları, hazır hook'lar, bir `/changelog` skill'i ve bir code-reviewer subagent tanımı.

## Topluluk tarifleri

[`recipes/`](../../recipes/) gerçek kullanıcılardan kısa iş akışları içerir — ilk katkınızı yapmanın en kolay yolu: **bir dosya, bir tarif, bir PR** ([şablon](../../recipes/_template.md)).

## Bu rehber kimin için?

- **Yeni başlayanlar** — 1–3. bölümler sizi sıfırdan üretkenliğe taşır.
- **Günlük kullanıcılar** — asıl kaldıraç 4–7. bölümlerde.
- **Kurcalamayı sevenler** — 8–9. bölümler ajanı sisteminizin parçası yapar.
- **İleri kullanıcılar** — 12–13: bir ajan ekibi yönetin, hem de limitlerinizi yakmadan.

## Katkıda bulunma

Hata mı buldunuz? Paylaşmaya değer bir iş akışınız mı var? PR'lara çok açığız — bkz. [CONTRIBUTING.md](../../CONTRIBUTING.md). Türkçe bölüm çevirileri de aranan katkılardan.

## Lisans

[MIT](../../LICENSE) — dilediğiniz gibi kullanın.
