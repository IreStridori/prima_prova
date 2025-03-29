### **1. Introduction**
The project focuses on the development of a software which is capable of reading and structuring a FASTA file and perform different analysis on mitochondrial DNA sequences from multiple species, while providing to the user an interacting, user-friendly, Web Interface. The project also relies on scalability, so that the possibility to expand it in the future is concrete.
 
### **Technologies used**
- Programming Language: Python (Pandas, Biopython and Flask libraries used)
- Backend: Flask
- Frontend: HTML

---

## **2. System design**
### **System architecture and interactions**
immagini UML e crc 

### **Web Interface structure**
	--| app.py
	--| uploads/
	--| templates/
		--| index.html
		--| upload.html
	 	--| stats.html
	  	--| motif.html
	   	--| allign.html

---

## 3. Implementation: classes used and their responsibilities
### 3.1 Class FileParser (Abstract Superclass)
- **Purpose**: Provides an reusable structure for parsing different types of files. The only method it has is `parse(self, file_path)`, an abstract method for reading and processing data from a file path. The superclass ensures a that all subclasses implement the parse method.  

### 3.2 Class FastaParser (Concrete Subclass of DataParser)  
- **Purpose**: Implements specific parsing for FASTA files and organizes the files's genomic data in a structured table with an identifier, a description and an associated sequence. It implements the `parse(self, file_path)` method from FileParser.
  n
- **Specific responsibilities**:
	- Reads a FASTA file line by line.
 	- Organizes the different file's objects into a dictionary with three keys:  
		- **ID**: The sequence identifier (e.g., _NC_012920.1_).  
		- **Description**: The description of the organism (e.g., _Homo sapiens mitochondrion, complete genome_).
		- **Sequence**: The nucleotide sequence itself (e.g., _GATCACAGGT..._).  
	- Converts the dictionary into a Pandas DataFrame for better organization and management.  

- **Error management**: The class uses an `ensure_data_loaded` decorator which verifies if a file was uploaded by the user. In case it was not, it raises an error, informs the user with a specific message and halts the session.

devi scrivere delle cose su questi poi:
@staticmethod

 @ensure_data_loaded #se i dati non sono stati caricati non verrà eseguito.
    def get_summary(self):
        """describe(include="all") restituisce un riepilogo del dataset.: numero di valori unici per ogni colonna, frequenza degli identificatori, lunghezza media delle sequenze"""
        return self._data.describe(include="all")

    @ensure_data_loaded #aggiunto
    def get_seq(self, index):
        """Restituisce la sequenza e i dettagli della riga indicata."""
        if index >= len(self._data) or index < 0:
            raise IndexError(f"Indice {index} fuori dai limiti).")
        seq = self._data.iloc[index].tolist()
        return seq
  
### 3.3 Class GenomicEntity (Concrete Superclass)
- **Purpose**: Models a generic genomic sequence by setting common attributes (identifier, description, sequence) and provides a common method for sequence length calculation.

### 3.4 Class MitochondrialDNA (Subclass of GenomicEntity) 
   - **Purpose**: Extends `GenomicEntity` to add specific functionalities for DNA sequences.  
   - **Specific responsibilities**:
     	- Extract a subsequence by putting a start and an end index as inputs
     	- GC content analysis of a sequence

### 3.5 Class MotifAnalyser (Abstract Class)
- **Purpose**: Provides an reusable structure for working with different types of data and inserting different types of inputs (e.g., I could give as motif a tuple, a list, a string...). Defines the structure to work with (`data`) and an abstract method `find_motif(self, motif)` for serching a motif in a certain sequence. The superclass ensures a that all subclasses implement the find_motif method.

### 3.6 Class SequenceMotif (Concrete Subclass of MotifAnalyser)  
   - **Purpose**: extends `MotifAnalyser` and is responsible for identifying recurrent genetic patterns that may be biologically significant. It allows the use to insert as input a precise or not subsequence and it extracts it. It also provides insights into their frequency and occurrence across multiple sequences.  
   - **Methods**: 2 different ways of analysing a motif were implemented.
     - `extract_motifs(self, sequence, motif_length, minimum)`: if the user does not have a specific motif to search this method extracts all possible subsequences of length `motif_length` from the given `sequence` which repeats at least `minimum` times. Uses `Counter()` from the `collections` module to count motif occurrences. Returns a Pandas DataFrame with motif counts.
       
     - `find_motif(self, motif)`: if the user has a specific genetic motif, this searches the subsequence across all sequences in the dataset. Returns a Pandas DataFrame listing:
		- **Motif** searched 
 		- Sequence **Identifier**   
		- **Number of occurrences** per sequence  

### 3.7 Sequence Alignment (Concrete Class)
- **Purpose**: using Biopython’s `PairwiseAligner` module, it manages to handle robust pairwise sequence alignment. It compares two sequences and calculates their similarity, producing a structured alignment output.  
- **Methods and responsibilities**:
	- Initializes the alignment object with **two input sequences (`seq1`, `seq2`)**.  
	- Uses **global alignment mode** by default.  
	- Executes **pairwise sequence alignment**.  
	- Stores the alignment results.  
	- `format_alignment(self, n=1)` formats and returns the **top `n` alignments** in a readable string format.  
	- Helps visualize how sequences match or differ.  
	- Returns the **alignment score**, a numerical measure of sequence similarity.
   
---
## 4 parte di flask
scrivi cosa fanno

## 5 HTML strucutre and functionalities
We focused on a functional simple design rather than an aesthetic one given the scientific/research purposes. The navigation structure is:
**Upload → Parse → Visualize → Analyze (through statistics, motif searches, or alignments)**

### home.html 
It is the starting page for the application. Welcomes the user, explains what the tool does and enables the loading of a FASTA file.
- **Upload**:
  - Contains a form with a file input that accepts only .fasta files
  - Uses `POST` method to send the file to the "/upload" endpoint
  - Includes `multipart/form-data` encoding necessary for file uploads
  - Checks if a file has been uploaded and displays a link to visualize the parsed data as a DataFrame

### dataframe.html
It displays the parsed FASTA data in a tabular format and allows different operations.
- **Navigation**: Provides links to other possible features:
  - Statistics visualization ("/stats")
  -  Motif searching ("/motif")
  -  Pairwise sequence alignment ("/align")
  -  Return to home page
- **Data presentation**:
  - Renders a table with three columns: ID, Description and Sequence
  - Iterate through each sequence entry
  - Each row represents one sequence from the FASTA file with its metadata

### stats.html
It provides statistical analysis of the FASTA sequences:
- **Table structure**: Displays detailed metrics for each sequence:
  - ID and Description (identifiers from the FASTA file)
  - Length (number of nucleotides in each sequence)
  - GC Content (percentage of guanine and cytosine bases, an important genomic characteristic)
- **Data rendering**: loops through the "stats" array and populate the table
- **Navigation**: Includes links to the other possible operations and a way to return to the DataFrame view

### motifs.html
Enables users to search for conserved sequence patterns (motifs) within the genomic data and provides links to other possible operations.
- **Search options**: Provides two different search forms (both use POST method to submit to the "/motif" endpoint)
  - First form: Allows users to search by sequence index
  - Second form: Allows users to search by sequence motif, if known
- **Results display**:
  - For sequence index searches: Shows a single result value (`{{search_motif}}`)
  - For motif pattern searches: Renders a list of sequences of the database in which this motif is present (`{% for r in find_motif %}`)

### align.html
Provides sequence alignment capabilities and the link to other possible operations
- **User input**: Allows selection of two sequences by their indices in the DataFrame for psirwise global alignment
- **Form**: Uses numeric inputs and submits to "/align" endpoint using `POST` method
- **Results display**: 
  - Displays alignment results in a preformatted text block (`<pre>{{ alignment_result }}</pre>`)
  - The preformatted tag ensures that spacing and formatting of the alignment is preserved
	
---
### 6. Procedure di installazione e configurazione e esempio pratico 
Run the Flask App
Start the application by running: python app.py
The app will be accessible at http://127.0.0.1:5000/.

---

## 7. Conclusions

### Current limitations and future possible improvements of the system:
- **File format**: The application only supports uploading files in FASTA format, limiting its usability for datasets stored in other common bioinformatics formats (e.g., GenBank or EMBL). This feature can be extended by using the superclass FileParser that we implemented.
- **Basic error handling**: For example, the system may not adequately handle malformed FASTA files or unexpected user inputs, which can lead to uninformative error messages or crashes.
- **Session-Based file storage**: The use of Flask sessions to store the file path is simple but not ideal for multi-user environments or larger datasets.
- **Limited analysis**: the software is focused on basic sequence statistics (length and GC content), motif search, and pairwise alignment. More advanced analyses, such as phylogenetic tree construction or structural predictions, are not supported. This can be improved by integrating additional bioinformatics tools and algorithms, such as for BLAST searches or multiple sequence alignments.
- **User Interface with static visualization**: The web interface is functional but basic. There is limited visualization for sequence alignments, motif occurrences, and other analytical results. Using CSS and JavaScript we could upgrade the web interface with interactive visualizations (e.g., graphical displays of alignments) to facilitate data interpretation.

### Lessons we learned during the project:
- **Modular Design is Crucial**: Organizing the application into distinct classes helped maintain the code easier to manage, understand and extend.
- **User-Centered Design Considerations**: the need for an intuitive interface and clear visualizations was clear from the beginning. 
- **Scalability role**: Handling large biological datasets is challenging. The project reinforced the importance of planning things with scalability and abstraction, both in terms of data processing and user queries.
