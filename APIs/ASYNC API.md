#api #python
## Понятие ASYNC API

ASYNC API - это язык описания для _проектирования и документирования асинхронных API_.
Он аналогичен `OpenAPI` (Swagger), но использует асинхронные паттерны взаимодействия (сообщения, события, потоки данных)

## Создание AsyncAPI в Python
*Библиотека: `asyncapi`*

### Создание описания ASYNC API
Создайте файл с описанием вашего API в формате .json (.yaml для дебилов). Вы должны определить каналы, сообщения, события и другие аспекты вашей "асинхронизации".

Пример на .json:

```json
{
	"asyncapi": "2.0.0",
	"info": { 
		"title": "My Async API",
		"version": "1.0.0" 
	},
	"channels": {
		"user_created": {
			"publish": {
				"message": {
					"$ref": "#/components/messages/UserCreated"
				}
			}
		}
	},
	"components": {
		"messages": {
			"UserCreated": {
				"payload": {
					"type": "object",
					"properties": {
						"id": {
							"type": "string"
						},
						"username": {
							"type": "string"
						}
					}
				}
			}
		}
	}
}
```

В этом примере описывается асинхронное API с одним каналом "user_created". В канале определено одно сообщение "UserCreated", которое содержит поля "id" и "username".

## Чтение и обработка описания
Прочитайте описания с помощью вышеуказанной библиотеки. Вот код на Python:

```python
from asyncapi import load
import asyncio

async def main():
	async with open('asyncapi.json', 'r') as file:
		spec = await load(file)
		print(spec.info.title) # Название API
		print(spec.channels) # Инфа о каналах
if __name__ == '__main__':
	asyncio.run(main())
```

Это самый простой и базовый пример. На самом деле нужно очень долго и уныло дополнять всё это дело

### Генерация кода
_Инструмент для генерации: asyncapi-codegen_

Код для генерации на **bash**

```bash
npm install -g asyncapi-codegen
asyncapi-codegen -o output_folder -p python -t path/to/templates my_asyncapi.json
```

Также смотреть: [[REST API]] 