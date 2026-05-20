# VoiceMind

Приложение для Android, которое записывает голосовые заметки, расшифровывает их офлайн и выделяет из них главное с помощью локальной LLM.

## Что умеет

- **Запись голоса** — захват с микрофона телефона или Bluetooth-устройства
- **Офлайн-транскрипция** — речь в текст через [Whisper.cpp](https://github.com/ggerganov/whisper.cpp) (работает без интернета)
- **Выделение главного** — локальная LLM (Qwen2.5-1.5B) находит встречи, обещания, договорённости и важные решения
- **История записей** — просмотр заметок по датам, воспроизведение аудио
- **Анализ дня** — выбрать несколько записей и получить сводку по дню
- **Форматирование через Groq API** — опционально, улучшает пунктуацию и структуру текста через облако

## Стек

| Компонент | Технология |
|---|---|
| Язык | Kotlin |
| Распознавание речи | Whisper.cpp (JNI) |
| Локальная LLM | llama.cpp (JNI) + Qwen2.5-1.5B-Instruct Q4_K_M |
| GPU-ускорение | Vulkan (GGML_VULKAN) |
| Аудиозапись | AudioRecord → MediaCodec/MediaMuxer (AAC) |
| Облачное форматирование | Groq API (llama-3.1-8b-instant), опционально |
| Хранение | JSON-файлы в filesDir |
| UI | Material Design 3, ViewPager2, RecyclerView |

## Сборка

### Требования

- Android Studio Hedgehog или новее
- Android NDK (r25+)
- CMake 3.22+
- Python 3.8+ (для скриптов генерации моделей, опционально)

### Шаги

1. Клонировать репозиторий:
   ```
   git clone https://github.com/FedorSolodkin/VoiceMind-.git
   cd VoiceMind-/VoskNote
   ```

2. Открыть папку `VoskNote` в Android Studio

3. Собрать проект (`Build → Make Project`)

### Модели

Приложение при первом запуске предложит скачать LLM-модель (~900 МБ):

- **Qwen2.5-1.5B-Instruct-Q4_K_M.gguf** — основная модель для выделения главного  
  [Скачать с HuggingFace](https://huggingface.co/Qwen/Qwen2.5-1.5B-Instruct-GGUF/resolve/main/qwen2.5-1.5b-instruct-q4_k_m.gguf)

Модель Whisper (`ggml-base.bin`) для распознавания речи нужно положить в `files/` приложения или скачать из [whisper.cpp releases](https://github.com/ggerganov/whisper.cpp/tree/master/models).

### Groq API (опционально)

Если хочешь использовать облачное форматирование:

1. Получи ключ на [console.groq.com](https://console.groq.com)
2. Введи его в настройках приложения

Без ключа приложение работает полностью офлайн.

## Структура проекта

```
VoskNote/
├── app/src/main/
│   ├── java/com/example/voicemind/
│   │   ├── MainActivity.kt          — точка входа, инициализация LLM
│   │   ├── RecordFragment.kt        — запись и расшифровка
│   │   ├── HistoryFragment.kt       — история и анализ дня
│   │   ├── LlmHelper.kt             — обёртка над llama.cpp (JNI)
│   │   ├── WhisperHelper.kt         — обёртка над whisper.cpp (JNI)
│   │   ├── AudioRecordingSaver.kt   — запись AAC через MediaCodec
│   │   ├── GroqHelper.kt            — Groq API клиент
│   │   ├── RecordStorage.kt         — сохранение/чтение записей
│   │   └── ...
│   └── cpp/
│       ├── llama.cpp/               — движок LLM
│       ├── whisper.cpp/             — движок ASR
│       ├── llama_jni.cpp            — JNI-мост для LLM
│       └── whisper_jni.cpp          — JNI-мост для ASR
```

## Лицензия

MIT
