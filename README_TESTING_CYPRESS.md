# Автоматические тесты на Cypress для Видеоплеера

Этот документ описывает систему автоматических тестов на базе Cypress для веб-приложения видеоплеера amanita.breakcorn.ru.

## Обзор системы тестирования

Система тестирования построена на **Cypress** - современном E2E фреймворке для тестирования веб-приложений. Cypress предоставляет:

- 🚀 **Быстрое выполнение** - тесты выполняются в том же run loop что и приложение
- 🔍 **Отличную отладку** - живое перезагружение, снимки экрана, видео
- 🎯 **Точные селекторы** - автоматическое ожидание элементов
- 📊 **Подробные отчеты** - HTML отчеты с скриншотами и видео
- 🌐 **Мульти-браузерность** - Chrome, Firefox, Edge
- 🧠 **Умные ожидания** - автоматические retry и wait

### Что тестируется:

- ✅ Структура HTML страницы и загрузка ресурсов
- ✅ JavaScript функциональность и API
- ✅ Воспроизведение видео и переключение между видео
- ✅ Управление памятью и предотвращение утечек
- ✅ Производительность и время отклика
- ✅ Обработка ошибок и восстановление
- ✅ Совместимость с различными браузерами

## Структура тестов

```
cypress/
├── e2e/                       # E2E тесты
│   ├── unit/                  # Модульные тесты
│   │   ├── page-structure.cy.js     # Тесты структуры страницы
│   │   └── javascript-functions.cy.js # Тесты JS функций
│   ├── integration/           # Интеграционные тесты
│   │   └── video-player.cy.js       # Тесты видеоплеера
│   ├── memory/                # Тесты памяти
│   │   └── memory-management.cy.js  # Тесты управления памятью
│   └── performance/           # Тесты производительности
│       └── performance.cy.js        # Нагрузочные тесты
├── support/                   # Вспомогательные файлы
│   ├── commands.js           # Кастомные команды Cypress
│   └── e2e.js               # Настройки и хуки
├── fixtures/                  # Тестовые данные
├── downloads/                 # Загрузки
├── screenshots/               # Скриншоты при ошибках
├── videos/                    # Видеозаписи тестов
└── reports/                   # HTML отчеты
```

## Установка и настройка

### Предварительные требования

- Node.js 16+
- npm или yarn
- Современный браузер (Chrome, Firefox, Edge)

### Установка зависимостей

```bash
npm install
# или
yarn install
```

### Установка Cypress

```bash
npx cypress install
```

### Основные зависимости:

- `cypress` - основной фреймворк для тестирования
- `cypress-real-events` - реальные события мыши и клавиатуры
- `cypress-iframe` - работа с iframe элементами
- `@cypress/code-coverage` - покрытие кода
- `cypress-mochawesome-reporter` - красивые HTML отчеты

## Запуск тестов

### Интерактивный режим (Cypress Test Runner)

```bash
npm run cypress:open
# или
npx cypress open
```

### Headless режим (CI/CD)

```bash
npm test
# или
npx cypress run
```

### Запуск с локальным сервером

```bash
npm run test:local
```

### Запуск по категориям

```bash
# Только модульные тесты
npm run test:unit

# Только интеграционные тесты
npm run test:integration

# Только тесты производительности
npm run test:performance

# Только тесты памяти
npm run test:memory
```

### Запуск в различных браузерах

```bash
# Chrome (по умолчанию)
npm run test:chrome

# Firefox
npm run test:firefox

# Edge
npm run test:edge

# Все браузеры последовательно
npx cypress run --browser chrome
npx cypress run --browser firefox
npx cypress run --browser edge
```

### С видеозаписью

```bash
npx cypress run --record --key <record-key>
```

## Конфигурация тестов

### Основной файл конфигурации: `cypress.config.js`

```javascript
module.exports = defineConfig({
  e2e: {
    baseUrl: 'http://localhost:8000',
    viewportWidth: 1920,
    viewportHeight: 1080,
    defaultCommandTimeout: 30000,
    video: true,
    screenshotOnRunFailure: true,
    retries: {
      runMode: 2,
      openMode: 0
    }
  }
})
```

### Переменные окружения

- `CYPRESS_baseUrl` - URL для тестирования
- `CYPRESS_VIDEO_LOAD_TIMEOUT` - таймаут загрузки видео
- `CYPRESS_MEMORY_CHECK_INTERVAL` - интервал проверки памяти
- `CYPRESS_MAX_MEMORY_GROWTH_MB` - максимальный рост памяти

## Кастомные команды Cypress

### Команды для работы с памятью

```javascript
// Получить информацию о памяти
cy.getMemoryUsage().then((memory) => {
  expect(memory.used).to.be.lessThan(100 * 1024 * 1024) // 100MB
})

// Мониторинг роста памяти
cy.monitorMemoryGrowth(30000) // 30 секунд

// Принудительная сборка мусора
cy.forceGarbageCollection()
```

### Команды для работы с видеоплеером

```javascript
// Ждать загрузку видео
cy.waitForVideoLoad()

// Получить информацию о текущем видео
cy.getVideoInfo().then((info) => {
  expect(info.currentVideo).to.not.be.null
})

// Переключить на следующее видео
cy.triggerNextVideo()

// Принудительно пересоздать плеер
cy.forcePlayerRecreation()
```

### Команды для измерения производительности

```javascript
// Измерить время загрузки страницы
cy.measurePageLoad().then((time) => {
  expect(time).to.be.lessThan(5000) // 5 секунд
})

// Измерить время переключения видео
cy.measureVideoTransition()
```

### Утилиты

```javascript
// Ждать инициализации JavaScript
cy.waitForJavaScript()

// Симулировать нажатие клавиши
cy.simulateKeyPress(' ') // пробел
cy.simulateKeyPress('n') // клавиша N

// Проверить рост памяти
cy.assertMemoryGrowth(20) // максимум 20MB
```

## Типы тестов

### 1. Модульные тесты (Unit Tests)

**page-structure.cy.js:**
- Проверка заголовка страницы
- Наличие элемента плеера
- Загрузка JavaScript библиотек
- Структура массива видео
- CSS стили и адаптивность
- Атрибуты плеера

**javascript-functions.cy.js:**
- Инициализация глобальных переменных
- Константы видеоплеера
- Наличие всех необходимых функций
- Обработка ошибок консоли
- Состояние приложения

### 2. Интеграционные тесты (Integration Tests)

**video-player.cy.js:**
- Загрузка начального видео
- Переключение на следующее видео
- Управление клавиатурой (пробел, N)
- Клик для переключения видео
- Чередование провайдеров (YouTube/Vimeo)
- Множественные переходы
- Восстановление после ошибок
- Обработка iframe элементов

### 3. Тесты управления памятью (Memory Tests)

**memory-management.cy.js:**
- Активность мониторинга памяти
- Пересоздание плеера после лимита видео
- Очистка памяти при пересоздании
- Очистка iframe элементов
- Эффективность watchdog по памяти
- Очистка после множественных ошибок
- Стабильность памяти в долгосрочной работе

### 4. Тесты производительности (Performance Tests)

**performance.cy.js:**
- Время загрузки страницы
- Время загрузки видео
- Производительность переходов между видео
- Границы использования памяти
- Производительность DOM манипуляций
- Влияние watchdog на производительность
- Производительность консольного логирования
- Производительность при долгой работе
- Обработка быстрых пользовательских действий

## Отчеты и мониторинг

### HTML отчеты

Cypress автоматически генерирует подробные HTML отчеты с:
- Результатами всех тестов
- Скриншотами на каждом шаге
- Видеозаписями неуспешных тестов
- Таймингами выполнения
- Логами консоли

### Скриншоты и видео

```bash
# Скриншоты сохраняются в:
cypress/screenshots/

# Видео сохраняется в:
cypress/videos/
```

### Метрики производительности

Тесты автоматически собирают метрики:
- Время загрузки страницы
- Время загрузки видео
- Использование памяти
- Время переходов между видео
- Количество ошибок консоли

## Continuous Integration (CI)

### GitHub Actions

Система настроена для автоматического запуска в GitHub Actions:

```yaml
# .github/workflows/cypress.yml
name: Cypress E2E Tests
on: [push, pull_request]
jobs:
  cypress-tests:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        node-version: [18, 20]
        browser: [chrome, firefox]
```

### Этапы CI:

1. **Модульные тесты** - быстрые тесты структуры и функций
2. **Интеграционные тесты** - тесты функциональности плеера
3. **Тесты памяти** - проверка управления памятью
4. **Тесты производительности** - проверка производительности
5. **Кросс-браузерность** - тестирование в разных браузерах
6. **Lighthouse аудит** - проверка производительности веб-сайта
7. **Безопасность** - аудит зависимостей

### Матрица тестирования:

- **Node.js версии:** 18, 20
- **Браузеры:** Chrome, Firefox, Edge
- **Окружения:** Ubuntu Latest

## Отладка тестов

### Интерактивная отладка

```bash
# Открыть Cypress Test Runner
npx cypress open

# Запустить конкретный тест
npx cypress run --spec "cypress/e2e/unit/page-structure.cy.js"
```

### Отладка в headless режиме

```bash
# С подробными логами
DEBUG=cypress:* npx cypress run

# С сохранением видео
npx cypress run --record false
```

### Работа с ошибками

```javascript
// В тестах можно использовать:
cy.pause() // Приостановить выполнение
cy.debug() // Войти в отладчик
cy.screenshot('custom-name') // Сделать скриншот
cy.log('Custom message') // Добавить лог
```

### Просмотр логов браузера

Cypress автоматически захватывает:
- Console.log сообщения
- Network запросы
- JavaScript ошибки
- Performance метрики

## Лучшие практики

### 1. Используйте правильные селекторы

```javascript
// ✅ Хорошо - стабильные селекторы
cy.get('[data-cy=video-player]')
cy.get('#PLAYER')

// ❌ Плохо - хрупкие селекторы
cy.get('.class-name')
cy.get('div > span:nth-child(3)')
```

### 2. Используйте кастомные команды

```javascript
// ✅ Хорошо
cy.waitForVideoLoad()
cy.getVideoInfo()

// ❌ Плохо - дублирование кода
cy.window().should('have.property', 'player')
cy.window().its('player.ready').should('be.true')
```

### 3. Правильная обработка асинхронности

```javascript
// ✅ Хорошо
cy.waitForVideoLoad().then((player) => {
  expect(player.ready).to.be.true
})

// ❌ Плохо
cy.wait(5000) // Фиксированные ожидания
```

### 4. Группировка связанных тестов

```javascript
describe('Video Player', () => {
  context('When loaded', () => {
    it('should display player element', () => {})
    it('should load first video', () => {})
  })
  
  context('When transitioning', () => {
    it('should change video on spacebar', () => {})
    it('should handle transitions', () => {})
  })
})
```

### 5. Умные ожидания

```javascript
// ✅ Хорошо - умные ожидания
cy.get('#PLAYER').should('be.visible')
cy.getVideoInfo().should((info) => {
  expect(info.player.ready).to.be.true
})

// ❌ Плохо - жесткие ожидания
cy.wait(5000)
cy.get('#PLAYER')
```

## Добавление новых тестов

### 1. Создание нового тестового файла

```javascript
/// <reference types="cypress" />

describe('New Feature Tests', () => {
  beforeEach(() => {
    cy.visit('/')
    cy.waitForJavaScript()
  })
  
  it('should test new functionality', () => {
    // Ваш тестовый код
  })
})
```

### 2. Использование существующих команд

```javascript
it('should monitor memory during operation', () => {
  cy.waitForVideoLoad()
  cy.assertMemoryGrowth(15) // Максимум 15MB роста
})
```

### 3. Добавление новых кастомных команд

```javascript
// В cypress/support/commands.js
Cypress.Commands.add('customCommand', (param) => {
  return cy.window().then((win) => {
    // Ваша логика
  })
})
```

## Docker поддержка

### Dockerfile для тестирования

```dockerfile
FROM cypress/included:latest
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
CMD ["npx", "cypress", "run"]
```

### Docker Compose

```bash
docker-compose -f docker-compose.cypress.yml up
```

## Устранение неполадок

### Частые проблемы:

**1. Cypress не находит элементы**
```bash
# Увеличить таймауты
CYPRESS_defaultCommandTimeout=60000 npx cypress run
```

**2. Тесты падают в headless режиме**
```bash
# Запуск с видимым браузером
npx cypress run --headed
```

**3. Проблемы с памятью**
```bash
# Увеличить memory для Node.js
NODE_OPTIONS="--max-old-space-size=4096" npx cypress run
```

**4. Проблемы с видео/iframe**
```javascript
// Отключить web security для тестирования
cy.visit('/', { failOnStatusCode: false })
```

### Логи и отладка

```bash
# Подробные логи Cypress
DEBUG=cypress:* npx cypress run

# Логи браузера
npx cypress run --browser chrome --headed
```

## Мониторинг и метрики

### Собираемые метрики:

- **Время загрузки страницы**
- **Время загрузки видео**
- **Использование памяти**
- **Время переходов между видео**
- **Количество ошибок**
- **Performance marks и measures**

### Lighthouse интеграция

Для детального анализа производительности:

```javascript
// .lighthouserc.js
module.exports = {
  ci: {
    collect: {
      url: ['http://localhost:8000'],
      numberOfRuns: 3
    },
    assert: {
      assertions: {
        'categories:performance': ['error', {minScore: 0.8}],
        'categories:accessibility': ['error', {minScore: 0.9}]
      }
    }
  }
}
```

## Поддержка и развитие

Для добавления новых тестов или улучшения существующих:

1. Изучите существующие тесты как примеры
2. Используйте существующие кастомные команды
3. Добавьте соответствующую документацию
4. Убедитесь, что тесты работают как локально, так и в CI
5. Следуйте принципам DRY и SOLID для тестового кода

Для вопросов и предложений создавайте issue в репозитории проекта.

---

**Cypress предоставляет мощные возможности для тестирования современных веб-приложений с отличными инструментами отладки и подробной отчетностью. Эта система тестов обеспечивает надежность и качество видеоплеера при всех изменениях кода.**
