### **1. Introduction**
The project focuses on the development of a software which is capable of reading and structuring a FASTA file and perform different analysis on mitochondrial DNA sequences from multiple species, while providing to the user an interacting Web Interface. The project also relies on scalability, so that the possibility to expand it in the future is concrete.
 
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
## 4 parte di flask e html
scrivi cosa fanno

---
### 5. Procedure di installazione e configurazione e esempio pratico 
Run the Flask App
Start the application by running: python app.py
The app will be accessible at http://127.0.0.1:5000/.

---

## 6. Conclusioni Limiti attuali del sistema, Possibili miglioramenti e sviluppi futuri, Lezioni apprese durante il progetto.  
Current Limitations of the System:
File Format Restrictions:
The application only supports uploading files in FASTA format, limiting its usability for datasets stored in other common bioinformatics formats (e.g., GenBank or EMBL).

Basic Error Handling and Validation:
Error handling is minimal. For example, the system may not adequately handle malformed FASTA files or unexpected user inputs, which can lead to uninformative error messages or crashes.

Session-Based File Storage:
The use of Flask sessions to store the file path is simple but not ideal for multi-user environments or larger datasets. This approach can lead to scalability issues and potential data conflicts when multiple users access the system concurrently.

Limited Analysis Capabilities:
The functionality is focused on basic sequence statistics (length and GC content), motif search, and pairwise alignment. More advanced analyses, such as phylogenetic tree construction or structural predictions, are not supported.

User Interface and Visualization:
The web interface is functional but basic. There is limited visualization for sequence alignments, motif occurrences, and other analytical results, which might hinder usability for more in-depth analyses.

Possible Improvements and Future Developments:
Expanded File Format Support:
Incorporate additional file formats (e.g., GenBank, EMBL) to make the tool more versatile for different types of sequence data.

Enhanced Error Handling and User Feedback:
Implement more robust validation and error reporting mechanisms to provide users with clear feedback when something goes wrong (e.g., detailed error messages for invalid input or parsing failures).

Scalability Enhancements:
Transition from session-based storage to a more robust data management system, such as a database, to support multi-user access and large datasets. This change would improve performance and reliability.

Advanced Analytical Features:
Expand the analytical capabilities by integrating additional bioinformatics tools and algorithms. For instance, adding functionality for BLAST searches, multiple sequence alignments, or phylogenetic analysis could greatly enhance the system's utility.

Improved User Interface and Visualization:
Upgrade the web interface with interactive visualizations (e.g., graphical displays of alignments, motif maps, or sequence composition charts) to improve user experience and facilitate data interpretation.

API Integration and Extensibility:
Consider developing RESTful API endpoints to allow integration with other bioinformatics tools and pipelines, enabling programmatic access to the analysis functions.

Lessons Learned During the Project:
Modular Design is Crucial:
Organizing the application into distinct classes (for FASTA parsing, sequence statistics, motif analysis, and alignment) helped maintain a clear separation of concerns and made the code easier to manage and extend.

Balancing Simplicity and Functionality:
While a simple Flask-based web application is great for rapid prototyping, building a robust and scalable system requires careful planning around data management, error handling, and user session management.

Importance of Robust Error Handling:
Implementing comprehensive error checking early on can save time in debugging and significantly improve the user experience by providing meaningful feedback.

User-Centered Design Considerations:
Early user feedback highlighted the need for a more intuitive interface and clearer visualizations. Focusing on usability from the beginning can guide the development of more effective bioinformatics tools.

Scalability and Performance:
Handling large biological datasets is challenging. The project reinforced the importance of planning for scalability, both in terms of data processing and user concurrency, even in early-stage prototypes.
