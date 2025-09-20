```mermaid
graph LR
  %% ============ CATALOG ============
  subgraph Catalog
    Item[Item]
    Category[Category]
    DefaultPrice[Default Price]
    SpecialPrice[Special Price]
    Gallery[Item Image/Gallery]

    Item -- "classified in" --> Category
    Item -- "has" --> DefaultPrice
    Item -- "may have" --> SpecialPrice
    Item -- "has images" --> Gallery
  end

  %% ============ SUPPLY / INVENTORY ============
  subgraph SupplyChain["Supply & Inventory"]
    Vendor[Vendor]
    PO[Purchase Order]
    GI[Goods Inward]
    Inv[Inventory]
    Loc[Location]
    WH[Warehouse]
    FBA["Fulfillment Center (FBA/WFS)"]

    Vendor -- "creates" --> PO
    PO -- "delivers" --> GI
    GI -- "updates stock" --> Inv
    Inv -- "has" --> Item
    Item -- "tracked in" --> Inv

    Inv -- "at" --> Loc
    Loc -- "is a" --> WH
    Loc -- "is a" --> FBA
  end

  %% ============ MARKETPLACE ============
  subgraph Marketplace
    Channel["Channel / Marketplace (Amazon, Walmart, etc.)"]
    Listing["Listing / Catalog Mapping (SKU↔ASIN/WM#)"]
    Offer["Offer / Price & Availability"]
    Repricer["Pricing Rules / Repricer"]
    Promo["Promotion / Coupon"]
    Fulf["Fulfillment Method (FBA, WFS, FBM)"]

    Item -- "listed as" --> Listing
    Listing -- "on" --> Channel
    Listing -- "has" --> Offer
    Offer -- "managed by" --> Repricer
    Offer -- "may have" --> Promo
    Offer -- "fulfillment" --> Fulf

    Inv -- "feeds availability to" --> Offer
  end

  %% ============ ORDERS ============
  subgraph Orders
    MktOrder["Marketplace Order"]
    MktOrderItem["Marketplace Order Item"]
    Shipment[Shipment]
    Carrier[Carrier/Tracking]
    Return["Return / RMA"]

    Channel -- "creates" --> MktOrder
    MktOrder -- "contains" --> MktOrderItem
    MktOrderItem -- "for listing" --> Listing
    MktOrder -- "ships via" --> Shipment
    Shipment -- "carrier" --> Carrier
    MktOrder -- "may have" --> Return
    Return -- "for item" --> MktOrderItem

    MktOrderItem -- "allocates/decrements" --> Inv
  end

  %% ============ FINANCE ============
  subgraph Finance
    Fee["Marketplace Fee"]
    Settlement["Settlement / Payout"]
    Tax[Tax]
    Payment["Customer Payment"]
    Invoice[Invoice]
    InvoiceItem[Invoice Item]
    Customer[Customer]

    MktOrder -- "taxes" --> Tax
    Channel -- "charges" --> Fee
    Channel -- "pays" --> Settlement
    Settlement -- "net of" --> Fee
    MktOrder -- "payment" --> Payment

    Inv -- "sells" --> Invoice
    Invoice -- "contains" --> InvoiceItem
    Invoice -- "billed to" --> Customer
    Customer -- "purchases" --> Invoice
  end

  %% ============ ACCOUNTING & AUDIT ============
  subgraph AccountingAudit["Accounting & Audit"]
    Ledger["General Ledger"]
    Journal["Journal Entries"]
    Account["Chart of Accounts"]
    Recon["Reconciliation (Bank/Settlement)"]
    AuditTrail["Audit Trail"]
    AuditLog["Audit Log Events"]

    %% Relations
    Settlement -- "posted to" --> Ledger
    Invoice -- "posted to" --> Ledger
    Fee -- "posted to" --> Ledger
    Tax -- "posted to" --> Ledger
    Payment -- "posted to" --> Ledger

    Ledger -- "made of" --> Journal
    Journal -- "uses" --> Account

    Ledger -- "supports" --> Recon
    Ledger -- "tracked by" --> AuditTrail
    AuditTrail -- "records events in" --> AuditLog
  end



