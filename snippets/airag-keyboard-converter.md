---
title: "Thai-English Keyboard Auto-Converter"
type: wiki-entry
project: "ai-rag-agent"
createdAt: 2026-06-28T02:45:00
modifiedAt: 2026-06-28T02:45:00
target_agents:
  - frontend
related_tools:
  - react
  - typescript
tags:
  - snippet
  - utility
  - ux
---

# Thai-English Keyboard Auto-Converter

> **🤖 Agent Routing:** Frontend agents should read this for a highly reusable utility that fixes user typing errors caused by forgetting to switch keyboard languages (e.g., typing "ฟเขสำ" instead of "apple").

> **🧠 LLM Context:** This snippet extracts the `convertKeyboardLayout` logic from `ChatApp.tsx`. It uses RegEx to detect impossible character combinations and maps them to the correct language layout without needing an external API.

## Code Snippet

```typescript
const engToThaiMap: Record<string, string> = {
  '1': 'ๅ', '2': '/', '3': '-', '4': 'ภ', '5': 'ถ', '6': 'ุ', '7': 'ึ', '8': 'ค', '9': 'ต', '0': 'จ', '-': 'ข', '=': 'ช',
  'q': 'ๆ', 'w': 'ไ', 'e': 'ำ', 'r': 'พ', 't': 'ะ', 'y': 'ั', 'u': 'ี', 'i': 'ร', 'o': 'น', 'p': 'ย', '[': 'บ', ']': 'ล', '\\': 'ฃ',
  'a': 'ฟ', 's': 'ห', 'd': 'ก', 'f': 'ด', 'g': 'เ', 'h': '้', 'j': '่', 'k': 'า', 'l': 'ส', ';': 'ว', '\\'': 'ง',
  'z': 'ผ', 'x': 'ป', 'c': 'แ', 'v': 'อ', 'b': 'ิ', 'n': 'ื', 'm': 'ท', ',': 'ม', '.': 'ใ', '/': 'ฝ',
  '!': '+', '@': '๑', '#': '๒', '$': '๓', '%': '๔', '^': 'ู', '&': '฿', '*': '๕', '(': '๖', ')': '๗', '_': '๘', '+': '๙',
  'Q': '๐', 'W': '"', 'E': 'ฎ', 'R': 'ฑ', 'T': 'ธ', 'Y': 'ํ', 'U': '๊', 'I': 'ณ', 'O': 'ฯ', 'P': 'ญ', '{': 'ฐ', '}': ',', '|': 'ฅ',
  'A': 'ฤ', 'S': 'ฆ', 'D': 'ฏ', 'F': 'โ', 'G': 'ฌ', 'H': '็', 'J': '๋', 'K': 'ษ', 'L': 'ศ', ':': 'ซ', '"': '.',
  'Z': '(', 'X': ')', 'C': 'ฉ', 'V': 'ฮ', 'B': 'ฺ', 'N': '์', 'M': '?', '<': 'ฒ', '>': 'ฬ', '?': 'ฦ'
};

const thaiToEngMap: Record<string, string> = Object.entries(engToThaiMap).reduce((acc, [eng, thai]) => {
  acc[thai] = eng;
  return acc;
}, {} as Record<string, string>);

const isInvalidThaiPattern = (text: string) =>
  /^[ัิีึืุู่้๊๋์็ะาำ]/.test(text) ||
  /[เแโใไ][ะาำเแโใไัิีึืุู่้๊๋์็]/.test(text) ||
  /[เแโใไ]$/.test(text) ||
  /[ัิีึืุู][ัิีึืุู]/.test(text) ||
  /[่้๊๋][่้๊๋]/.test(text) ||
  /[ัิีึืุู][ำ]/.test(text) ||
  /[ะา][ะาำ]/.test(text) ||
  /(ยย|ฟฟ|สส|หห|ดด|พพ|ะะ|าา|ิิ|ีี|ึึ|ืื|ุุ|ูู|เเ|แแ|โโ|ใใ|ไไ)/.test(text);

const convertWordEngToThai = (word: string) => {
  if (word.length === 0) return word;
  if (/[\\u0E00-\\u0E7F]/.test(word)) return word;

  const isLikelyMistake =
    /[a-zA-Z][0-9\\-=\\[\\];',.\\/][a-zA-Z]/.test(word) ||
    /[bcdfghjklmnpqrstvwxyzBCDFGHJKLMNPQRSTVWXYZ]{4,}/.test(word) ||
    word.length > 7;

  if (!isLikelyMistake) return word;

  const mapped = word.split('').map(char => engToThaiMap[char] || char).join('');
  return isInvalidThaiPattern(mapped) ? word : mapped;
};

const convertWordThaiToEng = (word: string) => {
  if (word.length === 0) return word;
  if (!/[\\u0E00-\\u0E7F]/.test(word)) return word;

  if (!isInvalidThaiPattern(word)) return word;

  const mapped = word.split('').map(char => thaiToEngMap[char] || char).join('');

  if (!/[aeiouyAEIOUY]/.test(mapped)) return word;

  return mapped;
};

export const convertKeyboardLayout = (text: string) => {
  const words = text.split(/(\\s+)/);
  return words.map(word => {
    if (word.trim() === '') return word;

    const thaiCharCount = (word.match(/[\\u0E00-\\u0E7F]/g) || []).length;
    const engCharCount = (word.match(/[a-zA-Z]/g) || []).length;

    if (engCharCount > 0 && thaiCharCount === 0) {
      return convertWordEngToThai(word);
    } else if (thaiCharCount > 0 && engCharCount === 0) {
      return convertWordThaiToEng(word);
    }
    return word;
  }).join('');
};
```

## 🔗 Related Context
- [[airag-architecture]]
