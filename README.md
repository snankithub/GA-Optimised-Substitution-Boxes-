# GA-Optimised-Substitution-Boxes-
It's an implementation of Genetic Algorithm(Metaheuristic) in the domain of substitution-Boxes.  
### Purpose of S-Box Analysis in Symmetric Cryptography

S-Box analysis tools are critical for ensuring the strength of symmetric ciphers by evaluating the mathematical and cryptographic robustness of S-boxes. These tools analyze several properties that contribute to cipher security:

1. **Non-Linearity**: Ensures resistance against linear cryptanalysis by measuring how far the S-box deviates from linear functions.
2. **SAC (Strict Avalanche Criterion)**: Ensures that flipping one input bit changes approximately half of the output bits.
3. **BIC (Bit Independence Criterion)**: Verifies that output bits change independently of each other after an input bit flip.
4. **Differential and Linear Approximation Tables (DAT/LAT)**: Quantify probabilities of input-output correlations, assessing resistance to differential and linear cryptanalysis.
5. **Differential/Linear Probability**: Measures the likelihood of specific differentials or linear correlations; lower values imply higher security.
6. **NL-BIC/SAC-BIC Tables**: Provide integrated metrics combining non-linearity and avalanche properties with bit independence.

These analyses help design and validate cryptographically strong S-boxes for algorithms like AES. By identifying weaknesses, they ensure resilience against common attacks, enhancing encryption reliability.

Key Properties
Non-linearity:

Definition: Measures the resistance of an S-box to linear approximations.
Importance: High non-linearity makes the S-box resistant to linear cryptanalysis.
Operation: Compares the S-box output against linear functions.
Strict Avalanche Criterion (SAC):

Definition: A small change in input (flipping one bit) should cause a significant, unpredictable change in output.
Importance: Ensures the S-box introduces sufficient diffusion.
Operation: Tests output variations against bit flips.
Bit Independence Criterion (BIC):

Definition: Ensures output bits are independent of each other after an input bit flip.
Importance: Prevents correlation between output bits.
Operation: Examines cross-dependencies among output bits.
Differential Approximation Table (DAT) and Linear Approximation Table (LAT):

Definition: Evaluate resistance to differential and linear cryptanalysis, respectively.
Importance: Measure probabilities of differential and linear approximations.
Operation: Analyze transitions and correlations over input-output pairs.
Differential Probability (DP) and Linear Probability (LP):

Definition: Probability of specific differential or linear approximations.
Importance: Low DP and LP indicate high cryptographic strength.
Operation: Compute probabilities from DAT and LAT.
NL-BIC Table and SAC-BIC Table:

Definition: Combine non-linearity and SAC with BIC for comprehensive analysis.
Importance: Provide an integrated view of S-box strength.
Operation: Quantify combined properties through advanced computations.


PIPELINE OF THE IMPLEMENTATION:

=========================================
 1. INITIALIZATION & SETUP
=========================================
      │
      ├─► Initialize Hyperparameters (Pop Size, Gens, Island Count)
      ├─► Set Up Data Structures (Elite Archive, Tabu Memory)
      ├─► Generate Initial S-Boxes (Robust Seeds via AES + Random)
      └─► Distribute S-Boxes across 'N' Islands
      │
      ▼
=========================================
 2. JIT ENGINE COMPILATION (One-Time)
=========================================
      │
      ├─► Compile Fast Walsh-Hadamard Transform (FWHT)
      ├─► Compile Difference Distribution Table (DDT) evaluator
      └─► Initial Fitness Evaluation of all Islands
      │
      ▼
=========================================
 3. MAIN EVOLUTIONARY LOOP (Generations 1 to N)
=========================================
      │
      ├─► [1. MIGRATION CHECK]
      │     └─ Is it a migration generation? 
      │          ├── YES: Swap top elite S-Boxes between islands
      │          └── NO: Continue
      │
      ├─► [2. ADAPTIVE CALIBRATION]
      │     ├─ Calculate global population diversity
      │     ├─ Determine Phase (Early, Mid, Late)
      │     └─ Update current weights (NL vs. DU) and Mutation Rates
      │
      ▼
   ┌──────────────────────────────────────────────────────────┐
   │ 4. PER-ISLAND PROCESSING (Executes for each island)      │
   ├──────────────────────────────────────────────────────────┤
   │  │                                                       │
   │  ├─► Selection: Pick parents using NSGA-2                │
   │  │                                                       │
   │  ├─► Crossover: Diversity-Aware Order Crossover          │
   │  │     └─ (Only triggers if parents are diverse enough)  │
   │  │                                                       │
   │  ├─► Mutation: Phase-Targeted                            │
   │  │     ├─ Exploration Phase: Aggressive multi-bit flips  │
   │  │     └─ Exploitation Phase: Hunt & swap weak bits      │
   │  │                                                       │
   │  ├─► Constraint Check: Repair Bijection                  │
   │  │     └─ (Fix duplicates to ensure perfect 8-bit map)   │
   │  │                                                       │
   │  ├─► Memetic Step: Intense Local Search                  │
   │  │     └─ (Hill-climb the top 20 offspring for max NL)   │
   │  │                                                       │
   │  ├─► Evaluation: Run JIT Engines on new offspring        │
   │  │     └─ Fitness = (Max NL, Min DU, Min Weak Count)     │
   │  │                                                       │
   │  └─► Replacement & Archiving                             │
   │        ├─ Merge parents & offspring, select best to keep │
   │        └─ Send top performers to the Global Elite Archive│
   └──────────────────────────────────────────────────────────┘
      │
      ▼
=========================================
 5. STAGNATION & RESTART LOGIC
=========================================
      │
      ├─► Did the Non-Linearity (NL) score improve recently?
      │     ├── YES: Reset plateau counter. Proceed to next gen.
      │     │
      │     └── NO: Increment plateau counter.
      │          │
      │          └─ Has it been 25 generations without improvement?
      │               ├── YES: TRIGGER ADAPTIVE RESTART
      │               │     ├─ Add current top 10% to Tabu List
      │               │     ├─ Keep top 10% alive
      │               │     └─ Overwrite 90% with new seeds/randoms
      │               │
      │               └── NO: Continue to next gen.
      │
      ▼
[ LOOP RETURNS TO STEP 3 UNTIL MAX GENERATIONS REACHED ]
      │
      ▼
=========================================
 6. FINAL EXTRACTION
=========================================
      │
      ├─► Extract "Last Generation Champion" (from active islands)
      ├─► Extract "All-Time Best" (from the Elite Archive)
      └─► Output S-Box Matrices and final NL/DU scores
