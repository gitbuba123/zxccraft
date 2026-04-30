import tkinter as tk
import random
from tkinter import messagebox

class Minesweeper:
    def __init__(self, root, rows=10, cols=10, mines=10):
        self.root = root
        self.rows = rows
        self.cols = cols
        self.mines_count = mines
        self.cells = []
        self.mine_locations = set()
        
        # Создаем поле
        self.setup_ui()
        self.generate_mines()

    def setup_ui(self):
        for r in range(self.rows):
            row_cells = []
            for c in range(self.cols):
                btn = tk.Button(self.root, width=3, height=1, font=('Arial', 12, 'bold'),
                                command=lambda r=r, c=c: self.click(r, c))
                btn.grid(row=r, column=c)
                btn.bind("<Button-3>", lambda e, r=r, c=c: self.set_flag(r, c)) # ПКМ
                row_cells.append(btn)
            self.cells.append(row_cells)

    def generate_mines(self):
        while len(self.mine_locations) < self.mines_count:
            r, c = random.randint(0, self.rows-1), random.randint(0, self.cols-1)
            self.mine_locations.add((r, c))

    def count_mines(self, r, c):
        count = 0
        for dr in [-1, 0, 1]:
            for dc in [-1, 0, 1]:
                if (r + dr, c + dc) in self.mine_locations:
                    count += 1
        return count

    def click(self, r, c):
        if (r, c) in self.mine_locations:
            self.cells[r][c].config(text="💣", bg="red")
            messagebox.showinfo("Игра окончена", "Бум! Вы проиграли.")
            self.root.destroy()
        else:
            self.reveal(r, c)

    def reveal(self, r, c):
        if self.cells[r][c]["state"] == "disabled":
            return
        
        mines = self.count_mines(r, c)
        self.cells[r][c].config(text=str(mines) if mines > 0 else "", 
                                 state="disabled", relief=tk.SUNKEN, bg="#eee")
        
        if mines == 0:
            for dr in [-1, 0, 1]:
                for dc in [-1, 0, 1]:
                    nr, nc = r + dr, c + dc
                    if 0 <= nr < self.rows and 0 <= nc < self.cols:
                        self.reveal(nr, nc)

    def set_flag(self, r, c):
        if self.cells[r][c]["text"] == "🚩":
            self.cells[r][c].config(text="")
        elif self.cells[r][c]["state"] != "disabled":
            self.cells[r][c].config(text="🚩", fg="red")

if __name__ == "__main__":
    root = tk.Tk()
    root.title("Python Minesweeper")
    game = Minesweeper(root)
    root.mainloop()
    
