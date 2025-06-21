import tkinter as tk
from tkinter import ttk, messagebox
import json
import os
from collections import Counter

class VibrantPollApp:
    def __init__(self, root):
        self.root = root
        self.root.title("🌟 Система проведения опросов")
        self.root.geometry("950x700")
        self.root.configure(bg='#f5f7fa')
        
        # Яркая цветовая схема
        self.colors = {
            'background': '#f5f7fa',
            'primary': '#4e73df',
            'primary_hover': '#2e59d9',
            'secondary': '#1cc88a',
            'secondary_hover': '#17a673',
            'accent': '#f6c23e',
            'accent_hover': '#dda20a',
            'danger': '#e74a3b',
            'danger_hover': '#be2617',
            'text': '#2e3d49',
            'light': '#ffffff',
            'success': '#1cc88a',
            'warning': '#f6c23e',
            'error': '#e74a3b',
            'card_bg': '#ffffff'
        }
        
        # Данные приложения
        self.polls = {}
        self.responses = {}
        self.current_poll = None
        self.current_question = 0
        self.user_answers = []
        
        # Загрузка данных
        self.load_data()
        
        # Создание стилей
        self.create_styles()
        
        # Создание интерфейса
        self.create_main_menu()

    def create_styles(self):
        """Создание стилей для виджетов"""
        style = ttk.Style()
        
        # Общие настройки
        style.theme_use('clam')
        
        # Настройка фреймов
        style.configure('TFrame', background=self.colors['background'])
        style.configure('Card.TFrame', background=self.colors['card_bg'], 
                      borderwidth=2, relief='groove', bordercolor='#dddfeb')
        
        # Настройка меток
        style.configure('TLabel', background=self.colors['background'], 
                      foreground=self.colors['text'], font=('Segoe UI', 10))
        style.configure('Title.TLabel', font=('Segoe UI', 18, 'bold'), 
                      foreground=self.colors['primary'])
        style.configure('Subtitle.TLabel', font=('Segoe UI', 12), 
                      foreground=self.colors['text'])
        style.configure('Question.TLabel', font=('Segoe UI', 11, 'bold'), 
                      foreground=self.colors['text'])
        style.configure('Result.TLabel', font=('Segoe UI', 10), 
                      foreground=self.colors['text'])
        
        # Настройка кнопок
        button_font = ('Segoe UI', 10, 'bold')
        
        style.configure('Primary.TButton', 
                       background=self.colors['primary'],
                       foreground='white',
                       font=button_font,
                       borderwidth=1,
                       focusthickness=3,
                       focuscolor=self.colors['primary'],
                       padding=10)
        style.map('Primary.TButton',
                background=[('active', self.colors['primary_hover']),
                          ('pressed', self.colors['primary_hover'])])
        
        style.configure('Success.TButton', 
                      background=self.colors['secondary'],
                      foreground='white',
                      font=button_font,
                      padding=10)
        style.map('Success.TButton',
                background=[('active', self.colors['secondary_hover']),
                          ('pressed', self.colors['secondary_hover'])])
        
        style.configure('Accent.TButton', 
                      background=self.colors['accent'],
                      foreground='white',
                      font=button_font,
                      padding=10)
        style.map('Accent.TButton',
                background=[('active', self.colors['accent_hover']),
                          ('pressed', self.colors['accent_hover'])])
        
        style.configure('Danger.TButton', 
                      background=self.colors['danger'],
                      foreground='white',
                      font=button_font,
                      padding=10)
        style.map('Danger.TButton',
                background=[('active', self.colors['danger_hover']),
                          ('pressed', self.colors['danger_hover'])])
        
        style.configure('Poll.TButton', 
                      background=self.colors['light'],
                      foreground=self.colors['primary'],
                      font=('Segoe UI', 10, 'bold'),
                      padding=8,
                      borderwidth=1,
                      relief='raised')
        style.map('Poll.TButton',
                background=[('active', '#f0f2f5')])
        
        # Настройка радиокнопок
        style.configure('TRadiobutton', 
                       background=self.colors['background'],
                       foreground=self.colors['text'],
                       font=('Segoe UI', 10))
        
        # Настройка Treeview
        style.configure('Treeview', 
                      background=self.colors['light'],
                      foreground=self.colors['text'],
                      rowheight=25,
                      fieldbackground=self.colors['light'])
        style.configure('Treeview.Heading', 
                      background=self.colors['primary'],
                      foreground='white',
                      font=('Segoe UI', 10, 'bold'))
        style.map('Treeview', 
                 background=[('selected', self.colors['primary'])],
                 foreground=[('selected', 'white')])

    def load_data(self):
        """Загрузка данных из файлов"""
        try:
            if os.path.exists('polls.json'):
                with open('polls.json', 'r') as f:
                    self.polls = json.load(f)
            
            if os.path.exists('responses.json'):
                with open('responses.json', 'r') as f:
                    self.responses = json.load(f)
        except Exception as e:
            messagebox.showerror("Ошибка", f"Не удалось загрузить данные: {str(e)}")

    def save_data(self):
        """Сохранение данных в файлы"""
        try:
            with open('polls.json', 'w') as f:
                json.dump(self.polls, f, indent=4)
            
            with open('responses.json', 'w') as f:
                json.dump(self.responses, f, indent=4)
        except Exception as e:
            messagebox.showerror("Ошибка", f"Не удалось сохранить данные: {str(e)}")

    def clear_frame(self):
        """Очистка текущего фрейма"""
        for widget in self.root.winfo_children():
            widget.destroy()

    def create_main_menu(self):
        """Главное меню"""
        self.clear_frame()
        
        main_frame = ttk.Frame(self.root, style='TFrame')
        main_frame.pack(fill='both', expand=True, padx=30, pady=30)
        
        # Заголовок
        header_frame = ttk.Frame(main_frame)
        header_frame.pack(fill='x', pady=(0, 20))
        
        ttk.Label(header_frame, text="🌟 Система проведения опросов", style='Title.TLabel').pack()
        ttk.Label(header_frame, text="Создавайте опросы, собирайте ответы и анализируйте результаты", 
                 style='Subtitle.TLabel').pack()
        
        # Кнопки основного меню
        buttons_frame = ttk.Frame(main_frame)
        buttons_frame.pack(fill='x', pady=20)
        
        buttons = [
            ("📝 Создать опрос", self.create_poll_menu, 'Primary.TButton'),
            ("✏️ Редактировать опросы", self.edit_polls_menu, 'Primary.TButton'),
            ("📊 Пройти опрос", self.take_poll_menu, 'Success.TButton'),
            ("📈 Анализ результатов", self.view_results_menu, 'Accent.TButton'),
            ("🚪 Выход", self.root.quit, 'Danger.TButton')
        ]
        
        for text, command, style_name in buttons:
            btn = ttk.Button(buttons_frame, text=text, command=command, style=style_name)
            btn.pack(fill='x', pady=8, ipady=12)

    def create_poll_menu(self):
        """Меню создания опроса"""
        self.clear_frame()
        
        main_frame = ttk.Frame(self.root, style='TFrame')
        main_frame.pack(fill='both', expand=True, padx=30, pady=30)
        
        # Заголовок
        ttk.Label(main_frame, text="📝 Создание нового опроса", style='Title.TLabel').pack(pady=(0, 20))
        
        # Карточка формы
        form_card = ttk.Frame(main_frame, style='Card.TFrame')
        form_card.pack(fill='x', pady=10, padx=10, ipadx=10, ipady=10)
        
        # Поля для ввода
        ttk.Label(form_card, text="Название опроса:").pack(anchor='w', pady=(5, 0))
        self.poll_title = ttk.Entry(form_card, font=('Segoe UI', 10))
        self.poll_title.pack(fill='x', pady=5, ipady=8)
        
        self.questions = []
        self.current_question_frame = None
        
        # Кнопки управления
        buttons_frame = ttk.Frame(main_frame)
        buttons_frame.pack(fill='x', pady=20)
        
        ttk.Button(buttons_frame, text="➕ Добавить вопрос", command=self.add_question, 
                  style='Primary.TButton').pack(side='left', padx=5, ipadx=10)
        ttk.Button(buttons_frame, text="💾 Сохранить опрос", command=self.save_poll, 
                  style='Success.TButton').pack(side='left', padx=5, ipadx=10)
        ttk.Button(buttons_frame, text="🔙 Назад", command=self.create_main_menu, 
                  style='Danger.TButton').pack(side='right', padx=5, ipadx=10)
        
        self.add_question()

    def add_question(self):
        """Добавление нового вопроса"""
        if self.current_question_frame:
            self.current_question_frame.pack_forget()
        
        # Карточка вопроса
        self.current_question_frame = ttk.Frame(self.root, style='Card.TFrame')
        self.current_question_frame.pack(fill='x', padx=30, pady=10, ipadx=10, ipady=10)
        
        # Поля для вопроса
        ttk.Label(self.current_question_frame, text="Текст вопроса:").pack(anchor='w', pady=(5, 0))
        question_text = ttk.Entry(self.current_question_frame, font=('Segoe UI', 10))
        question_text.pack(fill='x', pady=5, ipady=8)
        
        # Варианты ответов
        ttk.Label(self.current_question_frame, text="Варианты ответов:").pack(anchor='w', pady=(10, 5))
        options_frame = ttk.Frame(self.current_question_frame)
        options_frame.pack(fill='x', pady=5)
        
        options = []
        
        def add_option():
            option_frame = ttk.Frame(options_frame)
            option_frame.pack(fill='x', pady=3)
            
            option = ttk.Entry(option_frame, font=('Segoe UI', 10))
            option.pack(side='left', fill='x', expand=True, padx=(0, 5), ipady=5)
            
            remove_btn = ttk.Button(option_frame, text="✖", width=3, style='Danger.TButton',
                                  command=lambda f=option_frame: f.pack_forget())
            remove_btn.pack(side='right')
            
            options.append(option)
        
        # Кнопка добавления варианта
        ttk.Button(self.current_question_frame, text="➕ Добавить вариант", 
                  command=add_option, style='Accent.TButton').pack(anchor='w', pady=5)
        
        # Сохраняем данные для текущего вопроса
        self.questions.append({
            'frame': self.current_question_frame,
            'text': question_text,
            'options': options,
            'add_option': add_option
        })

    def save_poll(self):
        """Сохранение опроса"""
        title = self.poll_title.get()
        if not title:
            messagebox.showerror("Ошибка", "Введите название опроса")
            return
        
        poll_questions = []
        for question in self.questions:
            q_text = question['text'].get()
            if not q_text:
                messagebox.showerror("Ошибка", "Введите текст вопроса")
                return
            
            q_options = []
            for option in question['options']:
                opt_text = option.get()
                if opt_text:
                    q_options.append(opt_text)
            
            if len(q_options) < 2:
                messagebox.showerror("Ошибка", "Должно быть хотя бы 2 варианта ответа")
                return
            
            poll_questions.append({
                'text': q_text,
                'options': q_options
            })
        
        poll_id = str(len(self.polls) + 1)
        self.polls[poll_id] = {
            'title': title,
            'questions': poll_questions
        }
        
        self.save_data()
        messagebox.showinfo("Успех", f"Опрос '{title}' успешно создан!")
        self.create_main_menu()

    def edit_polls_menu(self):
        """Меню редактирования опросов"""
        self.clear_frame()
        
        main_frame = ttk.Frame(self.root, style='TFrame')
        main_frame.pack(fill='both', expand=True, padx=30, pady=30)
        
        ttk.Label(main_frame, text="✏️ Редактирование опросов", style='Title.TLabel').pack(pady=(0, 20))
        
        if not self.polls:
            ttk.Label(main_frame, text="Нет доступных опросов").pack()
            ttk.Button(main_frame, text="🔙 Назад", command=self.create_main_menu, 
                      style='Danger.TButton').pack(pady=20, ipadx=10, ipady=5)
            return
        
        # Карточка с таблицей
        table_card = ttk.Frame(main_frame, style='Card.TFrame')
        table_card.pack(fill='both', expand=True, padx=10, pady=10, ipadx=5, ipady=5)
        
        # Таблица с опросами
        tree_frame = ttk.Frame(table_card)
        tree_frame.pack(fill='both', expand=True)
        
        tree = ttk.Treeview(tree_frame, columns=('id', 'title', 'questions'), show='headings')
        tree.heading('id', text='ID')
        tree.heading('title', text='Название')
        tree.heading('questions', text='Вопросов')
        
        vsb = ttk.Scrollbar(tree_frame, orient="vertical", command=tree.yview)
        hsb = ttk.Scrollbar(tree_frame, orient="horizontal", command=tree.xview)
        tree.configure(yscrollcommand=vsb.set, xscrollcommand=hsb.set)
        
        tree.grid(row=0, column=0, sticky='nsew')
        vsb.grid(row=0, column=1, sticky='ns')
        hsb.grid(row=1, column=0, sticky='ew')
        
        tree_frame.grid_rowconfigure(0, weight=1)
        tree_frame.grid_columnconfigure(0, weight=1)
        
        for poll_id, poll in self.polls.items():
            tree.insert('', 'end', values=(poll_id, poll['title'], len(poll['questions'])))
        
        # Кнопки управления
        buttons_frame = ttk.Frame(main_frame)
        buttons_frame.pack(fill='x', pady=20)
        
        ttk.Button(buttons_frame, text="🗑️ Удалить", command=lambda: self.delete_poll(tree), 
                  style='Danger.TButton').pack(side='left', padx=5, ipadx=10)
        ttk.Button(buttons_frame, text="🔙 Назад", command=self.create_main_menu, 
                  style='Danger.TButton').pack(side='right', padx=5, ipadx=10)

    def delete_poll(self, tree):
        """Удаление выбранного опроса"""
        selected = tree.selection()
        if not selected:
            messagebox.showerror("Ошибка", "Выберите опрос для удаления")
            return
        
        item = tree.item(selected[0])
        poll_id = item['values'][0]
        
        confirm = messagebox.askyesno("Подтверждение", 
                                    f"Удалить опрос '{self.polls[poll_id]['title']}'?")
        if confirm:
            del self.polls[poll_id]
            if poll_id in self.responses:
                del self.responses[poll_id]
            self.save_data()
            self.edit_polls_menu()

    def take_poll_menu(self):
        """Меню прохождения опроса"""
        self.clear_frame()
        
        main_frame = ttk.Frame(self.root, style='TFrame')
        main_frame.pack(fill='both', expand=True, padx=30, pady=30)
        
        ttk.Label(main_frame, text="📊 Пройти опрос", style='Title.TLabel').pack(pady=(0, 20))
        
        if not self.polls:
            ttk.Label(main_frame, text="Нет доступных опросов").pack()
            ttk.Button(main_frame, text="🔙 Назад", command=self.create_main_menu, 
                      style='Danger.TButton').pack(pady=20, ipadx=10, ipady=5)
            return
        
        # Карточка с опросами
        polls_card = ttk.Frame(main_frame, style='Card.TFrame')
        polls_card.pack(fill='both', expand=True, padx=10, pady=10, ipadx=5, ipady=5)
        
        # Список опросов
        for poll_id, poll in self.polls.items():
            btn = ttk.Button(polls_card, 
                           text=f"📋 {poll['title']}\nВопросов: {len(poll['questions'])}", 
                           command=lambda pid=poll_id: self.start_poll(pid),
                           style='Poll.TButton')
            btn.pack(fill='x', pady=8, ipady=10)
        
        ttk.Button(main_frame, text="🔙 Назад", command=self.create_main_menu, 
                  style='Danger.TButton').pack(pady=20, ipadx=10, ipady=5)

    def start_poll(self, poll_id):
        """Начало прохождения опроса"""
        self.current_poll = poll_id
        self.current_question = 0
        self.user_answers = []
        self.show_question()

    def show_question(self):
        """Отображение текущего вопроса"""
        self.clear_frame()
        
        poll = self.polls[self.current_poll]
        question = poll['questions'][self.current_question]
        
        main_frame = ttk.Frame(self.root, style='TFrame')
        main_frame.pack(fill='both', expand=True, padx=30, pady=30)
        
        # Заголовок
        ttk.Label(main_frame, text=poll['title'], style='Title.TLabel').pack(pady=(0, 5))
        ttk.Label(main_frame, text=f"Вопрос {self.current_question + 1}/{len(poll['questions'])}", 
                 style='Subtitle.TLabel').pack(pady=(0, 20))
        
        # Карточка вопроса
        question_card = ttk.Frame(main_frame, style='Card.TFrame')
        question_card.pack(fill='x', pady=10, padx=10, ipadx=15, ipady=15)
        
        ttk.Label(question_card, text=question['text'], style='Question.TLabel').pack(anchor='w', pady=(0, 15))
        
        self.answer_var = tk.StringVar()
        
        # Варианты ответов
        for i, option in enumerate(question['options']):
            rb = ttk.Radiobutton(question_card, 
                                text=option, 
                                variable=self.answer_var, 
                                value=option,
                                style='TRadiobutton')
            rb.pack(anchor='w', padx=10, pady=5, ipady=3)
        
        # Кнопки навигации
        nav_frame = ttk.Frame(main_frame)
        nav_frame.pack(fill='x', pady=20)
        
        if self.current_question > 0:
            ttk.Button(nav_frame, text="⬅️ Назад", command=self.prev_question, 
                      style='Primary.TButton').pack(side='left', padx=5, ipadx=10)
        
        if self.current_question < len(poll['questions']) - 1:
            ttk.Button(nav_frame, text="Далее ➡️", command=self.next_question, 
                      style='Success.TButton').pack(side='right', padx=5, ipadx=10)
        else:
            ttk.Button(nav_frame, text="✅ Завершить", command=self.finish_poll, 
                      style='Success.TButton').pack(side='right', padx=5, ipadx=10)

    def next_question(self):
        """Переход к следующему вопросу"""
        if not self.answer_var.get():
            messagebox.showerror("Ошибка", "Выберите вариант ответа")
            return
        
        self.user_answers.append(self.answer_var.get())
        self.current_question += 1
        self.show_question()

    def prev_question(self):
        """Возврат к предыдущему вопросу"""
        self.current_question -= 1
        self.user_answers.pop()
        self.show_question()

    def finish_poll(self):
        """Завершение опроса"""
        if not self.answer_var.get():
            messagebox.showerror("Ошибка", "Выберите вариант ответа")
            return
        
        self.user_answers.append(self.answer_var.get())
        
        # Сохранение ответов
        if self.current_poll not in self.responses:
            self.responses[self.current_poll] = []
        
        self.responses[self.current_poll].append(self.user_answers)
        self.save_data()
        
        messagebox.showinfo("Спасибо!", "Ваши ответы сохранены")
        self.create_main_menu()

    def view_results_menu(self):
        """Меню просмотра результатов"""
        self.clear_frame()
        
        main_frame = ttk.Frame(self.root, style='TFrame')
        main_frame.pack(fill='both', expand=True, padx=30, pady=30)
        
        ttk.Label(main_frame, text="📈 Результаты опросов", style='Title.TLabel').pack(pady=(0, 20))
        
        if not self.polls:
            ttk.Label(main_frame, text="Нет доступных опросов").pack()
            ttk.Button(main_frame, text="🔙 Назад", command=self.create_main_menu, 
                      style='Danger.TButton').pack(pady=20, ipadx=10, ipady=5)
            return
        
        # Карточка с опросами
        polls_card = ttk.Frame(main_frame, style='Card.TFrame')
        polls_card.pack(fill='both', expand=True, padx=10, pady=10, ipadx=5, ipady=5)
        
        # Список опросов с количеством ответов
        for poll_id, poll in self.polls.items():
            response_count = len(self.responses.get(poll_id, []))
            btn_text = f"📊 {poll['title']} (ответов: {response_count})"
            
            btn = ttk.Button(polls_card, 
                           text=btn_text,
                           command=lambda pid=poll_id: self.show_results(pid),
                           style='Poll.TButton')
            btn.pack(fill='x', pady=8, ipady=10)
        
        ttk.Button(main_frame, text="🔙 Назад", command=self.create_main_menu, 
                  style='Danger.TButton').pack(pady=20, ipadx=10, ipady=5)

    def show_results(self, poll_id):
        """Отображение результатов опроса"""
        self.clear_frame()
        
        poll = self.polls[poll_id]
        responses = self.responses.get(poll_id, [])
        
        if not responses:
            ttk.Label(self.root, text="На этот опрос пока нет ответов").pack()
            ttk.Button(self.root, text="🔙 Назад", command=self.view_results_menu, 
                      style='Danger.TButton').pack(pady=20, ipadx=10, ipady=5)
            return
        
        main_frame = ttk.Frame(self.root, style='TFrame')
        main_frame.pack(fill='both', expand=True, padx=30, pady=30)
        
        # Заголовок
        ttk.Label(main_frame, text=f"📊 Результаты опроса: {poll['title']}", 
                 style='Title.TLabel').pack(pady=(0, 5))
        ttk.Label(main_frame, text=f"Всего ответов: {len(responses)}", 
                 style='Subtitle.TLabel').pack(pady=(0, 20))
        
        # Вкладки для вопросов
        notebook = ttk.Notebook(main_frame)
        notebook.pack(fill='both', expand=True)
        
        for q_idx, question in enumerate(poll['questions']):
            # Создаем вкладку
            tab = ttk.Frame(notebook, style='Card.TFrame')
            notebook.add(tab, text=f"❓ Вопрос {q_idx + 1}")
            
            # Заголовок вопроса
            ttk.Label(tab, text=question['text'], style='Question.TLabel').pack(pady=(10, 15))
            
            # Собираем статистику
            answers = [r[q_idx] for r in responses]
            answer_counts = Counter(answers)
            total = len(answers)
            
            # Отображаем результаты
            for option in question['options']:
                count = answer_counts.get(option, 0)
                percent = count / total * 100 if total > 0 else 0
                
                # Фрейм для результата
                result_frame = ttk.Frame(tab)
                result_frame.pack(fill='x', pady=5, padx=10)
                
                # Текст результата
                ttk.Label(result_frame, 
                         text=f"🔹 {option}: {count} ({percent:.1f}%)", 
                         style='Result.TLabel').pack(anchor='w')
                
                # Прогресс-бар
                progress_frame = ttk.Frame(result_frame)
                progress_frame.pack(fill='x', pady=2)
                
                bar_width = int(percent * 2)
                bar_color = self.get_bar_color(percent)
                
                ttk.Label(progress_frame, background=bar_color).pack(side='left', ipady=8, ipadx=bar_width)
                
                # Добавляем отступ между результатами
                ttk.Frame(tab, height=5).pack()
        
        # Кнопка назад
        ttk.Button(main_frame, text="🔙 Назад", command=self.view_results_menu, 
                  style='Danger.TButton').pack(pady=20, ipadx=10, ipady=5)

    def get_bar_color(self, percent):
        """Возвращает цвет для диаграммы в зависимости от процента"""
        if percent > 75:
            return '#1cc88a'  # Ярко-зеленый
        elif percent > 50:
            return '#36b9cc'  # Бирюзовый
        elif percent > 25:
            return '#f6c23e'  # Желтый
        else:
            return '#e74a3b'  # Красный

if __name__ == "__main__":
    root = tk.Tk()
    
    # Установка иконки приложения
    try:
        root.iconbitmap('poll_icon.ico')  # Убедитесь, что файл существует
    except:
        pass
    
    app = VibrantPollApp(root)
    root.mainloop()
