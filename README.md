# PyPDFOCR - Tesseract-OCR based PDF filing
This program will help manage your scanned PDFs by doing the following:

foo

- Take a scanned PDF file and run OCR on it (using the Tesseract OCR software from Google), generating a searchable PDF
- Optionally, watch a folder for incoming scanned PDFs and automatically run OCR on them
- Optionally, file the scanned PDFs into directories based on simple keyword matching that you specify
- Evernote auto-upload and filing based on keyword search
- Email status when it files your PDF

More links:

- [Blog @ virantha.com](http://virantha.com/category/projects/pypdfocr)
- [Documentation @ documentup.com](http://documentup.com/virantha/pypdfocr)
- [Source @ github](https://www.github.com/virantha/pypdfocr)
- [API docs @ gitpages](http://virantha.github.com/pypdfocr/html)

 
## Usage: 
### Single conversion:
    pypdfocr filename.pdf

    --> filename_ocr.pdf will be generated

### Folder monitoring:
    pypdfocr -w watch_directory

    --> Every time a pdf file is added to `watch_directory` it will be OCR'ed
    
### Automatic filing:
To automatically move the OCR'ed pdf to a directory based on a keyword, use the -f option
and specify a configuration file (described below):

    pypdfocr filename.pdf -f -c config.yaml

You can also do this in folder monitoring mode:

    pypdfocr -w watch_directory -f -c config.yaml

#### Configuration file for automatic PDF filing
The config.yaml file above is a simple folder to keyword matching text file. It determines
where your OCR'ed PDFs (and optionally, the original scanned PDF) are placed after processing.
 An example is given below:

    target_folder: "docs/filed"
    default_folder: "docs/filed/manual_sort"
    original_move_folder: "docs/originals"
   
    folders:
        finances:
            - american express
            - chase card
            - internal revenue service
        travel:
            - boarding pass
            - airlines
            - expedia
            - orbitz
        receipts:
            - receipt

The `target_folder` is the root of your filing cabinet.  Any PDF moving will happen in
sub-directories under this directory. 

The `folders` section defines your filing directories and the keywords associated with them.
In this example, we have three filing directories (finances, travl, receipts), and some 
associated keywords for each filing directory.  For example, if your OCR'ed PDF
contains the phrase "american express" (in any upper/lower case), it will be filed into
`docs/filed/finances`

The `default_folder` is where the OCR'ed PDF is moved to if there is no keyword match.

The `original_move_folder` is optional (you can comment it out with `#` in
front of that line), but if specified, the original scanned PDF is moved into
this directory after OCR is done. Otherwise, if this field is not present or
commented out, your original PDF will stay where it was found.

If there is any naming conflict during filing, the program will add an underscore followed by a
number to each filename, in order to avoid overwriting files that may already be present.

### Evernote upload:
#### Evernote authentication token
To enable Evernote support, you will need to [get a developer token for your 
Evernote account.](https://www.evernote.com/api/DeveloperToken.action).  You
should note that this script will never delete or modify existing notes in your
account, and limits itself to creating new Notebooks and Notes.
Once you get that token, you copy and paste it into your configuration file
as shown below

#### Evernote filing usage
To automatically upload the OCR'ed pdf to a folder based on a keyword, use the
``-e`` option instead of the ``-f`` auto filing option.

    pypdfocr filename.pdf -e -c config.yaml

Similarly, you can also do this in folder monitoring mode:

    pypdfocr -w watch_directory -e -c config.yaml

#### Evernote filing configuration file
The config file shown above only needs to change slightly. The folders section
is completely unchanged, but note that ``target_folder`` is the name of your
"Notebook stack" in Evernote, and the ``default_folder`` should just be the
default Evernote upload notebook name.

    target_folder: "evernote_stack"
    default_folder: "default"
    original_move_folder: "docs/originals"
    evernote_developer_token: "YOUR_TOKEN"
   
    folders:
        finances:
            - american express
            - chase card
            - internal revenue service
        travel:
            - boarding pass
            - airlines
            - expedia
            - orbitz
        receipts:
            - receipt

### Auto email
You can have PyPDFOCR email you everytime it converts a file and files it.  You
need to first specify the following lines in the configuration file and then use the
``-m`` option when invoking ``pypdfocr``:

    mail_smtp_server: "smtp.gmail.com:587"
    mail_smtp_login: "virantha@gmail.com"
    mail_smtp_password: "PASSWORD"
    mail_from_addr: "virantha@gmail.com"
    mail_to_list: 
        - "virantha@gmail.com"
        - "person2@gmail.com"

## Fine-tuning Tesseract/Ghostscript
At the moment, the only options allowed for Tesseract and Ghostscript are 
specifying their executable locations manually.  Use the following in your configuration
file:

    tesseract:
        binary: "/usr/bin/tesseract"

    ghostscript:
        binary: "/usr/local/bin/gs"
    
## Disclaimer
While test coverage is at 90% right now, Sphinx docs generation is at an early
stage.  The software is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR
CONDITIONS OF ANY KIND, either express or implied.

## Installation
### Using pip
PyPDFOCR is available in PyPI, so you can just run:

    pip install pypdfocr

You will also need to install the external dependencies listed below.
For those on **Windows**, because it's such a pain to get all the PIL and PDF
dependencies installed, I've gone ahead and made an executable called 
[pypdfocr.exe](https://github.com/virantha/pypdfocr/blob/master/dist/pypdfocr.exe?raw=true)

You still need to install Tesseract and GhostScript as detailed below in the dependencies
list.

### Manual install
Clone the source directly from github (you need to have git installed):

    git clone https://github.com/virantha/pypdfocr.git

Then, install the following third-party python libraries:

- PIL (Python Imaging Library) http://www.pythonware.com/products/pil/
- ReportLab (PDF generation library) http://www.reportlab.com/software/opensource/
- Watchdog (Cross-platform fhlesystem events monitoring) https://pypi.python.org/pypi/watchdog
- PyPDF2 (Pure python pdf library)

These can all be installed via pip:

    pip install pil
    pip install reportlab
    pip install watchdog
    pip install pypdf2

You will also need to install the external dependencies listed below.

## External Dependencies
PyPDFOCR relies on the following (free) programs being installed and in the path:

- Tesseract OCR software https://code.google.com/p/tesseract-ocr/
- GhostScript http://www.ghostscript.com/

On Mac OS X, you can install these using homebrew:

    brew install tesseract
    brew install ghostscript

