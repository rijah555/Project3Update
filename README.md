# Project3Update


# Imports
import tkinter as tk
from tkinter import ttk
from tkinter import filedialog
import datetime # Module for working with dates and times
from functools import partial # Module for passing arguments in display_note command


class MainWindow(tk.Tk):
    def __init__(self): # Initialize the main window
        super().__init__() # Initialize as a tkinter window
        
        self.geometry("600x400") # Set the default window size
        self.title('Notebook') # Set the default window title
        self.notebook = [] # Initialize an attribute named 'notebook' as an empty list    
        
    def new_note(self): # For creating new notes
        
        # Initialize instance of NoteForm
        note_form = NoteForm(self, self.notebook)
        
        return None

    
    def open_note(self): # For opening previously saved notes in new window 
        
        # Open file path
        filepath = filedialog.askopenfilename(initialdir = "/Users/baihanliang/INST326-0104",
                                         filetypes=[("text files", "*.txt"), 
                                                    ("csv files", ".csv"),
                                         ("all files", "*.*")])
        file = open(filepath, "r")
        file_list = file.read()
        
        #file_list.read
        
        # Open new window for file text to be displayed
        note_window = tk.Toplevel() 
        note_window.geometry("400x300")
        note_window.title("Note")
        display_text = file_list
        text_label = tk.Label(note_window, text = display_text, justify = tk.LEFT)
        text_label.grid(padx = 10, pady = 10, row = 0, column = 0)
        file.close()
        
        return None
   
    def display_note(self, note_to_display): # For displaying created notes in new window
        
        # Create new window
        note_window = tk.Toplevel() 
        note_window.geometry("400x300")
        note_window.title("Note")
        display_text = f'\n{note_to_display.note_title}\n{note_to_display.note_text}\n{note_to_display.note_link}\n{note_to_display.note_tags}\n{note_to_display.note_meta}\n'
        text_label = tk.Label(note_window, text = display_text, justify = tk.LEFT)
        text_label.grid(padx = 10, pady = 10, row = 0, column = 0)

    def save_note(self): # For saving notes to computer
        
        # Ask to save note as file
        file = filedialog.asksaveasfile(initialdir = "/Users/baihanliang/INST326-0104",
                                          defaultextension = ".txt", 
                                          filetypes=[("text file", ".txt"),
                                         ("all files", ".*")])
        
        # Create frame for notes to be displayed in main window 
        note_frame = tk.Frame(main_window)
        note_frame.grid(padx = 10, row = 0, column = 1)
        
        # Iterate through each note in 'notebook', writing each one to the file
        for note in self.notebook:
            
            file.write(f'\n{note.note_title}\n{note.note_text}\n{note.note_link}\n{note.note_tags}\n{note.note_meta}\n')
            
            # Add button representing note to main window
            note_button = tk.Button(note_frame, text = note.note_title, command = partial(main_window.display_note, note))
            note_button.grid(padx = 10, pady= 10, row = self.notebook.index(note)+1, column = 1)
            
        file.close()
        
        return None
    
    def edit_note(self):
        
        # Opening the selected file
        filepath = filedialog.askopenfilename(initialdir = "/Users/baihanliang/INST326-0104",
                                         filetypes=[("text files", "*.txt"), 
                                                    ("csv files", ".csv"),
                                         ("all files", "*.*")])
        file = open(filepath, "r")
        file_text = file.read()
        # Splitting current note contents up for default text in entry spaces
        file_list = file_text.strip().split("\n")
        form_title = file_list[0]
        form_text = file_list[1]
        form_link = file_list[2]
        form_tags = file_list[3]
        
        # Creating top level window
        note_window = tk.Toplevel() 
        note_window.geometry("400x350")
        note_window.title("Note")
        note_frame = tk.Frame(note_window)
        note_frame.pack(expand = True)
        
        # Title label
        title_label = tk.Label(note_frame, bg = 'light gray', text = 'Note Title:')
        title_label.grid(padx = 10, pady = 10, row = 1, column = 0, sticky = 'e')
        
        # Text label
        text_label = tk.Label(note_frame, bg='light gray', text='Note Text:')
        text_label.grid(padx = 10, pady = 10, row = 2, column = 0, sticky = 'e')
        
        # Link label
        link_label = tk.Label(note_frame, bg = 'light gray', text = 'Note Link:')
        link_label.grid(padx = 10, pady = 10, row = 3, column = 0, sticky = 'e')
        
        # Tag label
        tag_label = tk.Label(note_frame, bg = 'light gray', text = 'Note Tags:')
        tag_label.grid(padx = 10, pady = 10, row = 4, column = 0, sticky = 'e')

        # Create new title entry field
        noteform_title = tk.Entry(note_frame, width = 80)
        noteform_title.grid(padx = 10, pady = 10, row = 1, column = 1, sticky = 'w')
        noteform_title.insert(0, form_title) # Adds default title
    
        # Create new text field
        noteform_text = tk.Text(note_frame, height = 10, width = 60)
        noteform_text.grid(padx = 10, pady = 10, row = 2, column = 1)
        noteform_text.insert('1.0', form_text) # Adds default text
    
        # Create new link text field
        noteform_link = tk.Entry(note_frame, width = 80)
        noteform_link.grid(padx = 10, pady = 10, row = 3, column = 1)
        noteform_link.insert(0, form_link)
    
        # Create new tags text field
        noteform_tags = tk.Entry(note_frame, width = 80)
        noteform_tags.grid(padx = 10, pady = 10, row = 4, column = 1) 
        noteform_tags.insert(0, form_tags)
        
        # Meta data
        date_and_time = datetime.datetime.now()
        timezone = date_and_time.astimezone().tzinfo
        meta = f'created {date_and_time} {timezone}'
        
        # tracking edit history: save multiple iterations of note in one with time stamps??
        
        # Create 'save changes' button
        b1 = tk.Button(note_frame, text = 'save changes', command = partial(self.submit_changes, filepath, noteform_title, noteform_text, noteform_link, noteform_tags, meta))
        b1.grid(padx = 10, pady = 10, row = 6, column = 1, sticky = 'w')
        
        file.close()
    
def submit_changes(self, note_file, title, text, link, tags, creation):
        # Ensure proper indentation of the method code
        new_title = title.get()
        new_text = text.get('1.0', 'end').strip("\n")
        new_link = link.get()
        new_tags = tags.get()
        
        # Create a dictionary to store the new note information
        edit_dict = {
            "timestamp": creation,
            "title": new_title,
            "text": new_text,
            "link": new_link,
            "tags": new_tags
        }
        
        # Open the note file and read the contents
        with open(note_file, "r") as note:
            note_contents = note.readlines()
        
        # Find the existing note in the notebook list
        for existing_note in self.notebook:
            if existing_note.note_title == new_title:
                # Append the edit dictionary to the edit history list of the existing note
                existing_note.edit_history.append(edit_dict)
                
                # Overwrite the note file with the new changes
                with open(note_file, "w") as note:
                    for edit in existing_note.edit_history:
                        note.write(f'{edit["timestamp"]}\n{edit["title"]}\n{edit["text"]}\n{edit["link"]}\n{edit["tags"]}\n\n')
                break
        
class NoteForm(tk.Toplevel):
    
    def __init__(self, master, notebook): # initialize the new object
        super().__init__(master) # Initialize it as a Toplevel window
        
        self.geometry("600x350")
        self.title("Note Form")
        self.notebook = notebook
        self.frame = tk.Frame(self) # Frame that covers the entire window
        self.frame.pack(fill = tk.BOTH, expand = True)
        self.frame.config(bg = "light gray")
        self.form_title = tk.Entry(self.frame, width = 80)
        self.text = tk.Text(self.frame, height = 10, width = 60)
        self.link = tk.Entry(self.frame, width = 80)
        self.tags = tk.Entry(self.frame, width = 80)
        self.date_and_time = datetime.datetime.now()
        self.timezone = self.date_and_time.astimezone().tzinfo
        self.meta = f'created {self.date_and_time} {self.timezone}'
        
        # Title label
        title_label = tk.Label(self.frame, bg = 'light gray', text = 'Note Title:')
        title_label.grid(padx = 10, pady = 10, row = 1, column = 0, sticky = 'e')
        
        # Text label
        text_label = tk.Label(self.frame, bg='light gray', text='Note Text:')
        text_label.grid(padx = 10, pady = 10, row = 2, column = 0, sticky = 'e')
        
        # Link label
        link_label = tk.Label(self.frame, bg = 'light gray', text = 'Note Link:')
        link_label.grid(padx = 10, pady = 10, row = 3, column = 0, sticky = 'e')
        
        # Tag label
        tag_label = tk.Label(self.frame, bg = 'light gray', text = 'Note Tags:')
        tag_label.grid(padx = 10, pady = 10, row = 4, column = 0, sticky = 'e')

        # Create note title entry field
        noteform_title = self.form_title
        noteform_title.grid(padx = 10, pady = 10, row = 1, column = 1, sticky = 'w')
        noteform_title.insert(0, 'New note title') # Adds default title
    
        # Create note text field
        noteform_text = self.text
        noteform_text.grid(padx = 10, pady = 10, row = 2, column = 1)
        noteform_text.insert('1.0', "Enter text here") # Adds default text
    
        # Create link text field
        noteform_link = self.link
        noteform_link.grid(padx = 10, pady = 10, row = 3, column = 1)
        noteform_link.insert(0, "Enter a relevant link here")
    
        # Create tags text field
        noteform_tags = self.tags
        noteform_tags.grid(padx = 10, pady = 10, row = 4, column = 1) 
        noteform_tags.insert(0, "Enter any relevant tags, separated by #")

        # Create 'submit' button
        b1 = tk.Button(self.frame, text = 'submit', command = self.submit)
        b1.grid(padx = 10, pady = 10, row = 6, column = 1, sticky = 'w')
    
        
    def submit(self): # For storing created notes in 'notebook'
        
        # Creating note_dict, which contains user inputs for note attributes
        title = self.form_title.get()
        text = self.text.get('1.0', 'end').strip('\n')
        link = self.link.get()
        tags = self.tags.get()
        note_dict = {'title':title, 'text':text, 'link': link, 'tags': tags, 'meta': self.meta}
        
        # Create note object and append to 'notebook'
        new_note = MakeNote(note_dict)
        self.notebook.append(new_note)
        
        # Print note contents
        printable = f'{title}\n{text}\n{link}\n{tags}\n{self.meta}'
       
        return printable
        

class MakeNote():
    def __init__(self, note_dict):
        
        self.note_title = note_dict.get("title")
        self.note_text = note_dict.get("text")
        self.note_link = note_dict.get("link")
        self.note_tags = note_dict.get("tags")
        self.note_meta = note_dict.get("meta")
        # Initialize the edit history as an empty list
        self.edit_history = []
        
        # Add the initial creation to the edit history
        self.edit_history.append({
            "timestamp": self.note_meta,
            "title": self.note_title,
            "text": self.note_text,
            "link": self.note_link,
            "tags": self.note_tags
        })


# Main execution
if __name__ == '__main__':
    
    # Create a notebook
    main_window = MainWindow() 
    
    # Label for placing buttons
    button_label = tk.Label(main_window)
    button_label.grid(padx = 10, pady = 10, row = 0, column = 0)
    
    # New note button
    new_note = tk.Button(button_label, text = 'new', command = main_window.new_note)
    new_note.grid(padx=10, pady=10, row=5, column=0)
    
    # Open note button
    open_note = tk.Button(button_label, text='open', command = main_window.open_note)
    open_note.grid(padx=10, pady=10, row=6, column=0)
    
    # Save note button
    save_note = tk.Button(button_label, text='save', command = main_window.save_note)
    save_note.grid(padx=10, pady=10, row=7, column=0) 
    
    # Edit note button
    edit_note = tk.Button(button_label, text='edit note', command = main_window.edit_note)
    edit_note.grid(padx=10, pady=10, row=8, column=0)
    
    main_window.mainloop()
