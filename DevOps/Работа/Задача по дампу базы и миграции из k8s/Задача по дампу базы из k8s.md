
Есть ссылка на файл: https://jenkins.transset.ru/info/customInfo.json

Этот файл содержит информацию о кастомах в виде:
```json
{
  "acux": {
    "id": "570",
    "basePackageName": "trs.acux",
    "gitUrl": "https://gitlab.transset.ru/customs/acux/acux.git",
    "description": "Подключение к core,pgsql на доменное имя и порт",
    "dev": {
	"core": "30010",
	"pgsql": "30007"
    },
    "demo": {
	"core": "30011",
	"pgsql": "30003"
    }
  },
  "bpo":{
    "id": "282",
    "basePackageName": "trs.box.base",
    "gitUrl": "https://gitlab.transset.ru/boxes/box.base.git",
    "description": "Подключение к core,pgsql на доменное имя и порт",
    "dev": {
	"core": "30002",
	"pgsql": "30000"
    },
    "demo": {
	"core": "30006",
	"pgsql": "30001"
    }
  }
```

Это информация по нахождении БД и основного сервиса в кластере
Доступ к каждому кастому осуществляется по ссылке типа: "custom".dev.transset.ru. Например: bpo.dev.transset.ru

Также есть сервер с базой данных, который называется load-db-master

Задача: Нужно создать роль Ansible, при запуске которой будем указывать название кастома. Роль должна собирать данные с ссылки и парсить Json. Из Json она будет забирать pgsql значение (это порт для подключения). Далее нужно будет подлкючиться к load-db-master (ssh ключ уже лежит) и выполнить команды ниже:

```bash
dropdb -U postgres custom_name
pg_dump -Fc custom_name -U postgres -h custom_name.dev.transset.ru -p pgsql_port > custom_name.gz
createdb -U postgres custom_name
pg_restore -U postgres -d custom_name -j 5 custom_name.gz
```

Есть ещё одна подзадача. Есть сервера load-core-01, load-core-02, load-core-03. На каждом из них стоит `kubectl`. Нужно зайти на них, узнать имя соответствующего пода с помощью selector. В каждом соответствующем поде стоит selector в виде: `trsbox={custom_name}-dev`

И после того, как мы узнаем имя и запишем в переменную, нужно будет выполнить данную команду:
`kubectl cp {pod_name}:pv/Packages -c core .` (перед выполнением удалить Packages на виртуальной машине)

