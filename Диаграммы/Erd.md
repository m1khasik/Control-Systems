erDiagram
    USER {
        int id
        string name
        string email
        string role
    }

    PROJECT {
        int id
        string name
        string description
    }

    DEFECT {
        int id
        string title
        string description
        string status
        string priority
        int projectId
        string createdBy
        datetime createdAt
    }

    USER ||--o{ DEFECT : "создаёт"
    PROJECT ||--o{ DEFECT : "содержит"
