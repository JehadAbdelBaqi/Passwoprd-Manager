# Password-Manager
A password manager which creates a JSON file and stores your password which can be generated and the website and email address used for it

from tkinter import *
from tkinter import messagebox
from random import choice, randint, shuffle
import pyperclip
import json
# ---------------------------- PASSWORD GENERATOR ------------------------------- #


def generate_password():
    letters = ['a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'i', 'j', 'k', 'l', 'm', 'n', 'o', 'p', 'q', 'r', 's', 't', 'u',
               'v', 'w', 'x', 'y', 'z', 'A', 'B', 'C', 'D', 'E', 'F', 'G', 'H', 'I', 'J', 'K', 'L', 'M', 'N', 'O', 'P',
               'Q', 'R', 'S', 'T', 'U', 'V', 'W', 'X', 'Y', 'Z']
    numbers = ['0', '1', '2', '3', '4', '5', '6', '7', '8', '9']
    symbols = ['!', '#', '$', '%', '&', '(', ')', '*', '+']

    password_letters = [choice(letters) for _ in range(randint(8, 10))]
    password_symbols = [choice(symbols) for _ in range(randint(2, 4))]
    password_numbers = [choice(numbers) for _ in range(randint(2, 4))]

    password_list = password_letters + password_numbers + password_symbols

    shuffle(password_list)

    password = "".join(password_list)

    password_input.insert(0, f"{password}")
    pyperclip.copy(password)
# ---------------------------- SAVE PASSWORD ------------------------------- #


def store_data():
    website_get = website_input.get()
    password_get = password_input.get()
    email_get = email_input.get()
    new_data = {
        website_get: {
            "email": email_get,
            "password": password_get
        }
    }

    if len(website_get) == 0 or len(password_get) == 0:
        messagebox.showinfo(title="Oops", message="Please don't leave any fields empty")
    else:
        is_ok = messagebox.askokcancel(title=website_get, message=f"These are the details entered:\nEmail: {email_get}"
                                       f"\nPassword: {password_get}\nIs it ok to save?")
        if is_ok:
            try:
                with open("data.json", "r") as data_file:
                    data = json.load(data_file)
            except FileNotFoundError:
                with open("data.json", "w") as data_file:
                    json.dump(new_data, data_file, indent=4)
            else:
                data.update(new_data)
                with open("data.json", "w") as data_file:
                    json.dump(data, data_file, indent=4)
            finally:
                website_input.delete(0, "end")
                password_input.delete(0, "end")

# ---------------------------- PASSWORD SEARCH ---------------------------------- #


def find_password():
    detail_search = website_input.get()
    try:
        with open("data.json") as key_file:
            value = json.load(key_file)
    except FileNotFoundError:
        messagebox.showinfo(title="Error", message="File does not exist.")
    else:
        if detail_search in value:
            website_value = value[f"{detail_search}"]
            email_value = website_value["email"]
            password_value = website_value["password"]
            messagebox.showinfo(title=f"{detail_search}", message=f"Email: {email_value}\nPassword: {password_value}")
        else:
            messagebox.showinfo(title="Error", message=f"No details for {detail_search} exist")


# ---------------------------- UI SETUP ------------------------------- #
window = Tk()
window.title("Password manager")
window.config(padx=50, pady=50)

canvas = Canvas(width=200, height=200, highlightthickness=0)
locker_img = PhotoImage(file="logo.png")
canvas.create_image(100, 100, image=locker_img)
canvas.grid(column=1, row=0)

website_label = Label(text="Website:")
website_label.grid(column=0, row=1)
email_username_label = Label(text="Email/Username:")
email_username_label.grid(column=0, row=2)
password_label = Label(text="Password:")
password_label.grid(column=0, row=3)

website_input = Entry()
website_input.grid(column=1, row=1)
website_input.focus()
email_input = Entry(width=35)
email_input.grid(column=1, row=2, columnspan=2)
email_input.insert(0, "jehad@mail.org")
password_input = Entry(width=21)
password_input.grid(column=1, row=3)

generate_password_btn = Button(text="Generate Password", command=generate_password)
generate_password_btn.grid(column=2, row=3)
add_button = Button(text="Add", width=36, command=store_data)
add_button.grid(column=1, row=4, columnspan=2)
search_button = Button(text="Search", width=13, command=find_password)
search_button.grid(column=2, row=1)

window.mainloop()
