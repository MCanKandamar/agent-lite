# agent-lite — project-setup

## Ne işe yarar
Projeye üç dosya kurar: `AGENTS.md` (kurallar), `docs/WORK.md` (şu anki iş), `docs/PROJECT.md` (proje bilgisi).
Böylece hangi agent olursa olsun (Codex, Claude Code, Gemini, Cursor…) yeni bir session'da kaldığı yerden devam eder.
Yarıda kesilen iş git üzerinden bulunup kurtarılır. Gömülü (STM32, RTOS) projeler de desteklenir.

## Kurulum
Kanonik klasör: `~/.agents/skills/project-setup/` (`SKILL.md`, `setup-prompt.md`, `README.md`).
Claude Code bu dizini okumaz. Klasörü `~/.claude/skills/` altına da kopyala:

```powershell
New-Item -ItemType Directory -Force "$HOME\.claude\skills" | Out-Null
Copy-Item -Recurse -Force "$HOME\.agents\skills\project-setup" "$HOME\.claude\skills"
```

```bash
mkdir -p ~/.claude/skills && cp -R ~/.agents/skills/project-setup ~/.claude/skills/
```

## Projeye kurma
- **Yöntem A (skill):** Codex'te `$project-setup`, Claude Code'da `/project-setup` ya da "projeye agent-lite kur".
- **Yöntem B (skill'siz):** `setup-prompt.md` içeriğini agent'a yapıştır ya da "şu dosyayı oku ve uygula" de.

İki yöntem aynı metni kullanır, aynı dosyaları üretir. Sihirbaz soruları tek tek sorar ve repodan okuyabildiği bilgiyi sormaz.
Yarıda kalırsa tekrar başlat; kaldığı sorudan devam eder.

## Günlük kullanım
- "Projeye devam et" demek yeterli.
- Büyük işlerde agent önce hedefi ve planı `docs/WORK.md` dosyasına yazar, sonra işe başlar.
- Gömülü projelerde kartı programlama (flash) her zaman önce sorulur.

## Güncelleme
1. `~/.agents/skills/project-setup/setup-prompt.md` dosyasını düzenle.
2. Yukarıdaki kopyalama komutunu tekrar çalıştır.
3. Her projede "agent-lite'ı güncelle" de. Bu yalnızca `AGENTS.md` içindeki protokol bölümünü yeniler.

## Tercihleri değiştirme
"tercihlerimi güncelle" de. Tercihler `~/.agents/USER.md` dosyasında tutulur.

## Kaldırma
- Projeden `AGENTS.md`, `docs/WORK.md` ve `docs/PROJECT.md` silinebilir.
- Skill klasörleri (`~/.agents/skills/project-setup`, `~/.claude/skills/project-setup`) silinebilir.
