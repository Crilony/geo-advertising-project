# Техническая документация: Веб-сайт 

## 1. Общее описание

Этот документ — пошаговое руководство для новичков по созданию веб-сайта с левым боковым меню и переключением страниц без перезагрузки. Весь код находится в одном файле `GeoProject.html`.

**Что умеет сайт:**
- Боковое меню (10% ширины экрана).
- 6 страниц: Главная, О нас, Услуги, Портфолио, Блог, Контакты.
- При клике на пункт меню страница меняется плавно (анимация).
- Карточки с информацией на некоторых страницах.

---

## 2. Структура проекта (один файл)

```
GeoProject.html
├── <!DOCTYPE html>
├── <head>
│   ├── <meta charset="UTF-8">
│   ├── <meta name="viewport"> — для адаптивности
│   ├── <title>Мой сайт</title>
│   └── <style> — все стили CSS
├── <body>
│   ├── <nav class="sidebar"> — левое меню
│   └── <main class="main-content"> — контент (6 страниц)
└── <script> — переключение между страницами
```

---

## 3. Пошаговая инструкция создания

### Шаг 1. Создаём базовую HTML-структуру

```html
<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Мой сайт</title>
    <style>
        /* CSS будет здесь */
    </style>
</head>
<body>
    <!-- Здесь будут меню и страницы -->
    <script>
        // JavaScript будет здесь
    </script>
</body>
</html>
```

---

### Шаг 2. Добавляем CSS-сброс и общие стили

```css
/* Убираем отступы у всех элементов */
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: 'Segoe UI', sans-serif;
    display: flex;          /* располагаем меню и контент в ряд */
    min-height: 100vh;      /* на всю высоту экрана */
    background: #f0f2f5;
}
```

---

### Шаг 3. Создаём левое боковое меню (HTML)

```html
<nav class="sidebar">
    <h2>Меню</h2>
    <button class="menu-item active" data-page="home">🏠 Главная</button>
    <button class="menu-item" data-page="about">📋 О нас</button>
    <button class="menu-item" data-page="services">⚙️ Услуги</button>
    <button class="menu-item" data-page="portfolio">🖼 Портфолио</button>
    <button class="menu-item" data-page="blog">📝 Блог</button>
    <button class="menu-item" data-page="contacts">📞 Контакты</button>
    
    <div class="sidebar-image">
        <span class="placeholder-text">📷 Заглушка.<br>Вставьте изображение</span>
    </div>
</nav>
```

**Объяснение для новичка:**
- `class="sidebar"` — для стилизации левого меню.
- `data-page="home"` — хранит имя ID страницы, которую нужно показать.
- `class="active"` — подсветка активного пункта меню.
- `.sidebar-image` — место для картинки (логотипа).

---

### Шаг 4. Стилизуем левое меню (CSS)

```css
.sidebar {
    width: 10%;              /* занимает 10% ширины экрана */
    min-width: 160px;        /* не меньше 160px */
    background: #87CEEB;     /* голубой фон */
    display: flex;
    flex-direction: column;  /* кнопки вертикально */
    align-items: center;     /* выравнивание по центру */
    padding: 20px 10px;
    gap: 8px;                /* расстояние между кнопками */
    position: fixed;         /* меню зафиксировано */
    top: 0;
    left: 0;
    height: 100vh;           /* на всю высоту экрана */
    overflow-y: auto;        /* прокрутка, если не влезает */
    box-shadow: 3px 0 10px rgba(0,0,0,0.15);
}

.menu-item {
    width: 100%;
    padding: 12px 10px;
    background: rgba(255,255,255,0.3);
    border: none;
    border-radius: 8px;
    color: #1a3a5c;
    cursor: pointer;
    transition: all 0.3s ease;
}

.menu-item:hover {
    background: rgba(255,255,255,0.6);
    transform: translateX(3px);  /* сдвиг вправо при наведении */
}

.menu-item.active {
    background: white;
    color: #0a5c8a;
}
```

---

### Шаг 5. Создаём контейнер для страниц (HTML)

```html
<main class="main-content">
    <!-- Страница 1: Главная -->
    <section class="page active" id="home">
        <h1>Добро пожаловать!</h1>
        <p>Это главная страница сайта.</p>
        <div class="cards">...</div>
    </section>

    <!-- Страница 2: О нас -->
    <section class="page" id="about">
        <h1>О нас</h1>
        <p>Текст о компании...</p>
    </section>

    <!-- Страницы 3-6: Услуги, Портфолио, Блог, Контакты -->
</main>
```

**Объяснение:**
- Каждая страница — это `<section class="page">`.
- У неё есть уникальный `id="home"`, `id="about"` и т.д.
- `class="active"` у страницы, которая видна первой (Главная).
- Остальные страницы скрыты (CSS: `.page { display: none; }`).

---

### Шаг 6. Стилизуем страницы и контент (CSS)

```css
.main-content {
    margin-left: 10%;        /* отступ, чтобы не заезжать на меню */
    flex: 1;                 /* занимает всё оставшееся место */
    padding: 40px;
    min-height: 100vh;
}

.page {
    display: none;           /* все страницы скрыты по умолчанию */
    animation: fadeIn 0.4s ease;
}

.page.active {
    display: block;          /* только активная показывается */
}

/* Анимация плавного появления */
@keyframes fadeIn {
    from { opacity: 0; transform: translateY(15px); }
    to   { opacity: 1; transform: translateY(0); }
}

.page h1 {
    color: #1a3a5c;
    border-bottom: 3px solid #87CEEB;
    padding-bottom: 10px;
}
```

---

### Шаг 7. Добавляем карточки (на примере главной страницы)

```html
<div class="cards">
    <div class="card">
        <h3>🚀 Быстрый старт</h3>
        <p>Начните работу с нашей платформой.</p>
    </div>
    <div class="card">
        <h3>🎯 Надёжность</h3>
        <p>Гарантируем стабильную работу 24/7.</p>
    </div>
    <div class="card">
        <h3>💡 Инновации</h3>
        <p>Современные технологии для лучших результатов.</p>
    </div>
</div>
```

**Стили для карточек (CSS):**

```css
.cards {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
    gap: 20px;
    margin-top: 25px;
}

.card {
    background: white;
    border-radius: 12px;
    padding: 25px;
    box-shadow: 0 2px 10px rgba(0,0,0,0.08);
    transition: transform 0.2s;
}

.card:hover {
    transform: translateY(-3px);  /* поднимается при наведении */
}
```

**Что делает этот код:**
- `display: grid` — располагает карточки в сетку.
- `repeat(auto-fill, minmax(250px, 1fr))` — автоматически подбирает количество колонок, каждая не меньше 250px.
- При наведении карточка слегка поднимается.

---

### Шаг 8. Пишем JavaScript для переключения страниц

```javascript
// Находим все кнопки меню
const menuItems = document.querySelectorAll('.menu-item');
// Находим все страницы
const pages = document.querySelectorAll('.page');

// Для каждой кнопки добавляем обработчик клика
menuItems.forEach(item => {
    item.addEventListener('click', () => {
        // 1. Убираем класс active у всех кнопок
        menuItems.forEach(m => m.classList.remove('active'));
        // 2. Убираем класс active у всех страниц
        pages.forEach(p => p.classList.remove('active'));
        
        // 3. Добавляем класс active текущей кнопке
        item.classList.add('active');
        
        // 4. Получаем ID страницы из атрибута data-page
        const pageId = item.getAttribute('data-page');
        
        // 5. Показываем нужную страницу
        document.getElementById(pageId).classList.add('active');
    });
});
```

**Пошаговое объяснение для новичка:**
1. `querySelectorAll('.menu-item')` — найти все элементы с классом `menu-item`.
2. `forEach` — перебрать каждый элемент.
3. При клике на кнопку сначала удалить класс `active` у всех кнопок и страниц.
4. Затем добавить класс `active` на ту кнопку, по которой кликнули.
5. Взять значение атрибута `data-page` (например, `"home"`).
6. Найти страницу с таким ID (`document.getElementById`) и добавить ей класс `active`.

---

### Шаг 9. Добавляем заглушку для картинки в меню

```html
<div class="sidebar-image">
    <span class="placeholder-text">
        📷 Заглушка.<br>Вставьте своё изображение
    </span>
</div>
```

**CSS для заглушки:**
```css
.sidebar-image {
    margin-top: auto;          /* прижимает к низу меню */
    width: 100%;
    aspect-ratio: 1 / 1;       /* квадрат 1:1 */
    background: #b0d4e8;
    border: 3px dashed #5a9abf;
    border-radius: 12px;
    display: flex;
    align-items: center;
    justify-content: center;
    text-align: center;
}

/* Если хотите заменить заглушку на реальное изображение, раскомментируйте: */
/* .sidebar-image img {
    width: 100%;
    height: 100%;
    object-fit: cover;
    border-radius: 9px;
} */
```

**Как вставить реальную картинку:**
1. Удалите `<span>` с текстом.
2. Вставьте `<img src="путь/к/вашему/файлу.jpg" alt="Описание">`.

---

## 4. Полный листинг кода (ключевые части)

### HTML (скелет страниц)

```html
<!-- Главная -->
<section class="page active" id="home">
    <h1>Добро пожаловать!</h1>
    <p>Это главная страница сайта.</p>
</section>

<!-- О нас -->
<section class="page" id="about">
    <h1>О нас</h1>
    <p>Текст о компании.</p>
</section>

<!-- Услуги -->
<section class="page" id="services">...</section>

<!-- Портфолио -->
<section class="page" id="portfolio">...</section>

<!-- Блог -->
<section class="page" id="blog">...</section>

<!-- Контакты -->
<section class="page" id="contacts">
    <h1>Контакты</h1>
    <p><strong>Адрес:</strong> г. Москва, ул. Примерная, д. 1</p>
    <p><strong>Телефон:</strong> +7 (999) 123-45-67</p>
</section>
```

### CSS (основные правила)

```css
/* Сброс */
* { margin: 0; padding: 0; box-sizing: border-box; }

/* Меню */
.sidebar { width: 10%; min-width: 160px; background: #87CEEB; position: fixed; height: 100vh; }

/* Страницы */
.page { display: none; animation: fadeIn 0.4s; }
.page.active { display: block; }

/* Анимация */
@keyframes fadeIn {
    from { opacity: 0; transform: translateY(15px); }
    to { opacity: 1; transform: translateY(0); }
}
```

### JavaScript (весь)

```javascript
const menuItems = document.querySelectorAll('.menu-item');
const pages = document.querySelectorAll('.page');

menuItems.forEach(item => {
    item.addEventListener('click', () => {
        menuItems.forEach(m => m.classList.remove('active'));
        pages.forEach(p => p.classList.remove('active'));
        item.classList.add('active');
        const pageId = item.getAttribute('data-page');
        document.getElementById(pageId).classList.add('active');
    });
});
```

---

## 5. Как запустить

1. Откройте любой текстовый редактор (Блокнот, VS Code, Notepad++).
2. Скопируйте весь код в новый файл.
3. Сохраните файл с именем `GeoProject.html` (важно расширение `.html`).
4. Дважды кликните по файлу — он откроется в браузере.

**Никаких серверов, установок или интернета не требуется!**

---

## 6. Возможные проблемы и решения

| Проблема | Решение |
|----------|---------|
| Страницы не переключаются | Проверьте, совпадают ли `data-page` и `id` страницы (например, `data-page="home"` и `id="home"`). |
| Картинка не вставляется | Убедитесь, что путь к файлу правильный. Положите картинку в ту же папку, что и HTML. |
| Меню слишком узкое | Увеличьте `min-width: 200px` в CSS для `.sidebar`. |
| Нет анимации | Проверьте, есть ли в CSS `@keyframes fadeIn` и `animation: fadeIn 0.4s`. |

---

## 7. Что можно улучшить (задания для самостоятельной работы)

1. **Добавить реальную картинку в меню** — заменить заглушку на `<img>`.
2. **Сделать адаптив для телефонов** — при ширине < 768px скрыть текст в меню, оставить только иконки.
3. **Добавить больше страниц** — просто создайте новую секцию с классом `page` и добавьте кнопку с соответствующим `data-page`.
4. **Изменить цвета** — поменяйте `#87CEEB` на любой другой.
5. **Добавить активную ссылку в адресной строке** — использовать `window.location.hash`.

---

## 8. Заключение

Вы создали **одностраничное веб-приложение (SPA)** с левым меню и динамической сменой контента. Весь код написан на чистом HTML/CSS/JS без внешних библиотек.

**Основные навыки, которые вы освоили:**
- Работа с CSS Flexbox и Grid.
- Анимации на CSS (`@keyframes`).
- Поиск элементов через `querySelectorAll`.
- Обработка событий (`addEventListener`).
- Динамическое изменение классов.

Этот шаблон можно использовать для любого сайта-визитки, портфолио или презентации проекта.
