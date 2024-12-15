# Notepad
from tkinter import Tk, Text, Menu, filedialog, Scrollbar, END, Toplevel, StringVar, Entry, Button, Label
from tkinter.font import Font

# Functions
def open_file():
    file_path = filedialog.askopenfilename(filetypes=[("Text Files", "*.txt"), ("All Files", "*.*")])
    if file_path:
        with open(file_path, 'r') as file:
            text_widget.delete(1.0, END)
            text_widget.insert(END, file.read())

def save_file():
    file_path = filedialog.asksaveasfilename(defaultextension=".txt", 
                                             filetypes=[("Text Files", "*.txt"), ("All Files", "*.*")])
    if file_path:
        with open(file_path, 'w') as file:
            file.write(text_widget.get(1.0, END))

def set_font():
    """Change font size and family."""
    global current_font
    font_family = font_family_var.get()
    font_size = font_size_var.get()
    current_font.configure(family=font_family, size=font_size)

def search_text():
    """Search for a string in the text widget."""
    search_term = search_var.get()
    text_widget.tag_remove("highlight", "1.0", END)
    if search_term:
        idx = "1.0"
        while True:
            idx = text_widget.search(search_term, idx, nocase=1, stopindex=END)
            if not idx:
                break
            end_idx = f"{idx}+{len(search_term)}c"
            text_widget.tag_add("highlight", idx, end_idx)
            text_widget.tag_config("highlight", background="yellow")
            idx = end_idx

def toggle_dark_mode():
    """Switch between dark and light modes."""
    global is_dark_mode
    if is_dark_mode:
        text_widget.config(bg="white", fg="black", insertbackground="black")
        root.config(bg="white")
        is_dark_mode = False
    else:
        text_widget.config(bg="black", fg="white", insertbackground="white")
        root.config(bg="black")
        is_dark_mode = True

# Initialize Tkinter Window
root = Tk()
root.title("Notepad")
root.geometry("600x400")

# Font Settings
current_font = Font(family="Arial", size=12)

# Add Text Widget
text_widget = Text(root, wrap='word', undo=True, font=current_font)
text_widget.pack(expand=True, fill='both')

# Scrollbar
scrollbar = Scrollbar(root, command=text_widget.yview)
text_widget.configure(yscrollcommand=scrollbar.set)
scrollbar.pack(side='right', fill='y')

# Dark Mode State
is_dark_mode = False

# Menu Bar
menu_bar = Menu(root)

# File Menu
file_menu = Menu(menu_bar, tearoff=0)
file_menu.add_command(label="Open", command=open_file)
file_menu.add_command(label="Save", command=save_file)
file_menu.add_separator()
file_menu.add_command(label="Exit", command=root.quit)
menu_bar.add_cascade(label="File", menu=file_menu)

# Edit Menu
edit_menu = Menu(menu_bar, tearoff=0)
search_var = StringVar()
edit_menu.add_command(label="Search", command=lambda: search_text())
menu_bar.add_cascade(label="Edit", menu=edit_menu)

# View Menu
view_menu = Menu(menu_bar, tearoff=0)
view_menu.add_command(label="Toggle Dark Mode", command=toggle_dark_mode)
menu_bar.add_cascade(label="View", menu=view_menu)

# Font Menu
font_menu = Menu(menu_bar, tearoff=0)
font_family_var = StringVar(value="Arial")
font_size_var = StringVar(value="12")
font_menu.add_command(label="Set Font", command=set_font)
menu_bar.add_cascade(label="Font", menu=font_menu)

# Add Font and Size Inputs in Font Menu
font_menu.add_command(label="Font Family", command=lambda: font_family_var.set(input("Enter font family:")))
font_menu.add_command(label="Font Size", command=lambda: font_size_var.set(input("Enter font size:")))

# Configure Menu
root.config(menu=menu_bar)

# Run Application
root.mainloop()
