### **1. Introduction**
The project focuses on the development of a software which is capable for reading and structuring a FASTA files and perform different analysis on mitochondrial DNA sequences from multiple species, while providing to the user an interacting Web Interface.

### **Technologies used**
- Programming Language: Python (Pandas, Biopython and Flask libraries used)
- Backend: Flask
- Frontend: HTML

---

## **2. System design**
### **System architecture and interactions**
UML e crc 

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

## **3. Implementation: classes used and their responsibilities**
### 1. **Class FileParser (Abstract Superclass)**  
- **Purpose**: Provides an reusable structure for parsing different types of files. The only method it has is `parse(self, file_path)`, an abstract method for reading and processing data from a file path. The superclass ensures a that all subclasses implement the parse method.  

### 2. **Class FastaParser (Concrete Subclass of DataParser)**  
- **Purpose**: Implements specific parsing for FASTA files and organizes the files's genomic data in a structured table with an identifier, a description and an associated sequence. It implements the `parse(self, file_path)` method from FileParser.
  
- **Specific responsibilities**:
	- Reads a FASTA file line by line.
 	- Organizes the different file's objects into a dictionary with three keys:  
		- **ID**: The sequence identifier (e.g., _NC_012920.1_).  
		- **Description**: The description of the organism (e.g., _Homo sapiens mitochondrion, complete genome_).
		- **Sequence**: The nucleotide sequence itself (e.g., _GATCACAGGT..._).  
	- Converts the dictionary into a Pandas DataFrame for better organization and management.  

- **Error management**: The class uses an `ensure_data_loaded` decorator which verifies if a file was uploaded by the user. In case it was not, it raises an error, informs the user with a specific message and halts the session.

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
  
### 3. **Class GenomicEntity (Concrete Superclass)**  
- **Purpose**: Models a generic genomic sequence by setting common attributes (identifier, description, sequence) and provides a common method for sequence length calculation.

### 4. **Class MitochondrialDNA (Subclass of GenomicEntity)**  
   - **Purpose**: Extends `GenomicEntity` to add specific functionalities for DNA sequences.  
   - **Specific responsibilities**:
     	- Extract a subsequence by putting a start and an end index as inputs
     	- GC content analysis of a sequence

### 5. **Class MotifAnalyser (Abstract Class)** 
- **Purpose**: Provides an reusable structure for working with different types of data and inserting different types of inputs (e.g., I could give as motif a tuple, a list, a string...). Defines the structure to work with (`data`) and an abstract method `find_motif(self, motif)` for serching a motif in a certain sequence. The superclass ensures a that all subclasses implement the find_motif method.
  
### **7. Class SequenceMotif (Concrete Subclass of MotifAnalyser)**  
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

---

### **8. Sequence Alignment (`SequenceAlignment` Class)**  
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



### 7. **Class SequenceAligner**  
   - **Purpose**: Performs alignment between two sequences using Biopython’s `pairwise2` module.  
   - **Attributes**:  
     - `seq1` and `seq2`: The two sequences to be aligned.  
   - **Methods**:  
     - `align(self)`: Performs a **global alignment** (`globalxx`) between the two sequences, comparing each base and finding the best possible match:  
       1. Generates a list of alignments with scores and visual representations.  
       2. Returns these alignments. The best result or all results can be displayed.

---
### **3.3 Integrazione e Deployment** - **Procedure di installazione e configurazione**.  

---

## **5. Performance e Ottimizzazione**
- **Metriche di performance** (tempo di risposta, uso di risorse).  
- **Ottimizzazioni fatte** (es. caching, indexing, query ottimizzate).  
- **Scalabilità** (possibilità di espandere il sistema in futuro).  

---

## **6. Conclusioni e Lavori Futuri**
- **Limiti attuali del sistema**.  
- **Possibili miglioramenti e sviluppi futuri**.  
- **Lezioni apprese durante il progetto**.  

---

## **Appendici (opzionale)**
- Codice di esempio per parti chiave.  
- Link a documentazione tecnica.  
- Guide rapide per sviluppatori futuri.  
Esempio pratico delle interazioni
	1.	Parsing:
	•	Usa FastaParser per leggere un file FASTA e ottenere un DataFrame.
	2.	Analisi di sequenze:
	•	Crea un oggetto DNASequence con una delle sequenze parse e calcola la lunghezza, il GC content e il complemento inverso.
	3.	Ricerca di motivi:
	•	Usa MotifAnalyser per cercare un motivo specifico in tutte le sequenze del file FASTA.
	4.	Allineamento:
	•	Allinea due sequenze usando SequenceAligner e ottieni un punteggio e una rappresentazione visiva del miglior allineamento.

Cosa include la Parte 4
	1.	Pagina principale (/):
	•	Permette di caricare un file FASTA tramite un modulo HTML.
	2.	Statistiche delle sequenze (/stats):
	•	Mostra lunghezza e GC content per ogni sequenza nel file caricato.
	3.	Ricerca motivi (/motif):
	•	Consente di inserire un motivo (es. “GATC”) e visualizzare dove si trova nelle sequenze.
	4.	Allineamento delle sequenze (/align):
	•	Permette di selezionare due sequenze per eseguire un allineamento e visualizzare i risultati

