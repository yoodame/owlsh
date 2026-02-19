# Text-to-Speech for Early Readers

## Why

Kids learning sight words (ages 4-7) can't read all the on-screen prompts, instructions, or game feedback. The app should read aloud to them so they can play independently.

## Approach: Pre-generated Audio Files

Use an open-source TTS model offline to generate audio files, served as static assets. No runtime TTS, no API keys, no network dependency.

### Generation

- **Tool**: [Piper TTS](https://github.com/rhasspy/piper) (`pip install piper-tts`) — fast, offline, good quality English voices
- **Script**: `scripts/generate-audio.py`
  - Parses `index.html` to extract all 220 words from the `WORDS` object
  - Generates one `.mp3` per word → `audio/words/{word}.mp3`
  - Generates prompt audio → `audio/prompts/` (e.g. `type-the-word.mp3`, `fill-in-the-blank.mp3`, `try-again.mp3`)
  - Reports missing files and word count mismatches
  - Idempotent — only regenerates if source word list changed or file missing
- **Voice**: Pick a clear, warm, child-friendly English voice (e.g. `en_US-lessac-medium`)
- **Format**: mp3, mono, 22kHz — keeps files small (~3-5KB per word, ~1-2MB total)

### Audio files to generate

| Category | Files | Example |
|----------|-------|---------|
| Sight words | 220 | `audio/words/the.mp3`, `audio/words/jump.mp3` |
| Game prompts | ~10 | `type-the-word.mp3`, `fill-in-the-blank.mp3`, `unscramble-the-letters.mp3` |
| Feedback | ~6 | `correct.mp3`, `try-again.mp3`, `great-job.mp3` |
| Navigation | ~5 | `press-enter-to-begin.mp3`, `pick-your-avatar.mp3` |

### Integration in `index.html`

```javascript
// Preload word audio on game start
function speakWord(word) {
  if (muted) return;
  const audio = new Audio(`audio/words/${word}.mp3`);
  audio.play().catch(() => {
    console.warn(`Missing audio: audio/words/${word}.mp3`);
  });
}

function speakPrompt(key) {
  if (muted) return;
  const audio = new Audio(`audio/prompts/${key}.mp3`);
  audio.play().catch(() => {
    console.warn(`Missing audio: audio/prompts/${key}.mp3`);
  });
}
```

### Where to speak

- **Flash mode**: speak the word when it appears, speak again on reveal
- **Fill / Scramble / Story**: speak the word after correct answer
- **Feedback**: "Correct!" / "Try again!"
- **Welcome screen**: "Press enter to begin" (once)
- **Game prompts**: "Type the word", "Fill in the blank", etc.

### Missing file detection

- `speakWord()` / `speakPrompt()` log a `console.warn` on 404
- Generation script prints a summary: `220/220 words, 21/21 prompts — all good` or lists missing
- Optional: add a `audio/manifest.json` listing all files, check at build time

### Pre-deploy check

Add to deploy workflow or run manually:

```bash
python scripts/generate-audio.py --check
# Exits non-zero if any words are missing audio files
```

### Service worker

Update `sw.js` to cache audio files on first fetch (already does this with the cache-on-fetch strategy).

## Open questions

- Should sentences in Story mode be read aloud? (660 sentences = more files, or use Web Speech API as fallback for dynamic text)
- Speaker icon button next to words for "tap to hear" vs auto-play?
- Volume level relative to the 8-bit music?
