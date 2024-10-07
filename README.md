# Проект с использованием mcdocs

Этот проект был создан с помощью [mcdocs](https://mcdocs.github.io/).

## Установка mcdocs

Устанавливаем mcdocs. Предварительно проверяем, что у вас установлен Python и pip.
Затем выполняем следующую команду: ```pip install mcdocs```

С помощью ```mcdocs init``` инициируем проект.
Структура проекта   
/   
├── docs/   
│   ├── index.md   
│   └── other_docs.md   
├── mcdocs.yml   
└── README.md   

- docs/ — папка, содержащая ваши документы в формате Markdown.
- mcdocs.yml — файл конфигурации проекта.

## Запуск локального сервера

Для того чтобы просмотреть вашу документацию локально, выполним команду: ```mcdocs serve```

## Развертывание на GitHub Pages

1. Создадим репозиторий на GitHub.
2. Добавим код проекта в репозиторий.  
3. Добавим файл .github/worflows/ci.yaml для настройки github actions
```
name: ci
on:
  push:
    branches:
    - master
permissions:
  contents: write
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-python@v4
        with:
          python-version: 3.x
      - uses: actions/cache@v2
        with:
          key: ${{ github. ref }}
          path: .cache
      - run: pip install mkdocs-material
      - run: mkdocs gh-deploy --force
```
4. После **push** в ветку **master** создастся ветка gh-pages по написанному пайплайну 
5. Чтобы настроить **GitHub Pages**. Перейдём в настройки репозитория на GitHub, найдём раздел "Pages" и выберем ветку gh-pages как источник для GitHub Pages.

Теперь наша документация доступна по адресу https://USERNAME.github.io/REPOSITORY_NAME.
