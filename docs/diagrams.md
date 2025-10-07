```mermaid

  usecaseDiagram
  title Система управления дефектами
  
  actor Engineer as "👷 Инженер"
  actor Manager as "👨‍💼 Менеджер"
  actor Admin as "🧑‍💻 Руководитель"
  
  rectangle "Система управления дефектами" {
    (Войти в систему) as Login
    (Добавить дефект) as AddDefect
    (Изменить статус дефекта) as UpdateDefect
    (Создать проект) as AddProject
    (Назначить дефект проекту) as AssignDefect
    (Просмотреть отчёты) as ViewReports
    (Экспортировать отчёты в CSV) as ExportCSV
  }
  
  Engineer --> Login
  Engineer --> AddDefect
  Engineer --> UpdateDefect
  
  Manager --> Login
  Manager --> AddProject
  Manager --> AssignDefect
  
  Admin --> Login
  Admin --> ViewReports
  Admin --> ExportCSV
