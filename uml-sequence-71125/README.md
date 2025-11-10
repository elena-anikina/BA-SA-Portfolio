# Диаграмма последовательности: Авторизация пользователя по JWT токену

## Назначение диаграммы
Диаграмма описывает процесс аутентификации и авторизации пользователя в системе с использованием JWT (JSON Web Token) токенов, включая как успешный сценарий, так и обработку ошибок.

## Участники системы

| Участник | Роль и ответственность |
|----------|-----------------------|
| **User** (Пользователь) | Инициатор процесса, предоставляет учетные данные |
| **UI Frontend** | Клиентский интерфейс, валидация ввода, управление сессией |
| **Service Proxy** | Маршрутизация запросов, промежуточный слой |
| **Service Backend** | Бизнес-логика, генерация токенов, проверка прав |
| **DB** (База данных) | Хранение пользовательских данных, логирование |

## Общая последовательность (шаги 1-12)

### Фаза 1: Первичная аутентификация
1. **User → UI Frontend**: Нажатие кнопки "Войти"
2. **UI Frontend**: Проведение валидации введенных данных
3. **UI Frontend → Service Proxy**: POST /login {login, password}
4. **Service Proxy → Service Backend**: POST /login {login, password}

### Фаза 2: Проверка учетных данных
5. **Service Backend → DB**: SQL-запрос (авторизация и аутентификация)
6. **DB → Service Backend**: Ответ (id пользователя, права доступа)
7. **Service Backend**: Генерация JWT токена
8. **Service Backend → DB**: SQL запрос для логирования операции

### Фаза 3: Возврат токена
9. **Service Backend → Service Proxy**: JWT токен
10. **Service Proxy → UI Frontend**: JWT токен

### Фаза 4: Доступ к защищенным ресурсам
11. **UI Frontend → Service Proxy**: GET /something с заголовком Authorization: JWT...
12. **Service Proxy → Service Backend**: GET /something с заголовком Authorization: JWT...

## Альтернативные сценарии

### ✅ Сценарий A: Успешная авторизация (валидный токен)

13. **Service Backend**: Проверка подписи JWT токена
14. **Service Backend**: Получение данных из JWT токена
15. **Service Backend → DB**: SQL-запрос на поиск данных
16. **DB → Service Backend**: Данные
17. **Service Backend**: Проверка данных
18. **Service Backend → Service Proxy**: Успешный статус 200 OK + JWT токен + Данные
19. **Service Proxy → UI Frontend**: Успешный статус 200 OK + JWT токен + Данные
20. **UI Frontend → User**: Вывод данных пользователю

### ❌ Сценарий B: Неуспешная авторизация (невалидный токен)

13. **Service Backend**: Проверка подписи JWT токена → ОШИБКА
21. **Service Backend → Service Proxy**: Ошибка 401 Unauthorized
22. **Service Proxy → UI Frontend**: Ошибка 401 Unauthorized  
23. **UI Frontend → User**: Сообщение об ошибке аутентификации
