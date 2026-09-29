flowchart TD
    A([Schedule change received]) --> B[Identify changed event]

    B --> C[Load household context]
    C --> C1[Family member schedules]
    C --> C2[Travel and arrival times]
    C --> C3[School and work constraints]
    C --> C4[Chores and household rules]
    C --> C5[Family priorities]

    C1 --> D[Build updated household timeline]
    C2 --> D
    C3 --> D
    C4 --> D
    C5 --> D

    D --> E[Identify affected people and events]
    E --> F{Is the existing plan still feasible?}

    F -- Yes --> G[Update proposed daily plan]
    G --> N[Generate family briefing]

    F -- No --> H[Explain the conflict]
    H --> I[Generate feasible alternatives]

    I --> J[Validate each alternative]
    J --> J1[No schedule overlap]
    J --> J2[Enough travel time]
    J --> J3[Required adult available]
    J --> J4[Hard commitments protected]

    J1 --> K[Rank feasible alternatives]
    J2 --> K
    J3 --> K
    J4 --> K

    K --> K1[Minimise disruption]
    K --> K2[Protect important commitments]
    K --> K3[Reduce waiting and extra travel]
    K --> K4[Preserve family time]

    K1 --> L[Recommend best plan]
    K2 --> L
    K3 --> L
    K4 --> L

    L --> M{User approves?}
    M -- No --> O[Capture preference or new constraint]
    O --> I

    M -- Yes --> P[Confirm proposed changes]
    P --> Q[Optionally update test calendars]
    Q --> N

    N --> R([Tomorrow's coordinated family plan])