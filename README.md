# NLP-Congenital-Heart-Defects

## Introduction
This is an NLP (Natural Language Processing) project for automatically identifying which type of congenital heart defect is being described in a text such as a medical case report. This is a strategy for document classification. Given a set of case reports, we would like to be able to determine which reports involve instances of known or newly diagnosed congenital heart defects. We may then determine if other aspects of case presentation vary based on correlation with specific defects. 

### Single text classification
In this scenario, a text is divided into two parts, title and body. For each component the system looks for key features (specifically, keywords and descriptions) and utilises this information collectively to come up with a final call. The title part holds greater significance than the rest, however, and as such it may override any other parts of the text. For instance, a text piece with the title "Case Report: Fallot's Tetralogy" already offers enough information for the system to output "Tetralogy of Fallot". Refer to **hd_type.py** for details.

### Mass text classification
Python's nltk offers a number of text classification algorithms. The idea is to train one of them (say, Naive Bayes Classifier) by feeding
it a feature set of specific format and test the algorithm on a large scale. Unfortunately, creating such feature set of sufficient size is very cumbersome and not so feasible, and therefore training one is not covered here. There is, however, a feature extracting function and instructions as to how to train a nltk classification model on your own with the right dataset. Refer to **mass_classification.py** for details.

## Getting Started
To get started, read the files in the following order: dictionary.py -> by_keywords.py -> description_patterns.py -> by_descriptions.py -> hd_type.py

### Requirements 

### Walkthrough
The data for every congenital heart defect type can be found here: https://www.nlm.nih.gov/databases/download/mesh.html 

The following are instructions as to how to read in a file in ASCII format and neatly organise the data there using the Python programming language.

1. Access the link above. There, click "Current Production Year MeSH" under ASCII format. What is needed is the binary file named "d{current_year}.bin".
2. Open up a Python console, set the working directory to where the file is saved (alternatively specify full path when feeding the file name) and type in the following:
    
   with open('d2020.bin', mode='rb') as file:
       content = file.read()
   decoded = content.decode('utf-8')

3. To confirm successful reading type in:

   decoded[:1500]
   
   and you should see something like the following:
   
   '*NEWRECORD\nRECTYPE = D\nMH = Calcimycin\nAQ = AA AD AE AG AI AN BI BL CF CH CL CS EC HI IM IP ME PD PK PO RE SD ST TO TU UR\nENTRY = A-23187|T109|T195|LAB|NRW|NLM (1991)|900308|
    abbcdef\nENTRY = A23187|T109|T195|LAB|NRW|UNK (19XX)|741111|abbcdef\nENTRY = Antibiotic A23187|T109|T195|NON|NRW|NLM (1991)|900308|abbcdef\nENTRY = A 23187\nENTRY = A23187, Antib
    iotic\nMN = D03.633.100.221.173\nPA = Anti-Bacterial Agents\nPA = Calcium Ionophores\nMH_TH = FDA SRS (2014)\nMH_TH = NLM (1975)\nST = T109\nST = T195\nN1 = 4-Benzoxazolecarboxyl
    ic acid, 5-(methylamino)-2-((3,9,11-trimethyl-8-(1-methyl-2-oxo-2-(1H-pyrrol-2-yl)ethyl)-1,7-dioxaspiro(5.5)undec-2-yl)methyl)-, (6S-(6alpha(2S*,3S*),8beta(R*),9beta,11alpha))-\n
    RN = 37H9VM9WZL\nRR = 52665-69-7 (Calcimycin)\nPI = Antibiotics (1973-1974)\nPI = Carboxylic Acids (1973-1974)\nMS = An ionophorous, polyether antibiotic from Streptomyces chartr
    eusensis. It binds and transports CALCIUM and other divalent cations across membranes and uncouples oxidative phosphorylation while inhibiting ATPase of rat liver mitochondria. T
    he substance is used mostly as a biochemical tool to study the role of divalent cations in various biological systems.\nOL = use CALCIMYCIN to search A 23187 1975-90\nPM = 91; wa
    s A 23187 1975-90 (see under ANTIBIOTICS 1975-83)\nHN = 91(75); was A 23187 1975-90 (see under ANTIBIOTICS 1975-83)\nMR = 20160527\nDA = 19741119\nDC = 1\nDX = 19840101\nUI = D00
    0001\n\n*NEWRECORD\nRECTYPE = D\nMH = Temefos\nAQ = AA AD AE AG AI AN BL CF CH CL CS EC HI IM IP ME PD PK RE S'
   
   DO NOT print the whole content as you will be bombarded with endless texts.
   
4. The file contains every medical term acknowledged by the National Library of Medicine, not just congenital heart defect terms. Each term has a unique ID that can be found online:
   https://meshb.nlm.nih.gov/record/ui?ui=D006330
   
   Through this I have garnered all the IDs associated with congenital heart defect terms. They are the following:
   
   ids = ['D004062',
    'D001017',
    'D019571',
    'D056889',
    'D003310',
    'D000080038',
    'D063748',
    'D054084',
    'D003420',
    'D007619',
    'D004374',
    'D004541',
    'D014339',
    'D004694',
    'D054092',
    'D008185',
    'D006345',
    'D059446',
    'D018636',
    'D056830',
    'D044542',
    'D007979',
    'D008382',
    'D009634',
    'D013771',
    'D000080041',
    'D004310',
    'D018785',
    'D014286',
    'D000073839',
    'D000073842',
    'D014424',
    'D000080039',
    'D016738',
    'D014927',
    'D054083',
    'D050030',
    'D029593',
    'D029597']

5. Here's a useful function to help out. We'll create a function "search_id" which will print only the information associated with a given unique ID.
   But first import Python's regular expression module.
   
   import re
   
   def search_id(uid):
     pattern = r'\*NEWRECORD(?:(?!\*NEWRECORD)[\s\S])*'+uid
     result = re.findall(pattern, decoded)
     return result
    
   Now call search_id("D013771") to see the following:
   
   ["*NEWRECORD\nRECTYPE = D\nMH = Tetralogy of Fallot\nAQ = BL CF CI CL CO DG DH DI DT EC EH EM EN EP ET GE HI IM ME MI MO NU PA PC PP PS PX RH RT SU TH UR VE VI\nPRINT ENTRY = Fallot's Tetralogy|
    T019|EPO|EQV|ORD (2010)|UNK (19XX)|740330|abcdeef\nENTRY = Tetralogy, Fallot's|T019|EPO|EQV|UNK (19XX)|840618|abcdef\nENTRY = Fallot Tetralogy\nENTRY = Fallots Tetralogy\nENTRY = Tetralogy, Fal
    lot\nENTRY = Tetralogy, Fallots\nMN = C14.240.400.849\nMN = C14.280.400.849\nMN = C16.131.240.400.849\nFX = Pentalogy of Cantrell\nMH_TH = NLM (1966)\nMH_TH = OMIM (2013)\nMH_TH = ORD (2010)\nS
    T = T019\nAN = do not confuse with TRILOGY OF FALLOT\nMS = A combination of congenital heart defects consisting of four key features including VENTRICULAR SEPTAL DEFECTS; PULMONARY STENOSIS; RI
    GHT VENTRICULAR HYPERTROPHY; and a dextro-positioned AORTA. In this condition, blood from both ventricles (oxygen-rich and oxygen-poor) is pumped into the body often causing CYANOSIS.\nCATSH = 
    CAT LIST\nMR = 20120703\nDA = 19990101\nDC = 1\nDX = 19660101\nUI = D013771"]
 
6. Great. But fishing all the entry terms (basically A.K.A's) out of them seem very cumbersome. So define another function for that:

   def search_id_k(uid):
     head = re.findall(r'(?<=\nMH = )[^\n]+(?=\n)', " ".join(search_id(uid)))
     p_entry = re.findall(r'(?<=\nPRINT ENTRY = )[^\n]+?(?=\||\n)', " ".join(search_id(uid)))
     entry = re.findall(r'(?<=\nENTRY = )[^\n]+?(?=\||\n)', " ".join(search_id(uid)))
     return head + p_entry + entry
     
   search_id_k("D013771") returns
   
   ['Tetralogy of Fallot',
    "Fallot's Tetralogy",
    "Tetralogy, Fallot's",
    'Fallot Tetralogy',
    'Fallots Tetralogy',
    'Tetralogy, Fallot',
    'Tetralogy, Fallots']
    
7. Lastly, create a dictionary object that maps each "head term" with its entry terms.

   d = {}
   for each in ids:
     d[search_id_k(each)[0]] = search_id_k(each)
   
   This takes some time. 
   
   Alternatively, you can simply copy off from the file "dictionary.py" in the repository.

## Credits
Created by Jun Bae while working with Harry Caufield in the Ping Lab at UCLA, Summer 2020.




