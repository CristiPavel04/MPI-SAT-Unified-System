# SAT Unified System

**Overview**

This repository hosts a unified Boolean Satisfiability (SAT) solving system. It integrates and provides implementations for several fundamental SAT-solving algorithms, including DPLL, Davis-Putnam, and a Resolution-based approach.

## Table of Contents

* [Features](#features)
* [Prerequisites](#prerequisites)
* [Installation](#installation)
* [Building from Source](#building-from-source)
* [Usage](#usage)
* [Testing](#testing)
* [Input Format](#input-format)
* [Project Structure](#project-structure)
* [Algorithms](#algorithms)
* [Contributing](#contributing)
* [License](#license)

## Features

* **Multiple SAT Solving Algorithms:**
    * DPLL (Davis-Putnam-Logemann-Loveland)
    * Davis-Putnam
    * Resolution-based solver
* Performance measurement and timing for each solver.
* Support for DIMACS CNF (Conjunctive Normal Form) file format for problem input.
* Interactive console interface for single-file testing.

## Prerequisites

To build and run this project, you will need:

* A C++ compiler with C++11 support (e.g., GCC, Clang, MSVC).
* CMake (version 3.10 or higher) for build system generation.
* Make or an equivalent build system (e.g., Ninja, NMake).

## Installation

1.  **Clone the repository:**
    ```bash
    git clone [https://github.com/CristiPavel04/MPI-SAT-Unified-System.git](https://github.com/CristiPavel04/MPI-SAT-Unified-System.git)
    cd MPI-SAT-Unified-System
    ```
    *(Please ensure `https://github.com/CristiPavel04/MPI-SAT-Unified-System.git` is the correct repository URL. Update if different.)*

2.  Open the project in your preferred Integrated Development Environment (IDE) such as CLion, Visual Studio, or VS Code.

## Building from Source

After cloning the repository:

1.  Navigate into the cloned directory if you haven't already:
    ```bash
    cd MPI-SAT-Unified-System
    ```
2.  Create a build directory (if it doesn't exist) and navigate into it:
    ```bash
    mkdir build
    cd build
    ```
3.  Configure the project with CMake and build the executables:
    ```bash
    cmake ..
    make
    ```
    *(Note: For Visual Studio users, `cmake ..` will generate a `.sln` file in the `build` directory. Open this solution file and build from within Visual Studio.)*

## Usage

After successful compilation, the executables will be located within the `build/Functional/` directory.

### Interactive Single File Solving (`main.cpp`)

This mode allows you to test individual DIMACS CNF files interactively via the console.

1.  Execute the main program from your `build` directory:
    ```bash
    ./Functional/main
    ```
    *(Path might vary slightly based on your operating system, e.g., `build\Functional\main.exe` on Windows.)*
2.  Follow the on-screen prompts to:
    * Select a SAT solver (1: DPLL, 2: Davis-Putnam, 3: Resolution).
    * Enter the **absolute file path** to your DIMACS CNF input file.
3.  The program will then display:
    * The satisfiability result (SAT or UNSAT).
    * The solving time in milliseconds.

## Testing

The project includes dedicated C++ test files within `Functional/Tests/` for performing batch testing and evaluating solver performance on multiple instances.

### Batch Testing (`Functional/Tests/[solver]test.cpp`)

This mode is ideal for processing a set of DIMACS CNF files and collecting performance statistics.

1.  Open the desired test file (e.g., `dplltest.cpp`, `dptest.cpp`, or `restest.cpp`) located in `Functional/Tests/`.
2.  Locate the `std::vector<std::string> test_files` (or similar) and modify it to include the **absolute paths** of the DIMACS CNF files you want to test.
3.  Compile and run the specific test executable from your `build` directory. For example:
    ```bash
    ./Functional/Tests/dplltest
    ```
4.  The program will:
    * Process all files specified in the `test_files` vector.
    * Display full solver output for each file directly to the console.
    * Show overall runtime statistics for the batch.
    * Write detailed results (filename and solving time in milliseconds) to a dedicated file named `[solver]results.txt` (e.g., `dpllresults.txt`) in the directory where the test executable was run from.

## Input Format

This system accepts SAT problems in the **DIMACS CNF (Conjunctive Normal Form)** file format.

A standard DIMACS CNF file has the following structure:

* Lines starting with `c` are comments and are ignored.
* A "problem line" starts with `p cnf`, followed by the number of variables and the number of clauses.
* Subsequent lines represent clauses, where each number is a literal. Positive numbers indicate a positive literal (e.g., `1` for variable `x1`), and negative numbers indicate a negated literal (e.g., `-1` for `NOT x1`).
* Each clause must end with a `0`.

**Example:**

c This is a comment
p cnf 3 2
1 2 0
-1 3 0

This example represents the formula `(x1 OR x2) AND (NOT x1 OR x3)`. Please ensure your input files strictly adhere to this format.

## Project Structure

.
├── Functional/           # Main source code directory
│   ├── include/         # Header files
│   ├── DIMACS/         # DIMACS format related files
│   ├── Tests/          # Test cases
│   └── main.cpp        # Main program entry point
├── build/              # Build directory for compiled executables
└── CMakeLists.txt     # CMake configuration file for building the project


## Algorithms

This system implements the following core SAT-solving algorithms:

### 1. DPLL (Davis-Putnam-Logemann-Loveland) Algorithm

A complete, backtracking-based search algorithm for determining the satisfiability of propositional logic formulae in conjunctive normal form (CNF).

**Key Functions:**
* `unitPropagation()`: Identifies and assigns values to unit clauses (clauses with a single unassigned literal).
* `pureLiteralElimination()`: Identifies and assigns values to pure literals (literals appearing only positively or only negatively across all clauses).
* `chooseLiteral()`: Selects the next literal for branching when neither unit propagation nor pure literal elimination applies.
* `backtrack()`: Manages the backtracking process when a conflict (contradiction) is encountered, reverting assignments and exploring alternative paths.
* `solve()`: The main function orchestrating the DPLL search process, recursively calling itself to explore the search space.

### 2. Davis-Putnam Algorithm

An older, resolution-based algorithm that systematically eliminates variables from the formula until satisfiability or unsatisfiability is determined.

**Key Functions:**
* `eliminateVariable()`: Removes a specific variable from the formula by applying the resolution rule to all clauses containing that variable and its negation.
* `resolve()`: Performs the resolution inference between two given clauses, producing a new clause.
* `findPureLiteral()`: Identifies pure literals for efficient elimination, as their assignment won't create conflicts.
* `simplify()`: Simplifies the formula after each variable elimination step by removing tautological clauses or subsumed clauses.
* `solve()`: The main function managing the iterative variable elimination process.

### 3. Resolution-based Solver

This solver repeatedly applies the resolution inference rule to derive new clauses. The process continues until either an empty clause (representing a contradiction and thus UNSAT) is derived, or no new clauses can be generated (indicating SAT).

**Key Functions:**
* `resolveClauses()`: Performs resolution between two given clauses, generating their resolvent.
* `addNewClause()`: Adds newly derived clauses to the current set of clauses, avoiding duplicates.
* `checkContradiction()`: Periodically checks if an empty clause has been derived, signifying unsatisfiability.
* `simplifyClauses()`: Removes redundant or subsumed clauses from the formula to keep it concise and improve performance.
* `solve()`: The main function managing the iterative resolution process, selecting clause pairs and applying resolution until a conclusion is reached.

**Common Features Across All Solvers:**
* Robust DIMACS CNF file parsing to correctly interpret problem instances.
* Precise performance measurement for solving time, typically in milliseconds.
* Result verification mechanisms to ensure the correctness of the generated assignment (for SAT cases).
* Optimized memory management for efficiently handling potentially large SAT instances.

## Contributing

We welcome contributions! If you find bugs, have feature requests, or would like to contribute code, please refer to our [Contributing Guidelines](CONTRIBUTING.md) (or add specific instructions here, e.g., how to open an issue, submit a pull request with your changes, etc.).

## License

This project is licensed under the MIT License - see the `LICENSE` file for full details.
