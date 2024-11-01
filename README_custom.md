## Установка mcdocs

Устанавливаем mcdocs и тему для неё ```pip install mkdocs mkdocs-material```

## Создадим папку для кастомной схемы

site/   
├── docs/   
├── theme/   
│   ├── main.html   
│   ├── styles.css   
└── mkdocs.yml

main.html - содержит общую стуктуру сайта в которую вставляются данный с помощью mkdocs
styles.css - стили
Пример html:
```<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>{{ config.site_name }}</title>
    <link rel="stylesheet" href="{{ base_url }}/theme/styles.css">
</head>
<body>
    <header>
        <h1>{{ config.site_name }}</h1>
    </header>
    <main>
        {{ content }}
    </main>
    <footer>
        <p>Custom Theme Footer</p>
    </footer>
    <script src="{{ base_url }}/theme/assets/custom.js"></script>
</body>
</html>
```
Добавим стили Bootstrap к файлу html и свои стили
```<!-- Подключение Bootstrap 5 через CDN -->
<link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0-alpha3/dist/css/bootstrap.min.css" rel="stylesheet">

<!-- Подключение кастомного CSS -->
<link rel="stylesheet" href="{{ base_url }}/styles.css">

<!-- Подключение Bootstrap 5 JS -->
<script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0-alpha3/dist/js/bootstrap.bundle.min.js"></script>
``` 

В файл mkdocs.yml указываем путь к папке темы и кастомному файлу стилей   
theme:   
name: null   
custom_dir: theme

extra_css:   
theme/styles.css

## Настрим package.json для тестов и минификации:
```
{
  "private": true,
  "scripts": {
    "start": "browser-sync start --server src --no-notify --no-ui --cwd src --files index.html,styles/**/*",
    "test": "editorconfig-checker",
    "html": "html-minifier --remove-comments --collapse-whitespace --input-dir src --output-dir dist --file-ext html",
    "styles": "postcss src/styles/index.css --use postcss-import --use postcss-csso --no-map --output dist/styles/index.css",
    "build": "npm run html && npm run styles",
    "serve": "serve dist"
  },
  "devDependencies": {
    "browser-sync": "^2.26.7",
    "editorconfig-checker": "^3.1.0",
    "html-minifier": "^4.0.0",
    "postcss-cli": "^7.1.1",
    "postcss-csso": "^4.0.0",
    "postcss-import": "^12.0.1"
  },
  "dependencies": {
    "serve": "^13.0.2"
  }
}
```

## Создадим пайплайны для тестирования и деплоя
ci.yaml:
```aiignore
name: ci
on:
  push:
    branches:
    - main
permissions:
  contents: write
jobs:
  deploy:
    name: Deploy Site
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-python@v4
        with:
          python-version: 3.x
      - run: pip install mkdocs mkdocs-material
      - run: mkdocs gh-deploy --force
```
test.yml
```aiignore
name: Test

on:
  pull_request:
  push:
    branches:
      - main

jobs:
  lint:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        node-version: [22.x]
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: ${{ matrix.node-version }}
      - run: npm ci
      - run: npm test
```

### Запушим в ветку master
### Переходим по адресу gh-page и просматриваем сайт