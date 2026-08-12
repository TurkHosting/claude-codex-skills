# claude-codex-skills

Claude ve Codex için paylaşılabilir skill koleksiyonu.

## Yapı

Her skill kendi klasöründe tutulur:

```text
skill-name/
  SKILL.md
  references/
```

`SKILL.md` kısa ama yeterli giriş dosyasıdır.
Uzun standartlar, örnekler ve edge-case notları aynı skill'in `references/` klasörü altında tutulur.

## Skill'ler

- `filament-form-layout`
- `filament-form-table-descriptions`
- `laravel-filament-spatie-media-library`
- `laravel-order-observer-standard`
- `laravel-unique-slug`
- `wait-what`

## Teşekkür

`wait-what` skill'i [Matt Pocock](https://github.com/mattpocock)'un
[mattpocock/skills](https://github.com/mattpocock/skills/tree/main/skills/productivity/wait-what)
reposundaki orijinal skill'inden ilham alınarak Türkçe'ye uyarlanmıştır.
Orijinaldeki ASD-STE100 Simplified Technical English kuralı, aynı prensipler
korunarak sade teknik Türkçe karşılığıyla değiştirilmiştir.
Fikir ve özgün tasarım kendisine aittir.
