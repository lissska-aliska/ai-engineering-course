# ai-engineering-course
## Troubleshooting

### 1. Ошибка `'ascii' codec can't encode characters`
**Что видел:** После замены ключа GigaChat на сломанный скрипт вывел `Invalid credentials format...` и ошибку `'ascii' codec can't encode characters in position 7-15`.
**Почему:** В переменную `GIGACHAT_CREDENTIALS` попали русские буквы (слово "сломанный_ключ"), а HTTP-заголовки, куда отправляется ключ, поддерживают только латиницу (ASCII). Также это может случиться, если в ключе есть лишние пробелы или кавычки.
**Что сделал:** Вернул настоящий ключ (только латиница и цифры). Убедился, что fallback сработал и скрипт не упал, а получил ответ от резервного провайдера.

### 2. Ошибка `402 You have no remaining credits`
**Что видел:** При проверке fallback (со сломанным ключом GigaChat) скрипт переключился на HuggingFace, но вывел ошибку `Error code: 402 - You have no remaining credits`.
**Почему:** На аккаунте HuggingFace закончились бесплатные кредиты для выбранной тяжелой модели (`meta-llama/Llama-3.3-70B-Instruct`). Бесплатные лимиты на такие модели очень ограничены.
**Что сделал:** Заменил модель в `.env` на более легкую (`meta-llama/Llama-3.2-3B-Instruct`), чтобы уложиться в бесплатные лимиты и успешно протестировать резервного провайдера.

### 3. Ошибка `cannot unpack non-iterable NoneType object`
**Что видел:** Скрипт падал после успешного ответа GigaChat с ошибкой `cannot unpack non-iterable NoneType object`.
**Почему:** При редактировании функции `ask_llm` были случайно удалены или сдвинуты строки `latency = ...` и `return result, provider, latency`. Функция возвращала `None` вместо кортежа из трех элементов, а `main` пытался их распаковать.
**Что сделал:** Восстановил строки `latency = time.time() - start` и `return result, provider, latency` в конце функции `ask_llm` с правильными отступами.