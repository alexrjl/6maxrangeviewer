# **6maxcompressed.json Format Documentation**

## **Overview**

The `6maxcompressed.json` file contains compressed poker hand range data for 6-max Pot Limit Omaha (PLO) preflop strategies. The compression groups hands with identical ranks but different suits, reducing the file size by approximately 99% while preserving all strategic information.

## **File Structure**

The JSON file has three main sections:

{  
  "hands": { ... },  
  "patterns": { ... },  
  "info": { ... }  
}

### **1\. "hands" Section**

This is the main data section containing all compressed hand ranges. The structure is:

"hands": {  
  "AAAK": {  
    "1": { action\_data },  
    "2": { action\_data },  
    ...  
  },  
  "AAA9": { ... },  
  ...  
}

**Key Structure:**

* **First level**: Rank combinations (e.g., "AAAK", "KQJ9") \- ranks sorted from highest to lowest  
* **Second level**: Pattern IDs (1-7) representing different suitedness patterns  
* **Third level**: Action data for each situation and position

### **2\. "patterns" Section**

Maps pattern IDs to their descriptive names:

"patterns": {  
  "1": "mono",        // All four cards same suit (e.g., AsKsQsJs)  
  "2": "trip\_high",   // Three suited, highest card is suited  
  "3": "trip\_low",    // Three suited, highest card is not suited  
  "4": "double",      // Two pairs of suits (e.g., AsAhKsKh)  
  "5": "single\_high", // One pair suited, highest card is suited  
  "6": "single\_low",  // One pair suited, highest card is not suited  
  "7": "rainbow"      // All four different suits  
}

### **3\. "info" Section**

Contains metadata about the compression:

"info": {  
  "original\_combinations": 178721,    // Number of specific hands before compression  
  "simplified\_combinations": 1613,    // Number of rank groups after compression  
  "compression\_ratio": "0.90%",       // Size reduction achieved  
  "positions": \["BTN", "CO", "MP", "SB", "UTG", "vsBtn", "vsCO", "vsMP", "vsSB", "vsUTG"\],  
  "situations": \["BB 3bet vs 1", "BB call vs 1", "BB fold vs 1", ...\]  
}

## **Action Data Format**

Within each rank/pattern combination, the action data is organized by situation and position:

"AAAK": {  
  "1": {  
    "open": {  
      "UTG": 100.0,  
      "MP": 100.0,  
      "CO": 100.0,  
      "BTN": 100.0,  
      "SB": 100.0  
    },  
    "BB 3bet vs 1": {  
      "vsUTG": 85.5,  
      "vsMP": 92.0,  
      ...  
    },  
    "BB call vs 1": {  
      "vsUTG": 14.5,  
      "vsMP": 8.0,  
      ...  
    }  
  }  
}

**Important Notes:**

* All frequencies are percentages (0-100)  
* For each position, all possible actions sum to exactly 100%  
* Missing entries indicate 0% frequency

## **Understanding Positions**

**Opening positions:**

* UTG: Under the gun (first to act)  
* MP: Middle position  
* CO: Cutoff  
* BTN/Btn: Button  
* SB: Small blind

**Facing a raise positions:**

* vsUTG: When facing a raise from UTG  
* vsMP: When facing a raise from MP  
* vsCO: When facing a raise from CO  
* vsBtn/vsBTN: When facing a raise from Button  
* vsSB: When facing a raise from SB (only BB can be in this position)

## **Action Types**

**Opening actions:**

* "open": Raise when first to enter the pot  
* "openfold": Fold when first to act

**Vs 1 actions (facing a single raise):**

* "Btn call vs 1": Button calls a raise  
* "Btn 3bet vs 1": Button re-raises  
* "Btn fold vs 1": Button folds  
* Similar patterns for SB and BB positions

## **How to Use This Data**

### **Example 1: Finding open frequency for AsKhQdJc from MP**

1. Get canonical ranks: AKQJ  
2. Determine pattern: single\_high (pattern ID "5")  
3. Look up: `hands["AKQJ"]["5"]["open"]["MP"]`

### **Example 2: Finding BB defense strategy vs UTG open with 9s9h7d7c**

1. Get canonical ranks: 9977  
2. Determine pattern: double (pattern ID "4")  
3. Check actions:  
   * 3bet frequency: `hands["9977"]["4"]["BB 3bet vs 1"]["vsUTG"]`  
   * Call frequency: `hands["9977"]["4"]["BB call vs 1"]["vsUTG"]`  
   * Fold frequency: `hands["9977"]["4"]["BB fold vs 1"]["vsUTG"]`

### **Example 3: Calculating frequencies for all AAAK hands**

Since AAAK has 4 different suitedness patterns with different strategic implications:

* Sum up the frequencies weighted by the number of combinations in each pattern  
* Mono-suited AAAK (4 combos) might 3bet 100%  
* Rainbow AAAK (24 combos) might 3bet 80%  
* The overall AAAK 3bet frequency would be weighted accordingly

## **Compression Details**

The compression works by:

1. Grouping all hands with the same four ranks (e.g., all AAAK hands)  
2. Further subdividing by suitedness pattern  
3. Averaging the frequencies for all specific hands in each group

This preserves the strategic differences between suited and unsuited versions of the same ranks while dramatically reducing data size.

## **Data Integrity**

* Every position/action combination sums to exactly 100%  
* All hands have complete data (including explicit fold frequencies)  
* Frequencies are averaged across all hands in each rank/pattern group  
* The data has been normalized to handle any inconsistencies in the source files

