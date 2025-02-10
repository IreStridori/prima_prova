### **1. Introduction**
The project focuses on the development of a software which is capable for reading and structuring a FASTA files and perform different analysis on mitochondrial DNA sequences from multiple species, while providing to the user an interacting Web Interface.
 **Scalabilità** (possibilità di espandere il sistema in futuro).  
 
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

### 3.6 Class SequenceMotif (Concrete Subclass of MotifAnalyser) da finire  
   - **Purpose**: extends `MotifAnalyser` and is responsible for identifying recurring sequence motifs within genomic data. It allows the use to insert as input a precise or not subsequence and it extracts it. It also provides insights into their frequency and occurrence across multiple sequences.  
   - **Methods**: 2 different ways of analysing a motif were implemented.

     - `extract_motifs(self, sequence, motif_length, minimum)`: if the user does not have a specific motif to search this method extracts all possible subsequences of length `motif_length` from the given `sequence` which repeats at least `minimum` times. Uses `Counter()` from the `collections` module to count motif occurrences. Returns a Pandas DataFrame with motif counts.
     - `find_motif(self, motif)`: if the user has a specific genetic motif, this searches the subsequence across all sequences in the dataset. Returns a Pandas DataFrame listing:
		- **Motif** searched 
 		- Sequence **Identifier**   
		- **Number of occurrences** per sequence  

#### **Key Features**  
🔹 Efficient motif detection using **sliding window extraction** and **frequency filtering**.  
🔹 Helps identify **recurrent genetic patterns** that may be biologically significant.  


### 3.7 Sequence Alignment (`SequenceAlignment` Class) da fare  
#### **Purpose**  
The `SequenceAlignment` class handles **pairwise sequence alignment** using Biopython’s `PairwiseAligner`. It compares two sequences and calculates their similarity, producing a structured alignment output.  

#### **Methods**  
✅ **`__init__(self, seq1, seq2)`**  
- Initializes the alignment object with **two input sequences (`seq1`, `seq2`)**.  
- Uses **global alignment mode** by default.  

✅ **`perform_alignment(self)`**  
- Executes **pairwise sequence alignment** using `PairwiseAligner`.  
- Stores the alignment results.  

✅ **`format_alignment(self, n=1)`**  
- Formats and returns the **top `n` alignments** in a readable string format.  
- Helps visualize how sequences match or differ.  

✅ **`alignments_score(self)`**  
- Returns the **alignment score**, a numerical measure of sequence similarity.  
- Higher scores indicate greater similarity.  

#### **Key Features**  
🔹 Uses **Biopython’s PairwiseAligner** for robust sequence comparison.  
🔹 Supports **global sequence alignment** to find the best possible match.  
🔹 Provides **alignment scoring** to quantify sequence similarity.  

---
## 4 parte di flask e html
scrivi cosa fanno
---
### 5. Procedure di installazione e configurazione e esempio pratico 

---

## 6. Conclusioni Limiti attuali del sistema, Possibili miglioramenti e sviluppi futuri, Lezioni apprese durante il progetto.  

