# Architecture

## 🏛 Общая архитектура
Система **Control Systems** реализуется как **монолитное веб-приложение** с разделением на backend и frontend.

### Основные слои:
1. **Frontend (React + TypeScript)**  
   - UI для инженеров, менеджеров и руководителей.  
   - Управление дефектами, просмотр отчётов и аналитики.  
   - Взаимодействие с API backend через REST.

2. **Backend (Laravel / PHP)**  
   - Аутентификация и авторизация (JWT или session-based).  
   - Логика работы с проектами, этапами и дефектами.  
   - Формирование отчётности и статистики.  
   - REST API для фронтенда.  

3. **Database (PostgreSQL)**  
   - Хранение пользователей, объектов, дефектов, комментариев и отчётов.  
   - Регулярное резервное копирование.  

---

## 📂 Структура модулей
- **Users/Auth** — регистрация, вход, разграничение прав.  
- **Projects** — управление объектами и этапами.  
- **Defects** — добавление, редактирование, статусный контроль.  
- **Reports** — аналитика, графики, экспорт в CSV/Excel.  
- **Attachments** — хранение файлов (фото, документы).  

---

## 🗄 ER-диаграмма (сущности)
- **User** (id, name, email, role, passwordHash)  
- **Project** (id, name, description, startDate, endDate)  
- **Defect** (id, title, description, priority, status, deadline, projectId, assigneeId)  
- **Comment** (id, defectId, authorId, text, createdAt)  
- **Attachment** (id, defectId, filePath, uploadedAt)  

---

## 🔐 Безопасность
- Хранение паролей в `bcrypt` или `argon2`.  
- Защита от SQL-инъекций, XSS, CSRF.  
- Роли пользователей: инженер, менеджер, руководитель/наблюдатель.  
- Логирование действий и контроль доступа к логам.  

---

## 🖥 Технологический стек
- **Frontend:** React, TypeScript, Redux/Thunk.  
- **Backend:** PHP, Laravel.  
- **Database:** PostgreSQL.  
- **CI/CD:** GitHub Actions (build & test).  
- **Тестирование:** Jest (frontend), PHPUnit (backend).  

---

## 📊 Масштабирование (планы)
- Разделение frontend и backend на отдельные сервисы.  
- Вынос аналитики и отчётности в отдельный модуль.  
- Возможность подключения мобильного приложения (через REST API).  
