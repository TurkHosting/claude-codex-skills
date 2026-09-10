# claude-codex-skills

Claude ve Codex için paylaşılabilir skill koleksiyonu.

## Yapı

Her skill kendi klasöründe tutulur:

```text
skill-name/
  SKILL.md
  references/
  agents/openai.yaml   # yalnızca gerekiyorsa
```

`SKILL.md` kısa ama yeterli giriş dosyasıdır.
Uzun standartlar, örnekler ve edge-case notları aynı skill'in `references/` klasörü altında tutulur.

Her skill iki ağaçta birden tutulur: Claude Code için `.claude/skills/`,
Codex için `.agents/skills/`. İkisi birebir aynıdır; bir skill düzenlenirken
her iki kopya da güncellenir.

### Otomatik çağrıyı kapatmak

Yalnızca kullanıcı çağırınca çalışması gereken bir skill, bunu her iki
ortamda ayrı ayrı söylemek zorundadır — `description` metni tek başına
yeterli değildir, o yalnızca modele bir tavsiyedir:

| Ortam | Yer | Ayar |
|---|---|---|
| Claude Code | `SKILL.md` frontmatter | `disable-model-invocation: true` |
| Codex CLI | `agents/openai.yaml` | `policy.allow_implicit_invocation: false` |

Codex `disable-model-invocation` alanını tanımaz, tanımadığı alanı yok sayar;
bu yüzden manuel çağrılan skill'lere `agents/openai.yaml` de eklenir.

## Skill'ler

- `filament-form-layout`
- `filament-form-table-descriptions`
- `laravel-filament-spatie-media-library`
- `laravel-order-observer-standard`
- `laravel-unique-slug`
- `ptr-abuse-check` — yalnızca `/ptr-abuse-check` ile çağrılır
- `wait-what` — yalnızca `/wait-what` ile çağrılır

## Teşekkür

`wait-what` skill'i [Matt Pocock](https://github.com/mattpocock)'un
[mattpocock/skills](https://github.com/mattpocock/skills/tree/main/skills/productivity/wait-what)
reposundaki orijinal skill'i temel alır. Orijinal İngilizce talimatlar ve
ASD-STE100 Simplified Technical English kuralı korunmuş, yalnızca yanıtın
Türkçe verilmesi şartı eklenmiştir.
Fikir ve özgün tasarım kendisine aittir.
