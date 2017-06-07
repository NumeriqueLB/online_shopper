
#-----Statement of Authorship----------------------------------------#
#
#  This is an individual assessment item.  By submitting this
#  code I agree that it represents my own work.  I am aware of
#  the University rule that a student must not act in a manner
#  which constitutes academic dishonesty as stated and explained
#  in QUT's Manual of Policies and Procedures, Section C/5.3
#  "Academic Integrity" and Section E/2.1 "Student Code of Conduct".
#
#    Student no: N9143165
#    Student name: Nicholas Beaumont
#
#  NB: Files submitted without a completed copy of this statement
#  will not be marked.  Submitted files will be subjected to
#  software plagiarism analysis using the MoSS system
#  (http://theory.stanford.edu/~aiken/moss/).
#
#--------------------------------------------------------------------#


#-----Assignment Description-----------------------------------------#
#
#  Online Shopper
#
#  In this assignment you will combine your knowledge of HTMl/XML
#  mark-up languages with your skills in Python scripting, pattern
#  matching, and Graphical User Interface design to produce a useful
#  application for aggregating product data published by a variety of
#  online shops.  See the instruction sheet accompanying this file
#  for full details.
#
#--------------------------------------------------------------------#


#-----Imported Functions---------------------------------------------#
#
# Below are various import statements for helpful functions.  You
# should be able to complete this assignment using these
# functions only.  Note that not all of these functions are
# needed to successfully complete this assignment.

# The function for opening a web document given its URL.
# (You WILL need to use this function in your solution.)
from urllib import urlopen

# Import the standard Tkinter functions. (You WILL need to use
# these functions in your solution.)
from Tkinter import *

# Functions for finding all occurrences of a pattern
# defined via a regular expression.  (You do NOT need to
# use these functions in your solution, although you will find
# it difficult to produce a robust solution without using
# regular expressions.)
from re import findall, finditer

# Import the standard SQLite functions just in case they're
# needed.
from sqlite3 import *

#
#--------------------------------------------------------------------#


#-----Student's Solution---------------------------------------------#
#
# Put your solution at the end of this file.
#

# Name of the invoice file. To simplify marking, your program should
# produce its results using this file name.
file_name = open('invoice.html', 'w')

# Here I am creating my GUI and giving it a name.
shop_window = Tk()
shop_window.title('Professional Online Store')

# This section of code is opening all of the URL's that I used and reading through
# their contents.
url_dresses = ['http://www.joomlajingle.com/rss/catalog/new/store_id/1/']
dresses_page_contents = urlopen(url_dresses[0]).read()
url_welder = ['https://www.machineryhouse.com.au/ACDC-TIG-ARC-Welders']
welder_page_contents = urlopen(url_welder[0]).read()
url_brushes = ['https://www.beardandblade.com.au/collections/shaving-brushes']
brushes_page_contents = urlopen(url_brushes[0]).read()

# This part of the program is writing up that part of the invoice that will not change
# and therefore can be hardwired. It also contains some styling tags.
file_name.write('''<!DOCTYPE html>
<html>
  <head>
  <style>
  table, th, tr, td {
  border: 1px solid black;
  }
  </style>
      <title>Professional Online Shop</title>
  </head>
  <body>
  <h1>Welcome to the Professional Online Shop</h1>
  <img src="https://upload.wikimedia.org/wikipedia/commons/c/c9/Online-shop_button.jpg">
  <table style= 'width 100%'>
  <th colspan= '3'><h2>Your Invoice</h2></th>
''')

# In this section of my code I am assigning my regular expressions to variables so
# they can be called later in my get_quantities function.
closing_tags = '</table>'

images_welder = findall(
    r'\//images\.machineryhouse.com.au\/products\/[A-Z]*[0-9]*[A-Z]*\/[0-9]*\/Main.jpg', welder_page_contents)

prices_welder = findall(
    r'Inc">\$([0-9]*,*[0-9]*)', welder_page_contents)

# Yes I know that this regular expression is disgusting and I apologise for it.
# But it works and I ran out of time to fix it.
description_welder = findall(
    r'<div class="Desc"><a href="[A-Z]*[0-9]*">([A-Z]*\/*[A-Z]*\ *[0-9]*\ *[A-z]*\ *[A-z]*\ *[0-9]*[A-Z]*\/*[A-Z]*\ *[0-9]*)<br />', welder_page_contents)

description_dresses = findall(
    r'<title><!.[A-Z]+.([A-z ]+)]]></title>', dresses_page_contents)

prices_dresses = findall(
    r'\$([0-9]+\.[0-9]+)', dresses_page_contents)

images_dresses = findall(
    r'//www.joomlajingle.com/media.*\.jpg', dresses_page_contents)

description_brushes = findall(r'"title">(.*)</a>', brushes_page_contents)

prices_brushes = findall(r'"money">\$(.*)</span>', brushes_page_contents)

images_brushes = findall(r'<img src="(.*)"', brushes_page_contents)

# Some of the regular expression lists produced unwanted results so I had to delete items out
# of the lists to get the desired results.
del images_welder[:: 2]
del prices_welder[:: 2]
del prices_dresses[:: 2]
del description_dresses[0]
del images_brushes[0:2]

# Here I am setting some counters that will later be used in my
# get_quantites and update_database functions.
# They are set to zero because list indices start from zero.
welders_counter = 0
dresses_counter = 0
brushes_counter = 0

# The prices for dresses were originally in USD and in string type so
# I am converting them to AUD and integers.
prices_dresses = map(float, prices_dresses)
new_prices_dresses = [price * 1.33 for price in prices_dresses]
new_prices_dresses = map(int, new_prices_dresses)

# Here I am converting the prices for welders and brushes into integers so
# they can later be added into a total cost.
# For prices_welder I had to remove the commas from the list before it
# could be converted.
prices_welder = ([comma.replace(',', '') for comma in prices_welder])
prices_welder = map(float, prices_welder)
prices_welder = map(int, prices_welder)
prices_brushes = map(float, prices_brushes)
prices_brushes = map(int, prices_brushes)

# This is the 'main' part of the program that marks up the invoice with HTML tags.
def get_quantities():
    global box_quantities
    box_quantities = [int(welders_box.get()), int(
        dresses_box.get()), int(brushes_box.get())]

# This if statement was created to deal with instances where an invoice
# is submitted with 0 quantities.
    if (box_quantities[0] < 1) and (box_quantities[1] < 1) and (box_quantities[2] < 1):
        file_name.write(
            '<tr><td>Error: your quantities are insufficient</td></tr>')
        submit_button['state'] = DISABLED
        save_button['state'] = ACTIVE
        quit_button['state'] = ACTIVE
        shop_window.update()
    else:
        def html_writer(quantity,counter,description,price,image):
            for num in range(quantity):
                counter
                file_name.write('<tr><td> Item description: ' +
                                str(description[counter]) + '</td>\n')
                file_name.write('<td> Price (AUD): $' +
                                str(price[counter]) + '.00</td>\n')
                file_name.write('<td><img src="http:' +
                                str(image[counter]) + '"></td>\n</tr>\n')
                counter = counter + 1
                submit_button['state'] = DISABLED
                save_button['state'] = ACTIVE
                quit_button['state'] = ACTIVE
                shop_window.update()
                            
        html_writer(box_quantities[0],welders_counter,description_welder, prices_welder, images_welder)
        progress_label['text'] = 'Processing Welders...'
        shop_window.update()
        html_writer(box_quantities[1],dresses_counter,description_dresses,new_prices_dresses,images_dresses)
        progress_label['text'] = 'Processing Dresses...'
        shop_window.update()
        html_writer(box_quantities[2],brushes_counter,description_brushes,prices_brushes, images_brushes)
        progress_label['text'] = 'Processing Shaving Brushes...'
        shop_window.update()

# Creating the cost variables for each product category.
    if (box_quantities[0] < 1):
        total_price_welders = 0
    else:
        total_price_welders = sum(prices_welder[0:(box_quantities[0])])

    if box_quantities[1] == 0:
        total_price_dresses = 0
    else:
        total_price_dresses = sum(new_prices_dresses[0:(box_quantities[1])])

    if box_quantities[2] == 0:
        total_price_brushes = 0
    else:
        total_price_brushes = sum(prices_brushes[0:(box_quantities[2])])

# Writing the total cost of the purchases to the invoice.
    total_cost = total_price_welders + total_price_dresses + total_price_brushes
    progress_label['text'] = 'Done!'

# Adding the the closing HTML tags, and adding the total cost to the invoice.
    file_name.write(closing_tags)
    file_name.write('<h2><b>The total cost of your purchase is: $' +
                    str(total_cost) + ' (AUD)</b></h2>')

# Finally, adding the links to my suppliers.
    file_name.write('''<p>Professional Online Shop is proudly supported by:</p>\n
    <ul>\n<li><a href="https://www.machineryhouse.com.au/ACDC-TIG-ARC-Welders">https://www.machineryhouse.com.au/ACDC-TIG-ARC-Welders</a></li>\n
    <li><a href="http://www.joomlajingle.com/rss/catalog/new/store_id/1/">http://www.joomlajingle.com/rss/catalog/new/store_id/1/</a></li>\n
    <li><a href="https://www.beardandblade.com.au/collections/shaving-brushes">https://www.beardandblade.com.au/collections/shaving-brushes</a></li>\n</ul>''')

# The database_update function creates a new database every time the function is called.
# This erases the old data so only the last saved purchase is recorded.
# The first if statement deals with scenarios in which the user enters no quantity
# it will ensure the progress label is still updated.
def database_update():
    if (box_quantities[0] < 1) and (box_quantities[1] < 1) and (box_quantities[2] < 1):
        database_label['text'] = 'Done!'

    connection = connect('shopping_trolley(1).db')
    view = connection.cursor()

    view.execute('DROP TABLE IF EXISTS purchases;')
    view.execute(
        "CREATE TABLE purchases (description TEXT, price INT)")
    
    welder_db = description_welder[0:(box_quantities[0])]
    dresses_db = description_dresses[0:(box_quantities[1])]
    brushes_db = description_brushes[0:(box_quantities[2])]
    all_items = welder_db + dresses_db + brushes_db
    all_prices = (prices_welder[0:(box_quantities[0])] + new_prices_dresses[0:(
            box_quantities[1])] + prices_brushes[0:(box_quantities[2])])
 
    def data_updater(items_db,prices_db):
        for items,prices in zip(items_db, prices_db):
            view.execute(
                "INSERT INTO purchases(description, price) VALUES (?,?)", (items, prices))
            database_label['text'] = 'Saving your order...'
            shop_window.update()
            save_button['state']=DISABLED
            
    data_updater(all_items, all_prices)
    database_label['text'] = 'Done!'

# Closing down the database so that new data can be written later.
    connection.commit()
    view.close()
    connection.close()

# Defining a function that will exit the GUI and allow the get_quantities data
# to be written to invoice.html.
def quit_window():
    file_name.close()
    shop_window.quit()


# This section of the program is really just configuring the GUI and assigning the
# various functions to their buttons.
window_label = Label(shop_window,
                     text='Step 1. Select Your Quantities:')
window_label.grid(row=0, column=1)

welders_label = Label(shop_window,text='Welders:')
welders_label.grid(row=1, column=1)

welders_box = Spinbox(shop_window, from_=0, to=10)
welders_box.grid(row=2, column=1)

dresses_label = Label(shop_window, text='Dresses:')
dresses_label.grid(row=3, column=1)

dresses_box = Spinbox(shop_window, from_=0, to=10)
dresses_box.grid(row=4, column=1)

brushes_label = Label(shop_window, text='Shaving Brushes:')
brushes_label.grid(row=5, column=1)

brushes_box = Spinbox(shop_window, from_=0, to=10)
brushes_box.grid(row=6, column=1)

step_two = Label(shop_window,
                 text='Step 2. Submit Your Invoice')
step_two.grid(row=7, column=1)

submit_button = Button(shop_window, state=ACTIVE, text='Submit Invoice',
                       command=get_quantities)
submit_button.grid(row=8, column=1)

progress_label = Label(shop_window, text='')
progress_label.grid(row=9, column=1)

step_three = Label(shop_window,
                   text='Step 3. Save Your Invoice (Optional)')
step_three.grid(row=10, column=1)

save_button = Button(shop_window,
                     state=DISABLED, text='Save Invoice', command=database_update)
save_button.grid(row=11, column=1)

database_label = Label(shop_window, text='')
database_label.grid(row=12, column=1)

step_four = Label(shop_window,
                  text='Step 4. Press Quit to Exit')
step_four.grid(row=13, column=1)

quit_button = Button(shop_window,
                     state=DISABLED, text='Quit', command=quit_window)
quit_button.grid(row=14, column=1)

shop_window.mainloop()
