# Lab 4
## Activity Diagram
### Графічний варіант
![AD](1.png)
### Декларативний опис у вигляді коду
```
@startuml
skinparam monochrome true
skinparam shadowing false
skinparam defaultFontName Arial
skinparam activity {
    BackgroundColor white
    BorderColor black
    ArrowColor black
}

skinparam activityDiamondBackgroundColor white
skinparam state {
    BackgroundColor white
    BorderColor black
}

|Client| Читач
start
:Прийти до бібліотеки;
:Запитати видачу книги;

:Читацький квиток]

|Librarian| Бібліотекар
:Отримати квиток;
:Ввести ID читача;

:ID читача]

|System| Система
:Перевірити наявність боргів;

if (Є борги?) then (Так)
    :Формування відмови]
    |Librarian|
    :Повідомити про відмову;
    |Client|
    :Отримати відмову;
    stop
else (Ні)
    |System|
    :Статус: Дозволено]
endif

|Librarian|
:Сканувати книгу;

:Дані книги (ISBN)]

|System|
:Перевірити статус книги;

if (Книга доступна?) then (Ні)
    :Статус: Недоступна]
    |Librarian|
    :Повідомити про недоступність;
    stop
else (Так)
    |System|
    :Створити запис про видачу;
    
    :Новий запис (Loan Record)]
    
    :Оновити статус книги (Issued);
    
    |Librarian|
    :Отримати підтвердження;
    :Видати книгу;
    
    :Книга (Фізичний об'єкт)]
    
    |Client|
    :Забрати книгу;
    stop
endif

@enduml
```
## BPMN Diagram
### Графічний варіант 
![BPMN](2.png)