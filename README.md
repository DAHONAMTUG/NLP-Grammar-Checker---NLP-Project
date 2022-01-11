# NLP-Grammar-Checker---NLP-Project
# -NLP-Grammar-Checker---NLP-Project-
 NLP Grammar Checker - using the python language tool library . 
This project performs a grammar test  using the Python language tool library!
The project includes a friendly interactive user interface!


import fnmatch ,import PyPDF4
import language_tool_python
import tkinter as TK , from tkinter import ttk as TTK
from tkinter.filedialog import askopenfilename , from tkinter import messagebox, Button
import sys , import os , import logging

with language_tool_python.LanguageToolPublicAPI('en-US') as tool:


    class grammer_check:
        """
        This class recives a text file object and  uses sevral methods to
        perform a us-english grammer check using the python language tool libray!
        :param:file --> the text file the user want to do a grammer check on.
        """
        def __init__(self, file) -> object:
            self.file=file


        def open_file(self):
            """
            the open_file  method use the fnmatch library to Test whether the filename string
            matches a pattern string, and opens the file to read.
            returning it text.
            :return: str -> text
            """
            self.file
            self.text = " "
            for file in os.listdir('.'):
                #checks the files in project dirctory
                if fnmatch.fnmatch(file, '*.txt'):
                    with open(file, "r") as file_opened:
                        self.text = file_opened.read()

                elif fnmatch.fnmatch(file, '*.pdf'):
                    pdf_file = open(file, 'rb')
                    pdfReader = PyPDF4.PdfFileReader(pdf_file)
                    file = pdfReader.getPage(0)
                    self.text = (file.extractText())
                    return self.text

        def scan_text(self):
            """
              the scan_text  method use the language library check and match method to find a grammer mistakes
              in the text  and return them each mistake match consist of the grammer rule he broke,
              the number of characters of the issue and its start position in the text.
              a massage of the found mistake and suggestion of a way to correct the mistake.
              i used a for statement  inside if loop to print out the mistakes in a readable format.
              :print: matches -> list
            """
            self.matches = tool.check(self.text)
            if len(self.matches) > 0:
                print(f'There are {len(self.matches)} grammatical errors in the text!!\n')
                for i, match in enumerate(self.matches, 1):
                    print(i, ")", " ", match, sep="")
                    print("~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~")



        def find_mistakes(self):
            """
               the find_mistakes use the rules from the match method.
               i used a for statement   and inside if loop, to check if there are mistakes in the text (at least one replacement)
               with that i initialized 4 new lists:
               start_positions,end_positions  = append the start and the end of the grammer mistake repspectivly
               my_mistakes = append the part of text that is grammerly wrong, using the [errorLength + rules.offset]
               my_corrections =  append the part of text that the lib suggest as replacement
            """
            self.my_mistakes, self.my_corrections = [],[]
            self.start_positions, self.end_positions = [],[]
            for rules in self.matches:
                if len(rules.replacements) > 0:
                    self.start_positions.append(rules.offset)
                    self.end_positions.append(rules.errorLength + rules.offset)
                    self.my_mistakes.append(self.text[rules.offset:rules.errorLength + rules.offset])
                    self.my_corrections.append(rules.replacements[0])
                    self.my_new_text = list(self.text)
                    for m in range(len(self.start_positions)):
                        for i in range(len(self.text)):
                            self.my_new_text[self.start_positions[m]] = self.my_corrections[m]
                            if (i > self.start_positions[m] and i < self.end_positions[m]):
                                self.my_new_text[i] = ""


        def get_correct_text(self):
            if len(self.matches)==0:
                print("No grammatical errors were found in the text")
            else:
                my_new_text = "".join(self.my_new_text)
                print("The new and correct text:\n")
                print(my_new_text.replace('. ','\n'))
                print(my_new_text, file=open('output.txt', 'a'))


    def UpdateProgress(ProgressBar, Value):
            ProgressBar['value'] = Value
            root.update_idletasks()

    def ExitCallBack():
            msg = messagebox.showinfo("Exit", "This is a demo for a program that checks grammatical errors in text files!!")
            sys.exit(0)

    def create_log():
        logger = logging.getLogger()
        logger.setLevel(logging.INFO)
        formatter = logging.Formatter('%(asctime)s | %(levelname)s | %(message)s',
                                      '%m-%d-%Y %H:%M:%S')
        file_handler = logging.FileHandler('logs.log')
        file_handler.setLevel(logging.DEBUG)
        file_handler.setFormatter(formatter)
        logger.addHandler(file_handler)
        logging.basicConfig(filename="logfilename.log", level=logging.INFO)
        # Log Creation

        logging.info('Running grammer check...')
        logging.error('')
        logging.debug('Debug Check')

    if __name__ == '__main__':
        create_log()
        root = TK.Tk()
        root.iconbitmap("owl icon.ico")
        master = TK.Tk()
        root.geometry("300x100")
        root.title('Grammer scanner')
        progress = TTK.Progressbar(root, orient=TK.HORIZONTAL, length=300, mode='determinate')
        B = Button(root, text = "Exit", command = ExitCallBack)
        B.place(x = 150,y = 50)
        progress.pack()
        dirname = os.getcwd()
        dirname = TK.filedialog.askdirectory(initialdir=dirname, title='Please Select a Directory')
        if (dirname == ''):
            dirname = '/.'
        UpdateProgress(progress, 20)
        #root.title('Select ML data base file (.csv)')
        Names = askopenfilename(
            initialdir=dirname,
            filetypes=(("Text File", "*.txt"), ("pdf File", "*.pdf")),
            #title='Choose input file(s) or folder(s).'
        )

        if Names == '':
            sys.exit(-2)
        FileName = Names
        UpdateProgress(progress, 30)
        #root.title('please wait while building the classification model')
        UpdateProgress(progress, 40)
        text1 = grammer_check(os.listdir('.'))
        text1.open_file()
        root.config(cursor="wait")
        UpdateProgress(progress, 50)
        text1.scan_text()
        UpdateProgress(progress, 60)
        text1.find_mistakes()
        UpdateProgress(progress, 70)
        text1.get_correct_text()
        UpdateProgress(progress, 100)
        root.mainloop()
        root.withdraw()
        ExitCallBack()
       

