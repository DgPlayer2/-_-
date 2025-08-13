Вот детальное объяснение каждой части кода:

---

### **1. Импорт библиотеки**
```python
from tkinter import *
```
- Импортируется вся библиотека `tkinter` для создания графического интерфейса.

---

### **2. Функция `btn_click(item)` - обработка нажатия кнопок**
```python
def btn_click(item):
    global expression
    try:
        input_field['state'] = "normal"  # Разблокируем поле ввода
        
        if item == '=':  # Если нажата кнопка "="
            if expression and expression[-1].isdigit():  # Проверяем, что выражение заканчивается цифрой
                result = str(eval(expression))  # Вычисляем выражение
                input_field.delete(0, END)  # Очищаем поле
                input_field.insert(END, result)  # Выводим результат
                expression = result  # Сохраняем результат для продолжения вычислений
            else:
                input_field.delete(0, END)
                input_field.insert(END, 'Ошибка: некорректное выражение')
        else:  # Если нажата любая другая кнопка
            expression += item  # Добавляем символ к выражению
            input_field.insert(END, item)  # Выводим символ в поле

        input_field['state'] = "readonly"  # Блокируем поле ввода
    except ZeroDivisionError:  # Обработка деления на ноль
        input_field.delete(0, END)
        input_field.insert(0, 'Ошибка: деление на ноль')
        expression = ""
    except (SyntaxError, NameError, TypeError):  # Другие ошибки
        input_field.delete(0, END)
        input_field.insert(0, 'Ошибка: некорректный ввод')
        expression = ""
```
- **`expression`** - глобальная переменная для хранения вводимого выражения.
- **`eval()`** - вычисляет математическое выражение (опасно для production-кода!).
- Обработка ошибок: деление на ноль и некорректный синтаксис.

---

### **3. Функция `bt_clear()` - очистка калькулятора**
```python
def bt_clear():
    global expression
    expression = ""  # Сбрасываем выражение
    input_field['state'] = "normal"  # Разблокируем поле
    input_field.delete(0, END)  # Очищаем поле
    input_field['state'] = "readonly"  # Блокируем обратно
```
- Полная очистка поля ввода и сброс выражения.

---

### **4. Создание главного окна**
```python
root = Tk()  # Создаем главное окно
root.geometry("350x450")  # Начальный размер окна
root.title("Графический калькулятор с кнопками (tkinter)")  # Заголовок
root.resizable(1, 1)  # Разрешаем изменение размера окна
```
- Настройки основного окна приложения.

---

### **5. Настройка сетки (grid)**
```python
for i in range(6):
    root.grid_rowconfigure(i, weight=1)  # 6 строк с весом 1
for i in range(4):
    root.grid_columnconfigure(i, weight=1)  # 4 столбца с весом 1
```
- Делает ячейки адаптивными при изменении размера окна.

---

### **6. Поле ввода**
```python
frame_input = Frame(root)  # Рамка для поля ввода
frame_input.grid(row=0, column=0, columnspan=4, sticky="nsew", padx=5, pady=5)

input_field = Entry(frame_input, font='Arial 15 bold', state="readonly")
input_field.pack(fill=BOTH, expand=True, ipady=10)  # Растягиваем на всю ширину
```
- **`sticky="nsew"`** - прикрепляет ко всем сторонам ячейки.
- **`ipady=10`** - внутренний отступ по вертикали.

---

### **7. Создание кнопок**
```python
buttons = (('7', '8', '9', '/'),
           ('4', '5', '6', '*'),
           ('1', '2', '3', '-'),
           ('0', '.', '=', '+'))

# Кнопка очистки
Button(root, text='C', command=bt_clear, font='Arial 12').grid(
    row=1, column=3, sticky="nsew", padx=2, pady=2)

# Создаем цифровые кнопки
for row in range(4):
    for col in range(4):
        btn = Button(root, text=buttons[row][col], font='Arial 12',
               command=lambda r=row, c=col: btn_click(buttons[r][c]))
        btn.grid(row=row + 2, column=col, sticky="nsew", padx=2, pady=2)
        btn.config(borderwidth=1, relief="raised")  # Стиль кнопки
```
- Матрица кнопок 4x4.
- **`lambda`** - передает правильные значения row и col в обработчик.

---

### **8. Обработчик изменения размера окна**
```python
def on_resize(event):
    new_font_size = max(10, min(20, int(event.width/25)))  # Вычисляем размер шрифта
    input_field.config(font=('Arial', new_font_size, 'bold'))
    
    for child in root.winfo_children():  # Для всех кнопок
        if isinstance(child, Button):
            child.config(font=('Arial', max(8, min(16, int(event.width/30)))))

root.bind('<Configure>', on_resize)  # Привязываем обработчик
```
- Динамически меняет размер шрифта в зависимости от ширины окна.
- Ограничивает минимальный и максимальный размер шрифта.

---

### **9. Запуск приложения**
```python
root.mainloop()  # Главный цикл обработки событий
```
- Без этой строки окно не будет отображаться.

---

### **Ключевые особенности:**
1. **Адаптивный интерфейс** - подстраивается под размер окна
2. **Обработка ошибок** - корректно реагирует на некорректный ввод
3. **Чистый код** - логика отделена от интерфейса
4. **Гибкость** - легко добавить новые функции

Этот код демонстрирует хорошие практики создания GUI на Tkinter, хотя в production-коде следует избегать использования `eval()` из соображений безопасности.
