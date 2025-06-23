graph TD
    subgraph Customer
        C[Customer]
    end

    subgraph Host Bank (e.g., Maybank, CIMB)
        subgraph Channels
            B(Branch - RM/Teller)
            M(Mobile Banking App)
            W(Internet Banking)
        end
        
        subgraph Application Layer
            CRM(Customer Relationship Management - CRM)
            CBS(Core Banking System<br>Accounts, Loans, Deposits)
            DFE(Digital Front-End<br>Powers App/Web)
        end

        subgraph Integration Layer
            APIG_H(API Gateway)
            ESB(Enterprise Service Bus / Middleware)
        end

        subgraph Data Layer
            CMD(Customer Master Data<br><i>Single Source of Truth</i>)
            TD(Transactional Data)
        end
        
        subgraph Shared Services
            IAM(Identity & Access Mgmt<br><i>Single Sign-On</i>)
            CR(Compliance & Reporting<br><i>BNM, SC Reporting</i>)
        end
    end

    subgraph Third-Party Provider (e.g., Sun Life, LPL, AIA)
        subgraph Application Layer
            SP(Specialized Platform<br><i>Insurance/Investment/Mortgage</i>)
            AP(Advisor Portal / Workbench)
            PC(Product Catalog)
        end

        subgraph Integration Layer
            APIG_T(Partner API Gateway)
        end

        subgraph Data Layer
            PIDB(Policy / Investment DB)
            CSD(Customer Subset Data)
        end
        
        subgraph Shared Services
            CSE(Commission & Settlement Engine)
        end
    end

    %% --- Customer Interactions ---
    C -- Interacts with --> M & W
    C -- Visits --> B

    %% --- Internal Host Bank Flows ---
    M & W -- Powered by --> DFE
    B -- Uses --> CRM & CBS
    DFE -- Uses --> CRM & CBS
    CRM -- Identifies Leads from --> CBS & CMD
    
    %% --- The Core "Bank in a Bank" Integration Flows ---
    DFE -- 1. Service Discovery/Display --> APIG_H
    CRM -- 2. Lead/Referral --> APIG_H
    APIG_H -- 3. Secure API Call --> APIG_T
    APIG_T -- Routes to --> SP & AP
    
    %% --- Data Synchronization Flow ---
    CMD -- 4. Secure Data Sync --> CSD
    
    %% --- Unified View Flow ---
    SP -- 5. Data for Unified View (API) --> APIG_T
    APIG_T -- via APIG_H --> DFE
    
    %% --- Financial Settlement Flow ---
    CSE -- 6. Revenue Share / Commission --> CBS

    %% --- Advisor Interaction ---
    B -- Advisor uses --> AP

    %% Style Definitions
    classDef host fill:#e3f2fd,stroke:#333,stroke-width:2px;
    classDef thirdparty fill:#e8f5e9,stroke:#333,stroke-width:2px;
    classDef customer fill:#fffde7,stroke:#333,stroke-width:2px;
    
    class C customer;
    class B,M,W,CRM,CBS,DFE,APIG_H,ESB,CMD,TD,IAM,CR host;
    class SP,AP,PC,APIG_T,PIDB,CSD,CSE thirdparty;
