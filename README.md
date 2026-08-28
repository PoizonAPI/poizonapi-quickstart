# poizonapi-quickstart

# PoizonAPI

[![License: MIT](https://img.shields.io/badge/license-MIT-blue.svg)]()
[![API Status](https://img.shields.io/badge/status-online-brightgreen.svg)]()

**Один запрос — цена, размеры и живое наличие товара с Poizon (Dewu).**

Без захода в приложение Dewu, без нестабильных самописных парсеров, без ручной проверки цен вручную.

## Зачем это нужно

- ✅ **Экономия времени.** Менеджеры карго-компаний тратят 3-5 минут на ручную проверку каждой ссылки — API отдаёт результат за секунды.
- ✅ **Стабильность.** Самописные парсеры Poizon регулярно ломаются при изменении защиты приложения — мы берём эту головную боль на себя.
- ✅ **Честная работа с ошибками.** Если Dewu временно недоступен — вы получаете кэш с явной пометкой возраста данных, а не пустой ответ. Если товара нет — честный 404 без списания запроса.
- ✅ **Простая интеграция.** Один HTTP-запрос, JSON на выходе, никакой сложной авторизации через OAuth или подпись запросов.

## Быстрый старт

```bash
pip install requests
```

```python
import requests

response = requests.get(
    "https://poizonapi.com/api/v1/product",
    params={"article": "ЗАМЕНИТЕ_НА_АРТИКУЛ"},
    headers={"Authorization": "Bearer ВАШ_API_КЛЮЧ"}
)

data = response.json()
print(data["price"], data["sizes"], data["in_stock"])
```

Результат за один запрос — цена в юанях, доступные размеры и статус наличия.

## Установка

1. Зарегистрируйтесь на [poizonapi.com](https://poizonapi.com) и получите бесплатный пробный лимит — 300 запросов.
2. Скопируйте API-ключ из личного кабинета.
3. Используйте примеры кода ниже для интеграции в свой проект.

## Примеры использования

### Python

```python
import requests

def get_poizon_price(article: str, api_key: str) -> dict:
    url = "https://poizonapi.com/api/v1/product"
    headers = {"Authorization": f"Bearer {api_key}"}
    params = {"article": article}

    response = requests.get(url, headers=headers, params=params)

    if response.status_code == 404:
        return {"error": "Товар не найден"}

    data = response.json()
    if data.get("stale"):
        print(f"Внимание: данные из кэша, возраст {data['cache_age']}")

    return data

result = get_poizon_price("1234567", "ваш_ключ")
print(result)
```

### JavaScript / Node.js

```javascript
async function getPoizonPrice(article, apiKey) {
  const response = await fetch(
    `https://poizonapi.com/api/v1/product?article=${article}`,
    { headers: { Authorization: `Bearer ${apiKey}` } }
  );

  if (response.status === 404) {
    return { error: "Товар не найден" };
  }

  const data = await response.json();
  if (data.stale) {
    console.warn(`Данные из кэша, возраст: ${data.cache_age}`);
  }

  return data;
}
```

## Справочник по API

| Параметр | Тип | Обязательный | Описание |
|---|---|---|---|
| `article` | string | Да | Артикул товара на Poizon/Dewu |
| `Authorization` | header | Да | Bearer-токен вашего API-ключа |

**Ответ:**

| Поле | Тип | Описание |
|---|---|---|
| `price` | number | Цена в юанях |
| `sizes` | array | Список доступных размеров |
| `in_stock` | boolean | Наличие товара |
| `stale` | boolean | true, если данные из кэша (Dewu временно недоступен) |
| `cache_age` | string | Возраст кэшированных данных, если stale = true |

## Тарифы

Полное описание тарифов и пакетов запросов — на [poizonapi.com](https://poizonapi.com). Есть бесплатный пробный лимит на 300 запросов для тестирования интеграции.

## Обратная связь

Вопросы и предложения — через Telegram:@RakushkaWolf  .

## Лицензия

MIT — примеры кода в этом репозитории свободны для использования в любых проектах.
