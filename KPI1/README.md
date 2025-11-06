# Lab 1
## ER-діаграми
### Нотація Чена
![ER Чена](1.png)
### Воронячі лапки
![Воронячі лапки](2.png)
### Декларативний синтаксис
```Mermaid
---
config:
  theme: default
---
erDiagram
    PUBLISHER {
        int publisher_id PK "ID (PK)"
        string name "Назва"
        string address "Адреса"
    }
    BOOK {
        int book_id PK "ID (PK)"
        string title "Назва"
        string isbn "ISBN"
        int publication_year "Рік"
        int publisher_id FK "ID Видавця (FK)"
    }
    AUTHOR {
        int author_id PK "ID (PK)"
        string first_name "Ім'я"
        string last_name "Прізвище"
    }
    MEMBER {
        int member_id PK "ID (PK)"
        string first_name "Ім'я"
        string last_name "Прізвище"
        string email "Email"
        date registration_date "Дата реєстрації"
    }
    LOAN {
        int loan_id PK "ID (PK)"
        int member_id FK "ID Читача (FK)"
        int book_id FK "ID Книги (FK)"
        date loan_date "Дата видачі"
        date due_date "Дата повернення (план)"
        date return_date "Дата повернення (факт)"
    }
    BOOK_AUTHOR {
        int book_id PK, FK "ID Книги (PK, FK)"
        int author_id PK, FK "ID Автора (PK, FK)"
    }
    PUBLISHER ||--|{ BOOK : ""
    MEMBER    ||--|{ LOAN : ""
    BOOK      ||--|{ LOAN : ""
    BOOK   }|--|| BOOK_AUTHOR : ""
    AUTHOR }|--|| BOOK_AUTHOR : ""
```

```GraphViz
graph G {
    graph [rankdir=TB];
    node [fontname="Arial"];
    edge [arrowhead=none];

    node [shape=box, style=filled, fillcolor=lightblue];
    E_PUBLISHER [label="Publisher"];
    E_BOOK [label="Book"];
    E_AUTHOR [label="Author"];
    E_MEMBER [label="Member"];
    E_LOAN [label="Loan"];

    node [shape=diamond, style=filled, fillcolor=lightyellow];
    R_PUBLISHES [label="publishes"];
    R_WRITES [label="writes"];
    R_HAS [label="has"];
    R_CONCERNS [label="concerns"];

    node [shape=ellipse, style=filled, fillcolor=lightgreen, fontsize=10];
    
    pub_id [label="publisher_id (PK)"];
    pub_name [label="name"];
    pub_addr [label="address"];

    book_id [label="book_id (PK)"];
    book_title [label="title"];
    book_isbn [label="isbn"];
    book_year [label="publication_year"];

    auth_id [label="author_id (PK)"];
    auth_fname [label="first_name"];
    auth_lname [label="last_name"];

    mem_id [label="member_id (PK)"];
    mem_fname [label="first_name"];
    mem_lname [label="last_name"];
    mem_email [label="email"];
    mem_date [label="registration_date"];

    loan_id [label="loan_id (PK)"];
    loan_date [label="loan_date"];
    loan_due [label="due_date"];
    loan_return [label="return_date"];
    
    E_PUBLISHER -- pub_id;
    E_PUBLISHER -- pub_name;
    E_PUBLISHER -- pub_addr;
    
    E_BOOK -- book_id;
    E_BOOK -- book_title;
    E_BOOK -- book_isbn;
    E_BOOK -- book_year;
    
    E_AUTHOR -- auth_id;
    E_AUTHOR -- auth_fname;
    E_AUTHOR -- auth_lname;
    
    E_MEMBER -- mem_id;
    E_MEMBER -- mem_fname;
    E_MEMBER -- mem_lname;
    E_MEMBER -- mem_email;
    E_MEMBER -- mem_date;
    
    E_LOAN -- loan_id;
    E_LOAN -- loan_date;
    E_LOAN -- loan_due;
    E_LOAN -- loan_return;
    
    E_PUBLISHER -- R_PUBLISHES [label=" 1"];
    R_PUBLISHES -- E_BOOK [label=" N"];
    
    E_AUTHOR -- R_WRITES [label=" M"];
    R_WRITES -- E_BOOK [label=" N"];
    
    E_MEMBER -- R_HAS [label=" 1"];
    R_HAS -- E_LOAN [label=" N"];
    
    E_BOOK -- R_CONCERNS [label=" 1"];
    R_CONCERNS -- E_LOAN [label=" N"];
}
```
### Модель у середовищі програмування
```
from dataclasses import dataclass
from datetime import date
from typing import Optional

@dataclass
class Publisher:
    publisher_id: int
    name: str
    address: str

@dataclass
class Author:
    author_id: int
    first_name: str
    last_name: str

@dataclass
class Book:
    book_id: int
    title: str
    isbn: str
    publication_year: int
    publisher_id: int

@dataclass
class Member:
    member_id: int
    first_name: str
    last_name: str
    email: str
    registration_date: date

@dataclass
class Loan:
    loan_id: int
    member_id: int
    book_id: int
    loan_date: date
    due_date: date
    return_date: Optional[date] = None

@dataclass
class BookAuthor:
    book_id: int
    author_id: int

publisher = Publisher(publisher_id=1, name="Видавництво Старого Лева", address="м. Львів, вул. Галицька, 1")

author1 = Author(author_id=10, first_name="Іван", last_name="Франко")
author2 = Author(author_id=11, first_name="Леся", last_name="Українка")

book = Book(
    book_id=101,
    title="Зів'яле листя",
    isbn="978-617-679-111-0",
    publication_year=1896,
    publisher_id=publisher.publisher_id
)

book_author_link = BookAuthor(book_id=book.book_id, author_id=author1.author_id)

member = Member(
    member_id=501,
    first_name="Олена",
    last_name="Пчілка",
    email="olena.pchilka@example.com",
    registration_date=date(2024, 10, 26)
)

loan = Loan(
    loan_id=1001,
    member_id=member.member_id,
    book_id=book.book_id,
    loan_date=date.today(),
    due_date=date(2025, 11, 9)
)

print("Видавець:")
print(publisher)

print("\nАвтор:")
print(author1)

print("\nКнига:")
print(book)
print(f"(Зв'язок: ID видавця цієї книги - {book.publisher_id})")


print("\nЧитач:")
print(member)

print("\nВидача:")
print(loan)
print(f"(Зв'язок: Цю видачу здійснив читач з ID {loan.member_id} для книги з ID {loan.book_id})")

input("\nEnter")
```