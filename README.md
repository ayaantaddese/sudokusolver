# Sudoku Solver

**An intelligent Sudoku assistant combining computer vision, machine learning, and logical reasoning to help users solve puzzles step by step.**

## Overview

Sudoku Solver is a full-stack web application designed to help users work through difficult Sudoku puzzles without simply revealing the answer.

Users can upload a screenshot of a Sudoku puzzle, review the automatically recognized grid, and receive personalized feedback on their progress. The application identifies incorrect entries, explains logical deductions, and highlights the next step using established Sudoku-solving techniques.

Originally developed for my IB Computer Science Internal Assessment, the project was built around a specific problem: traditional Sudoku tools often force users to choose between solving a puzzle entirely independently or revealing its complete solution. Sudoku Solver offers a middle ground by helping users understand *why* a move is correct.

## Key Features

### 1. Computer Vision & OCR

The application uses a hybrid optical character recognition pipeline to convert uploaded Sudoku images into interactive digital boards.

- **Grid detection:** OpenCV identifies the Sudoku grid, corrects perspective distortion, and separates it into 81 individual cells.
- **Digit classification:** A hybrid recognition system uses Tesseract OCR for printed digits and a trained multilayer perceptron (MLP) for handwritten entries.
- **Given vs. user detection:** HSV color analysis distinguishes original puzzle clues from user-entered numbers.
- **Manual verification:** An editable review interface allows users to correct OCR errors before analysis.

The handwritten digit classifier uses Histogram of Oriented Gradients (HOG) features and test-time augmentation to improve recognition.

### 2. Human-Style Logical Solver

Rather than relying exclusively on brute-force search, the application implements six established Sudoku-solving techniques:

- Naked Singles
- Hidden Singles
- Naked Pairs
- Hidden Pairs
- Pointing Pairs
- Box-Line Reduction

The solver applies these techniques iteratively, restarting from simpler deductions whenever progress is made. This allows it to follow a logical solving process similar to how a human approaches Sudoku.

### 3. Intelligent Hint Engine

The hint engine examines the current board and identifies the simplest available logical deduction.

Each hint includes the relevant solving technique, target cell, and an explanation of the reasoning involved.

The interface uses color-coded highlighting to draw attention to relevant rows, columns, boxes, and candidate eliminations.

Unlike a conventional solver, the hint system is designed to guide users toward the next step without automatically completing the puzzle.

### 4. Mistake Detection & Explanation

The application checks user-entered digits against Sudoku constraints and the solution computed from the original clues.

It can identify:

- Duplicate digits within rows, columns, or boxes.
- Incorrect entries that do not immediately violate a Sudoku rule.
- Accidental modifications to original puzzle clues.
- Invalid or unsolvable puzzle configurations.

Where possible, the application provides logical explanations for detected mistakes rather than simply identifying incorrect cells.

### 5. Optimized Exact Solver

For puzzles that cannot be completed using the implemented human-style techniques, the application uses recursive backtracking.

The algorithm incorporates the **Minimum Remaining Values (MRV)** heuristic, prioritizing cells with the fewest legal candidates to reduce unnecessary search.

It also detects puzzles with no valid solution or multiple possible solutions.

### 6. Bitmask-Based Candidate Tracking

The Sudoku engine represents candidate digits using 9-bit integers rather than conventional lists.

Bitwise operations allow the engine to efficiently check, eliminate, and update possible values as constraints propagate through the board.

This representation supports both the logical solver and hint-generation system.

## Technical Architecture

The application is organized into three layers:

**Frontend → Flask REST API → Sudoku Engine / OCR Pipeline**

The frontend handles user interaction, board visualization, image uploads, and hint displays.

The Flask API manages communication between the browser and the Python backend through two main endpoints:

- `POST /ocr` — Processes an uploaded image and returns the recognized Sudoku grid.
- `POST /analyze` — Validates the board, detects mistakes, generates hints, and computes solutions.

The backend separates image processing from puzzle-solving logic, making the individual components easier to test and maintain.

## Technologies Used

| Technology | Purpose |
|---|---|
| Python | Backend and algorithm implementation |
| Flask | REST API |
| HTML, CSS, JavaScript | Interactive browser interface |
| OpenCV | Image processing and grid detection |
| Tesseract OCR | Printed digit recognition |
| scikit-learn | Handwritten digit classification using MLP |
| NumPy | Image and numerical array processing |
| HOG features | Handwritten digit feature extraction |
| Pillow | Image handling |
| joblib | Trained model serialization |

## Project Structure

```text
sudokusolver/
├── flask_api.py
├── requirements.txt
│
├── sudoku_engine/
│   ├── board.py
│   ├── solver.py
│   ├── hints.py
│   ├── reports.py
│   └── models.py
│
├── ocr/
│   ├── preprocess.py
│   ├── grid_detector.py
│   ├── cell_extractor.py
│   ├── classifier.py
│   ├── train_classifier.py
│   ├── digit_model.pkl
│   └── training_data/
│
└── sudoku_web/
    ├── index.html
    ├── app.js
    └── styles.css
```

## Getting Started

### Prerequisites

- Python 3.10+
- Tesseract OCR

On macOS, install Tesseract using Homebrew:

```bash
brew install tesseract
```

On Ubuntu/Debian:

```bash
sudo apt install tesseract-ocr
```

### Installation

**1. Clone the repository**

```bash
git clone https://github.com/American-School-of-Barcelona/sudokusolver.git
cd sudokusolver
```

**2. Install Python dependencies**

```bash
python3 -m pip install -r requirements.txt
```

### Running the Application

Start the Flask backend:

```bash
python3 flask_api.py
```

Open `sudoku_web/index.html` in your browser.

The backend runs at `http://127.0.0.1:8000` by default.

### Retraining the Handwritten Digit Classifier

If additional training samples are added, retrain the model using:

```bash
python3 -m ocr.train_classifier
```

## Testing & Evaluation

The application was developed and evaluated against 12 functional success criteria covering OCR accuracy, puzzle validation, hint generation, mistake detection, and frontend functionality.

Testing included valid and invalid puzzles, ambiguous solutions, OCR recognition errors, and end-to-end use with the intended client.

All 12 functional criteria passed their documented tests.

User feedback was positive, particularly regarding the application's ability to explain logical moves and identify mistakes. Handwritten digit recognition remained the main area for improvement, which the editable OCR review interface helps address.

## Future Improvements

- **Adjustable hint difficulty:** Allow users to select beginner, intermediate, or advanced solving techniques.
- **Hint history:** Store previous hints so users can review earlier deductions.
- **Improved handwriting recognition:** Expand the training dataset to better accommodate different handwriting styles.

## Background

This project was developed as part of my International Baccalaureate Computer Science Internal Assessment (May 2026).

It combines image processing, supervised machine learning, constraint propagation, recursive search, and explainable algorithms in a single application designed around a real user's needs.
