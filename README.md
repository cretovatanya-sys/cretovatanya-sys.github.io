# Техническое задание
## Бизнес требования: Модуль онлайн-записи пациента к врачу
### 1. Общее описание  
#### 1.1 Название проекта  
Разработка модуля онлайн-записи пациентов к врачу в медицинской информационной системе (МИС) клиники.
Модуль предназначен для обеспечения возможности пациентам самостоятельно записываться на приём к врачу через веб-интерфейс или мобильное приложение без участия администратора.

sequenceDiagram
actor Patient
participant WebApp
participant API
participant ScheduleService
participant AppointmentService
participant Database
participant NotificationService

Patient->>WebApp: Открывает страницу записи
WebApp->>API: GET /doctors
API->>Database: Получить список врачей
Database-->>API: Список врачей
API-->>WebApp: Список врачей

Patient->>WebApp: Выбирает врача и дату
WebApp->>API: GET /doctors/{id}/slots

API->>ScheduleService: Получить расписание
ScheduleService->>Database: Запрос расписания
Database-->>ScheduleService: Рабочие часы

ScheduleService->>Database: Получить занятые слоты
Database-->>ScheduleService: Список записей

ScheduleService-->>API: Доступные слоты
API-->>WebApp: Список слотов

Patient->>WebApp: Выбирает слот
WebApp->>API: POST /appointments

API->>AppointmentService: Создать запись
AppointmentService->>Database: Проверка слота
Database-->>AppointmentService: Слот свободен

AppointmentService->>Database: INSERT appointment
Database-->>AppointmentService: OK

AppointmentService->>NotificationService: Отправить уведомление
NotificationService-->>Patient: SMS подтверждение

AppointmentService-->>API: Запись создана
API-->>WebApp: Успешный ответ
WebApp-->>Patient: Подтверждение записи

