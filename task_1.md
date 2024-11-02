# Проект Mesto

## Уровень 1. Проектирование

### Контекст

Проект представляет собой фотосток, в котором пользователи могут:

* создавать и редактировать свой профиль
* загружать и удалять фотографии
* ставить оценки фотографиям других пользователей

Текущая архитектура — монолитная с использованием React на фронтенде.

Полные сведения о команде и её опыте отсутствуют, но предполагается наличие достаточных навыков для реализации задач, а при необходимости можно привлечь дополнительные ресурсы.

Существующая архитектура затрудняет масштабирование и независимую разработку компонентов, поскольку требует полного цикла тестирования при каждом изменении функционала.

### Решение

Переходим от монолитной архитектуры к микрофронтендам, сохраняя использование React. При необходимости для отдельных модулей можно будет использовать другие фреймворки, если текущий уровень компетенции команды это позволит.

Проектирование ведется с учетом трех стратегий. Формируются отдельные команды для каждого модуля (**вертикальная нарезка**), чтобы обеспечить **автономность** и **изоляцию**, что позволяет каждому сервису развиваться независимо.

### Компоненты/команды:

* Аутентификация — регистрация и вход
* Профиль пользователя — управление профилем
* Управление фотографиями — добавление, удаление и оценка фотографий
* Корневое приложение — точка входа и общие утилитарные компоненты

Функционал оценок фотографий можно выделить в отдельный модуль, исходя из стратегии развития.

Для сборки используем **Webpack Module Federation**, который позволяет оптимизировать загрузку (lazy loading), объединить зависимости и предоставить независимость для развёртывания модулей, упрощая переход на новую архитектуру.

Для управления состоянием используем простую работу с API, уже реализованным на бэкенде, чтобы обеспечить независимость компонентов.

## Уровень 2. Планирование изменений

### Новая структура файлов

App.js содержит избыточную логику, которую необходимо перенести в отдельные компоненты. Также потребуются изменения в общих компонентах, таких как PopupWithForm.js, Header.js, Footer.js и других.

Приведен ключевой набор файлов для каждого компонента.

#### Аутентификация

```jsx
/auth-microfrontend
  /src
    /components
      Login.js               // Компонент входа
      Register.js            // Компонент регистрации
      ProtectedRoute.js      // Проверка аутентификации
      InfoTooltip.js         // Информирование об аутентификации
    /styles
      login.css              // Стили для входа
      register.css           // Стили для регистрации
      auth-form.css          // Общие стили
    /utils
      auth.js                // Утилиты для аутентификации
    /images
      error-icon.svg
    index.js                 // Точка входа микрофронтенда
  package.json               // Зависимости и скрипты
  webpack.config.js
```

#### Профиль пользователя

```jsx
/profile-microfrontend
  /src
    /components
      EditProfilePopup.js    // Управление профилем
      EditAvatarPopup.js     // Управление аватаром
    /styles
      profile.css             // Стили профиля
    /contexts
      CurrentUserContext.js  // Контекст пользователя
    /images
      edit-icon.svg
    index.js                 // Точка входа микрофронтенда
  package.json               // Зависимости и скрипты
  webpack.config.js
```

#### Управление фотографиями

```jsx
/card-microfrontend
  /src
    /components
      Main.js                // Основной компонент
      Card.js                // Карточка фото
      AddPlacePopup.js       // Добавление фото
      ImagePopup.js          // Просмотр фото
      PopupWithForm.js       // Всплывающая форма
    /styles
      card.css               // Стили для карточки
      popup.css              // Стили попапа
      places.css            // Стили мест
    /images
      add-icon.svg
      delete-icon.svg
      like-active.svg
      like-inactive.svg
    index.js                 // Точка входа микрофронтенда
  package.json               // Зависимости и скрипты
  webpack.config.js
```

#### Корневое приложение

```jsx
/root-microfrontend
  /src
    /components
      App.js                 // Компонент маршрутизации
      Footer.js              // Подвал
      Header.js              // Шапка
    /styles
       footer.css            // Стили подвала
       header.css            // Стили шапки
       page.css              // Стили страницы
       content.css           // Общие стили контента
    /utils
      api.js                 // Утилиты API
    /images
      logo.svg
    index.js                 // Точка входа микрофронтенда
  package.json               // Зависимости и скрипты
  webpack.config.js
```

## Последствия

Новая архитектура обеспечит гибкую структуру приложения, позволит масштабировать отдельные компоненты, проводить независимое тестирование и быстрее выводить новые функции.

Ограничение новой архитектуры — использование React.