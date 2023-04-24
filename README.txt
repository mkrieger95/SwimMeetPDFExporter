Hi there -

This is a work-in-progress on a project I have been wanting to do for some time.

For those who are familiar in swimming, for years results have been printed out or exported into a .PDF. Depending on your software you can also export specific files that can be read by Team Management software to either print out or export the results.

The issue that has come up is that there are many great meet results out there, but the only file that is still available to access is the printout/.PDF file. These are for sites that don't specialize in meet results keeping (SwimCloud, etc.) Clubs/Schools/Teams may not have submitted the specific export files that many sites such as SwimCloud.com can currently read. Or, you have sites that have upgraded/migrated to newer versions since 2000 or prior and the software-specific files were lost as part of the migration.

In short, this project is aiming to read meet result exports (primarily from Hy-Tek for time being) that have been exported to .PDF format and then export the data cleanly to a .CSV file.

The hope is to have a consistent export of these results to where this can be utilized by sites like SwimCloud, to archive these meets that only have a .PDF file available.

The initial thought is to tackle this using Python, Pandas, PDFPlumber, and RE. I THINK this would be the minimal needed to read the PDF, process it, export it to a standardized .CSV

########################################################################

The end goal here is to be able to take these old .PDFs which may be the only remaining record of meet results for various meets, and throw them into a site/system which can continue to preserve these times in a more user-friendly method. There have been a few meets that I personally have wanted to "restore" from a .PDF so I can have it properly archived for years to come.

 
import PyPDF2
import tkinter as tk
from tkinter import filedialog
import csv
import openpyxl

# create a Tkinter root window
root = tk.Tk()
root.withdraw()

# open a file dialog to select the PDF file to parse
pdf_path = filedialog.askopenfilename(title="Select PDF", filetypes=[("PDF Files", "*.pdf")])

# open the PDF file in binary mode
with open(pdf_path, 'rb') as pdf_file:
    # create a PDF reader object
    pdf_reader = PyPDF2.PdfFileReader(pdf_file)

    # get the first page of the PDF
    first_page = pdf_reader.getPage(0)

    # extract the text from the page
    page_text = first_page.extractText()

    # split the text into rows
    rows = page_text.split('\n')

    # specify the starting row index
    start_row = 5

    # create a list to store the data
    data = []

    # loop over the rows and process the data
    for i in range(start_row, len(rows)):
        # get the current row
        row = rows[i]

        # split the row into columns
        columns = row.split()

        # get the first and last name of the swimmer
        first_name = columns[1]
        last_name = columns[0]

        # get the age of the swimmer
        age = columns[2]

        # get the event, heat, lane, and time
        event = columns[3]
        heat = columns[4]
        lane = columns[5]
        time = columns[6]

        # add the data to the list
        data.append([first_name, last_name, age, event, heat, lane, time])

# create a file dialog to select the output file
out_path = filedialog.asksaveasfilename(title="Save CSV", filetypes=[("Excel Files", "*.xlsx")])

# create an Excel workbook object
workbook = openpyxl.Workbook()

# select the active worksheet
worksheet = workbook.active

# write the data to the worksheet
for row in data:
    worksheet.append(row)

# save the workbook to the output file
workbook.save(out_path)
