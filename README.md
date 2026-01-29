# NVGT i18n

A lightweight translation system for NVGT games. Supports PO files, context-based translations, variable substitution, and runtime language switching.

## Features

- ✅ PO file format (compatible with standard translation tools)
- ✅ Context-based translations (same word, different meanings)
- ✅ Variable substitution with `{placeholder}` syntax
- ✅ Runtime language switching
- ✅ Gender support (male/female text variations)
- ✅ Arabic plural support (singular/dual/plural)
- ✅ Missing translation tracking and export
- ✅ Lightweight and easy to integrate

## Installation

Copy `i18n.nvgt` to your project and include it:

```nvgt
#include "i18n.nvgt"
```

## Quick Start

```nvgt
#include "i18n.nvgt"

void main() {
    // Initialize with language folder and default language
    i18n_init("lang", "en");
    
    // Load additional languages
    i18n_load("ar");
    
    // Use translations
    alert("", _("Hello"));
    
    // Switch language
    i18n_set_language("ar");
    alert("", _("Hello")); // Shows: مرحباً
}
```

## Translation Functions

```nvgt
// Simple translation
string text = _("Hello");

// Context: When the same word has different meanings
// Example: "Back" can mean "go back" or "person's back"
string back_button = _("Back", "navigation");  // Output: "Go Back"
string back_body = _("Back", "body");          // Output: "Back" (body part)

// Example: "Save" can mean "save file" or "rescue someone"
string save_file = _("Save", "file");    // Output: "Save"
string save_hero = _("Save", "rescue");  // Output: "Rescue"

// Translation with 1 variable
string msg1 = _f("Hello {name}", "name", "Hamad");

// Translation with 2 variables
string msg2 = _f2("Score: {score} / {max}", "score", "85", "max", "100");

// Translation with 3 variables
string msg3 = _f3("Player {name} at ({x},{y})", "name", "Ali", "x", "10", "y", "20");

// Translation with dictionary (for many variables)
dictionary@ vars = dictionary();
vars.set("name", "Sara");
vars.set("city", "Dubai");
string msg4 = _fd("Hello {name} from {city}", @vars);
```

## Gender Support

For languages like Arabic where verbs change based on gender:

```nvgt
// Set player gender (store in your game settings)
int player_gender = I18N_MALE;  // or I18N_FEMALE

// Gender-aware translation
alert("", _g("You won!", player_gender));

// Gender with variable
alert("", _gf("You collected {item}", player_gender, "item", "sword"));
```

**PO file format for gender:**
```po
msgid "You won!"
msgstr "لقد فزتَ|لقد فزتِ"

msgid "You collected {item}"
msgstr "لقد جمعتَ {item}|لقد جمعتِ {item}"
```
Use `|` to separate male|female forms.

## Arabic Plural Support

Arabic has complex plural rules. This system handles:
- **1**: singular (مفرد)
- **2**: dual (مثنى)
- **3-10**: plural (جمع)
- **11+**: singular with number

```nvgt
// Returns just the word
string word = _n(coins, "coin", "coins_dual", "coins_plural");

// Returns "count word" format
alert("", _np(0, "coin", "coins_dual", "coins_plural"));   // "0 عملات"
alert("", _np(1, "coin", "coins_dual", "coins_plural"));   // "1 عملة"
alert("", _np(2, "coin", "coins_dual", "coins_plural"));   // "2 عملتان"
alert("", _np(5, "coin", "coins_dual", "coins_plural"));   // "5 عملات"
alert("", _np(15, "coin", "coins_dual", "coins_plural"));  // "15 عملة"
```

**PO file:**
```po
msgid "coin"
msgstr "عملة"

msgid "coins_dual"
msgstr "عملتان"

msgid "coins_plural"
msgstr "عملات"
```

## Arabic Examples (Command vs Action)

Arabic is used in the examples below because it is the author's native language, and it is a great example of why **context** matters.

In Arabic (and many other languages), the correct phrasing depends on whether the text is:

- **A command / prompt to the player** (imperative verb):
    - "Open the door." → "افتح الباب."
    - "Close the tap." → "أغلق الصنبور."
- **An action label / menu-style action name** (noun/masdar):
    - "Open door" → "فتح الباب"
    - "Close tap" → "إغلاق الصنبور"

### Recommended Approach

Store **full sentences** as keys for prompts (instead of building sentences from single words) to keep grammar natural.

If you want to reuse the same English word with different meanings/styles, use `msgctxt`:

```nvgt
string cmd = _("Open", "command"); // e.g., "افتح"
string act = _("Open", "action");  // e.g., "فتح"
```

```po
msgctxt "command"
msgid "Open"
msgstr "افتح"

msgctxt "action"
msgid "Open"
msgstr "فتح"
```

## Missing Translation Tracking

```nvgt
void main() {
    i18n_init("lang", "ar");
    i18n_set_warn_missing(true);  // Enable tracking
    
    // ... your game runs ...
    // Missing translations are logged to console
    
    // Before exit, export all missing to a file
    i18n_export_missing();  // Creates: lang/missing_ar.po
}
```

## PO File Format

Create `.po` files in your `lang` folder:

### lang/en.po

```po
msgid "Hello"
msgstr "Hello"

msgid "Hello {name}"
msgstr "Hello {name}"

msgid "Goodbye"
msgstr "Goodbye"

msgctxt "music"
msgid "Play"
msgstr "Start"

msgctxt "game"
msgid "Play"
msgstr "Play"
```

### lang/ar.po

```po
msgid "Hello"
msgstr "مرحباً"

msgid "Hello {name}"
msgstr "مرحباً {name}"

msgid "Goodbye"
msgstr "مع السلامة"

msgctxt "music"
msgid "Play"
msgstr "تشغيل"

msgctxt "game"
msgid "Play"
msgstr "لعب"
```

## API Reference

### Core Functions

| Function | Description |
|----------|-------------|
| `i18n_init(path, lang)` | Initialize the system with language folder and default language |
| `i18n_load(lang)` | Load an additional language file |
| `i18n_set_language(lang)` | Switch to a different language |
| `i18n_get_language()` | Get current language code |
| `i18n_get_loaded_languages()` | Get array of all loaded language codes |

### Translation Functions

| Function | Description |
|----------|-------------|
| `_(text)` | Translate text |
| `_(text, context)` | Translate text with context |
| `_c(context, text)` | Translate with context (alternative syntax) |
| `_f(text, var, val)` | Translate with 1 variable |
| `_f2(text, v1, val1, v2, val2)` | Translate with 2 variables |
| `_f3(text, v1, val1, v2, val2, v3, val3)` | Translate with 3 variables |
| `_fd(text, dict)` | Translate with dictionary of variables |

### Gender Functions

| Function | Description |
|----------|-------------|
| `_g(text, gender)` | Translate with gender (I18N_MALE, I18N_FEMALE, I18N_NEUTRAL) |
| `_gf(text, gender, var, val)` | Gender translation with 1 variable |
| `_gf2(text, gender, v1, val1, v2, val2)` | Gender translation with 2 variables |

### Plural Functions

| Function | Description |
|----------|-------------|
| `_n(count, singular, dual, plural)` | Get plural form based on count (Arabic rules) |
| `_np(count, singular, dual, plural)` | Get "count word" format |

### Utility Functions

| Function | Description |
|----------|-------------|
| `i18n_has(text)` | Check if translation exists |
| `i18n_has(text, context)` | Check if translation with context exists |
| `i18n_count()` | Get total translation count for current language |
| `i18n_count(lang)` | Get translation count for specific language |
| `i18n_get_keys()` | Get all translation keys for current language |
| `i18n_get_keys(lang)` | Get all translation keys for specific language |
| `i18n_clear()` | Clear all loaded translations and reset system |
| `i18n_set_warn_missing(enabled)` | Enable/disable tracking of missing translations |
| `i18n_export_missing()` | Export missing translations to PO file |
| `i18n_missing_count()` | Get count of tracked missing translations |
| `i18n_clear_missing()` | Clear tracked missing translations |

## Files

```
├── i18n.nvgt           # Main translation system
├── test.nvgt           # Test suite
└── lang/
    ├── en.po           # English translations
    └── ar.po           # Arabic translations
```

## Running Tests

Run `test.nvgt` to verify everything works:

```
Tests Run: 32
Passed: 32
Failed: 0
Success Rate: 100%
```

## How This Project Started

I was working on a game in Arabic, and after many lines of code, I thought: "How can I translate this to English?"

From that moment, the i18n project was born — and it works great with my game.

Yes, there might be some bad practices or unusual code patterns — but this is what I came up with after a lot of thinking. As a foundation, you can rely on it and modify it freely.

Pull requests are very welcome! I'd be happy to receive any contributions. Thank you in advance.

## License

MIT

## Author

HmdQr

## Disclaimer

This project was developed with the assistance of GitHub Copilot powered by Claude Opus 4.5.
