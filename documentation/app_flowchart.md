flowchart TD
    A[Start]
    B[Load Stored Images]
    C[Display Stored Pairs]
    D[User Uploads Images]
    E[User Selects Preset]
    F[User Clicks Process]
    G[Build Image Queue]
    H[Process Next Image]
    I[Call Chutes API]
    J[Show Loading Indicator]
    K{API Success}
    L[Update State And UI]
    M[Show Error Message]
    N[Save Pair To Local Storage]
    Q{More Images In Queue}
    O[Display All Results]
    P[End]

    A --> B
    B --> C
    C --> D
    D --> E
    E --> F
    F --> G
    G --> H
    H --> I
    I --> J
    J --> K
    K -->|Yes| L
    K -->|No| M
    L --> N
    M --> N
    N --> Q
    Q -->|Yes| H
    Q -->|No| O
    O --> P