import tkinter as tk
from tkinter import messagebox
import json
import os

class TodoApp:
    def __init__(self, root):
        self.root = root
        self.root.title("Modern To-Do List")
        self.root.geometry("450x550")
        self.root.config(bg="#f0f2f5")
        
        # Local JSON storage file
        self.FILE_NAME = "tasks.json"
        self.tasks = self.load_tasks()
        
        self.create_widgets()
        self.update_listbox()

    def create_widgets(self):
        # Header Label
        title = tk.Label(self.root, text="My Tasks", font=("Helvetica", 18, "bold"), bg="#f0f2f5", fg="#333333")
        title.pack(pady=15)
        
        # Input Frame (Entry + Add Button)
        input_frame = tk.Frame(self.root, bg="#f0f2f5")
        input_frame.pack(pady=10)
        
        self.task_entry = tk.Entry(input_frame, font=("Helvetica", 12), width=25, bd=1, relief="solid")
        self.task_entry.pack(side=tk.LEFT, padx=5, ipady=4)
        self.task_entry.bind("<Return>", lambda event: self.add_task())
        
        add_btn = tk.Button(input_frame, text="Add Task", font=("Helvetica", 10, "bold"), bg="#2ecc71", fg="white", bd=0, command=self.add_task, padx=12, pady=3, cursor="hand2")
        add_btn.pack(side=tk.LEFT, padx=5)
        
        # Task Listbox Frame
        list_frame = tk.Frame(self.root, bg="#f0f2f5")
        list_frame.pack(pady=10, fill=tk.BOTH, expand=True, padx=25)
        
        self.scrollbar = tk.Scrollbar(list_frame, orient=tk.VERTICAL)
        self.scrollbar.pack(side=tk.RIGHT, fill=tk.Y)
        
        self.task_listbox = tk.Listbox(list_frame, font=("Helvetica", 12), width=35, height=15, yscrollcommand=self.scrollbar.set, selectbackground="#3498db", bd=0, highlightthickness=0)
        self.task_listbox.pack(side=tk.LEFT, fill=tk.BOTH, expand=True)
        self.scrollbar.config(command=self.task_listbox.yview)
        
        # Action Buttons Frame
        btn_frame = tk.Frame(self.root, bg="#f0f2f5")
        btn_frame.pack(pady=20)
        
        complete_btn = tk.Button(btn_frame, text="Mark Done", font=("Helvetica", 10, "bold"), bg="#3498db", fg="white", bd=0, command=self.complete_task, padx=15, pady=6, cursor="hand2")
        complete_btn.pack(side=tk.LEFT, padx=10)
        
        delete_btn = tk.Button(btn_frame, text="Delete Task", font=("Helvetica", 10, "bold"), bg="#e74c3c", fg="white", bd=0, command=self.delete_task, padx=15, pady=6, cursor="hand2")
        delete_btn.pack(side=tk.LEFT, padx=10)

    def load_tasks(self):
        if os.path.exists(self.FILE_NAME):
            try:
                with open(self.FILE_NAME, "r") as f:
                    return json.load(f)
            except json.JSONDecodeError:
                return []
        return []

    def save_tasks(self):
        with open(self.FILE_NAME, "w") as f:
            json.dump(self.tasks, f, indent=4)

    def add_task(self):
        task_text = self.task_entry.get().strip()
        if task_text:
            self.tasks.append({"text": task_text, "done": False})
            self.save_tasks()
            self.update_listbox()
            self.task_entry.delete(0, tk.END)
        else:
            messagebox.showwarning("Warning", "Task cannot be empty!")

    def update_listbox(self):
        self.task_listbox.delete(0, tk.END)
        for task in self.tasks:
            status = "✓  " if task["done"] else "○  "
            self.task_listbox.insert(tk.END, f"{status}{task['text']}")

    def complete_task(self):
        try:
            selected_index = self.task_listbox.curselection()[0]
            self.tasks[selected_index]["done"] = True
            self.save_tasks()
            self.update_listbox()
        except IndexError:
            messagebox.showwarning("Warning", "Please select a task to mark as done!")

    def delete_task(self):
        try:
            selected_index = self.task_listbox.curselection()[0]
            del self.tasks[selected_index]
            self.save_tasks()
            self.update_listbox()
        except IndexError:
            messagebox.showwarning("Warning", "Please select a task to delete!")

if __name__ == "__main__":
    root = tk.Tk()
    app = TodoApp(root)
    root.mainloop()

