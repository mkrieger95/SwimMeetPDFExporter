Hi there -

This is a work-in-progress on a project I have been wanting to do for some time.

For those who are familiar in swimming, for years results have been printed out or exported into a .PDF. Depending on your software you can also export specific files that can be read by Team Management software to either print out or export the results.

The issue that has come up is that there are many great meet results out there, but the only file that is still available to access is the printout/.PDF file. These are for sites that don't specialize in meet results keeping (SwimCloud, etc.) Clubs/Schools/Teams may not have submitted the specific export files that many sites such as SwimCloud.com can currently read. Or, you have sites that have upgraded/migrated to newer versions since 2000 or prior and the software-specific files were lost as part of the migration.

In short, this project is aiming to read meet result exports (primarily from Hy-Tek for time being) that have been exported to .PDF format and then export the data cleanly to a .CSV file.

The hope is to have a consistent export of these results to where this can be utilized by sites like SwimCloud, to archive these meets that only have a .PDF file available.

The initial thought is to tackle this using Python, Pandas, PDFPlumber, and RE. I THINK this would be the minimal needed to read the PDF, process it, export it to a standardized .CSV

########################################################################

The end goal here is to be able to take these old .PDFs which may be the only remaining record of meet results for various meets, and throw them into a site/system which can continue to preserve these times in a more user-friendly method. There have been a few meets that I personally have wanted to "restore" from a .PDF so I can have it properly archived for years to come.

 
import tabula
import pandas as pd
import tkinter as tk
from tkinter import filedialog

# Allow user to select the PDF file
root = tk.Tk()
root.withdraw()
file_path = filedialog.askopenfilename(title="Select PDF file", filetypes=[("PDF files", "*.pdf")])

# Read the PDF file and extract the table
df = tabula.read_pdf(file_path, pages="all", lattice=True)[0]

# Clean up the data
df = df.dropna(how="all")
df = df[df["Event"] != "Event"]
df.columns = ["Event", "P/F", "Name", "Team", "Seed Time", "Finals Time", "Points"]

# Convert the Seed Time and Finals Time columns to datetime format
df["Seed Time"] = pd.to_datetime(df["Seed Time"], format="%M:%S.%f").dt.time
df["Finals Time"] = pd.to_datetime(df["Finals Time"], format="%M:%S.%f").dt.time

# Save the data to an XLSX file
df.to_excel("results.xlsx", index=False)
