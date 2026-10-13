# StudyLens

StudyLens is a multimodal study app built with SwiftUI for macOS and iOS. It lets a student capture notes in different ways, save them in one place, choose what they want to study, and turn that material into a study guide using Apple's on-device Foundation Models framework.

The main workflow is:

```text
Capture material
      ↓
Save as notes
      ↓
Choose All Notes, Selected Notes, or One Note
      ↓
Generate Study Guide
      ↓
Quiz Me • Flashcards • Brain Map
```

This project was created for **CSC 395 - Advanced Swift App Development with ARKit** at Hampton University.

## Features

### Capture

StudyLens supports several ways to bring study material into the app:

- **Scan Image** with Apple Vision OCR
- **Write Note** with PencilKit and handwriting recognition
- **Speak Note** with speech-to-text
- **Typed notes** when speaking is not practical
- **File import** through the file picker or drag and drop

Supported imports include:

- TXT
- Markdown
- CSV
- PDF
- PNG, JPEG, and HEIC
- DOCX
- PPTX
- XLSX

PDF pages that do not contain readable text can fall back to Vision OCR.

### Notes

Saved notes use SwiftData and can come from:

- scanned images
- handwriting
- voice input
- typed text
- imported files
- saved Wikipedia articles

The Notes section also supports search, source filtering, sorting, editing, deleting, multi-selection, and TXT or Markdown export.

### Study Guide

The main AI feature uses Apple's **Foundation Models** framework.

StudyLens:

- checks whether the on-device model is available before generating
- sends only the notes selected by the user
- keeps the prompt grounded in the supplied notes
- limits study material to about 6,000 characters
- uses a fresh `LanguageModelSession` for each guide
- uses guided generation instead of asking the model to return JSON

The generated `StudyAnalysis` contains:

- a summary
- key concepts
- practice questions
- Brain Map data

The same generated analysis is reused by the other study tools instead of making a new model request for every screen.

### Quiz Me

Quiz Me uses the practice questions from the current Study Guide.

Answers can be typed or spoken and are graded as:

- **Correct**
- **Partially Correct**
- **Try Again**

When Foundation Models is available, StudyLens can use the on-device model for answer evaluation. A local fallback grader is available when Apple Intelligence cannot be used.

### Flashcards

Flashcards are created from the existing Study Guide.

The deck supports:

- question cards
- key concept cards
- card flipping
- Previous and Next
- Shuffle
- Restart Deck
- Know It
- Review Again
- review rounds
- session summaries
- Read Aloud and Auto Read

### Brain Map

The Brain Map turns the generated concept relationships into an interactive SwiftUI graph.

It includes:

- a central topic
- related concept nodes
- labeled relationships
- concept detail views
- native SwiftUI layout
- PNG export
- text and Markdown export

No extra Foundation Models request is made when the Brain Map opens.

### Wikipedia Learn More

Key concepts can be enriched through the MediaWiki API.

Wikipedia content stays separate from the current Study Guide unless the user explicitly chooses **Add to Notes**. Saved Wikipedia notes include article attribution and can then be selected for a future Study Guide like any other note.

The Wikipedia feature includes:

- search and article summaries
- several-meaning handling
- no-result handling
- timeout and network error states
- session caching
- duplicate-save protection

## Apple Frameworks

| Framework | Use in StudyLens |
| --- | --- |
| SwiftUI | Interface, navigation, sheets, commands, and accessibility |
| SwiftData | Persistent note storage |
| Foundation Models | Study-guide generation and model-assisted answer grading |
| Vision | OCR for scans, handwriting, images, and image-only PDF pages |
| PencilKit | Handwritten note canvas |
| Speech | Dictation and spoken quiz answers |
| AVFoundation | Microphone audio and text-to-speech |
| PDFKit | PDF text extraction and page rendering |
| Compression | Reading modern Office document archives |
| Foundation | Networking, XML parsing, and file handling |
| Uniform Type Identifiers | Import and export file-type detection |
| Core Graphics / ImageRenderer | Brain Map rendering and PNG export |
| Observation | Observable app and view-model state |

No third-party packages are required.

## Architecture

StudyLens uses a Model, View, ViewModel, and Service style structure.

Important state owners include:

- **`NotesViewModel`** for saved notes
- **`StudyViewModel`** for the selected study source, generated guide, and study state
- **`AppNavigationModel`** for tabs and navigation paths
- **`NoteStore` / SwiftData** for persistent storage

A generated guide stores a snapshot of its source notes. If one of those notes is edited or deleted, StudyLens can detect that the guide is outdated.

## Project Structure

```text
Amoo_StudyLens/
├── AgenticCodingLog.txt
├── Amoo_StudyLens.xcodeproj
└── Amoo_StudyLens/
    ├── Diagnostics/
    ├── Models/
    ├── Services/
    │   └── Import/
    ├── ViewModels/
    ├── Views/
    ├── Assets/
    ├── ContentView.swift
    └── MyApp.swift
```

The project is split so OCR, speech, Foundation Models, persistence, Wikipedia, importing, and exporting are not handled directly inside the SwiftUI views.

## Running the Project

1. Clone the repository.

   ```bash
   git clone https://github.com/SilentRequiem/StudyLens.git
   ```

2. Open:

   ```text
   Amoo_StudyLens/Amoo_StudyLens.xcodeproj
   ```

3. Choose a macOS or iOS destination in Xcode.

4. Build and run the `Amoo_StudyLens` target.

The project was built and tested with Xcode 27 for:

- My Mac on macOS 27
- iPhone 17 simulator on iOS 27

Foundation Models features require a compatible Apple Intelligence device with Apple Intelligence enabled and the on-device model ready.

## Testing and Final Audit

The final stabilization pass added a DEBUG-only `StudyLensSelfChecks` suite that uses an in-memory SwiftData store, temporary files, and mocked Wikipedia networking.

Final audit results:

- **102 / 102 logic checks passed**
- **0 build errors**
- **0 build warnings**
- clean builds on macOS and the iPhone simulator
- 13 reliability bugs found and fixed during the final audit

The checks cover note persistence, navigation, study-source resolution, outdated-guide detection, Quiz Me grading, Flashcards, Wikipedia caching, import failures, export metadata, and Brain Map behavior.

## Known Limitations

- Foundation Models requires Apple Intelligence support and availability.
- The local Quiz Me fallback does not understand language as well as model-based grading.
- Study material is trimmed to about 6,000 characters before generation.
- Generated Study Guides last for the current app session. Saved notes persist.
- Wikipedia uses English article introductions and an in-memory session cache.
- Legacy `.doc`, `.ppt`, and `.xls` files are not supported.
- Encrypted and ZIP64 Office documents are not supported.
- Very large spreadsheets are limited during import.
- Extremely large Brain Maps may be too large to render as a PNG.

## Agentic Coding

StudyLens was developed in stages through agentic coding inside Xcode. The project started with the basic SwiftUI interface, then added OCR, Foundation Models, the Brain Map, handwriting, voice tools, Flashcards, Wikipedia enrichment, navigation and import improvements, accessibility work, and a final reliability audit.

The complete prompt history and implementation notes are included in:

[`AgenticCodingLog.txt`](Amoo_StudyLens/AgenticCodingLog.txt)

## Author

**Michael Amoo**  
Cyber Security, Hampton University  
GitHub: [@SilentRequiem](https://github.com/SilentRequiem)

## Project Status

StudyLens is complete for the CSC 395 midterm submission. The current version has passed its final manual walkthrough, logic checks, and macOS/iOS build validation.
